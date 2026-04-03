# Go-eCharger Binding

This Binding controls and reads data from the [Go-eCharger](https://go-e.co/).
It is a mobile wallbox for charging EVs and has an open REST API for reading data and configuration.
The API must be activated in the Go-eCharger app.

## Supported Things

This binding supports go-e Charger HOME+ with 7.4kW, 11kW or 22kW as well as go-e Charger HOMEfix and go-e Charger Gemini with 11kW and 22kW.

## Setup

1) Install the binding
2) Activate the local HTTP API in the go-e Charger app (Settings --> Connection --> API Settings --> "Allow access to local HTTP API vX").
Please note that v1 is the default, but more functions (channels) are supported by the API v2. However, v2 has to be supported by your go-e Charger (details see below).
3) Configure the thing (see below).

## Thing Configuration

The thing has these configuration parameters:

| Parameter       | Description                                                       | Required |
|-----------------|-------------------------------------------------------------------|----------|
| ip              | The IP-address of your Go-eCharger                                | yes*     |
| serial          | The serial number of the Go-eCharger                              | yes*     |
| token           | The access token for the Go-eCharger Cloud API                    | yes*     |
| apiVersion      | The API version to use (1=default or 2)<br>(API v1 is deprecated) | no       |
| refreshInterval | Interval to read data, default 5 (in seconds)                     | no       |

The API v2 is only available for Go-eCharger with new hardware revision (CM-03).
The API v1 is deprecated.
*) Configure ip for the Local API or serial and token for Cloud API. If both are configured the local API will be used.

## Groups

The API v2 of go-e charger wallbox provides a vast set of
[API keys](https://github.com/goecharger/go-eCharger-API-v2/blob/main/apikeys-de.md).
The apiVersion 2 is only available for go-e Charger with new hardware revision (CM-03, GM-10 and potentially others), which can be recognized with the serial number on the back of the device.
and state keys a grouping scheme is now applied.

| Group               | Group ID         | Abstract          | Description                                        |
|---------------------|------------------|-------------------|----------------------------------------------------|
| control             | goeEnergyControl | Energy control    | Control options for charging with Go-eCharger      |
| states              | goeStates        | State information | Various state information provided by the wall box |
| aWATTarMarketPrices | goeMarketPrices  | Market prices     | Control and state information about market prices  |
| meter               | goeMeter         | Measurement       | Measurement API keys                               |
| other               | goeOther         | Miscellaneous     | Various other control and information keys         |
| info                | goeInfo          | Static information| Static information about the wallbox               |

*Note* The grouping is a breaking change of the binding to the previous versions.


## Channels

Currently available channels are

### Group '__control__'

All members of the group `control` are read write.

| Channel ID                       | Item Type                | Description                                                   | API key | v1| v2|
|----------------------------------|--------------------------|---------------------------------------------------------------|---------|---|---|
| control#chargingMode             | String                   | Charging mode can be immediate, market price or energy budget | lmo     |   | X |
| control#forceState               | Number                   | Force state  (Neutral=0, Off=1, On=2)                         | frc     |   | X |
| control#maxCurrent               | Number:ElectricCurrent   | Maximum current allowed to use for charging                   | amp     | X | X |
| control#maxCurrentTemp           | Number:ElectricCurrent   | Maximum current temporary (not written to EEPROM)             | amx     | X |   |
| control#maxCurrentTemp           | Number:ElectricCurrent   | Maximum current temporary                                     | tcl     |   | X |
| control#minChargingCurrent       | Number:ElectricCurrent   | Minimum Charging Current                                      | mca     |   | X |
| control#maxChargingCurrent       | Number:ElectricCurrent   | Absolute Maximum Current                                      | ama     |   | X |
| control#phaseSwitchMode          | Number                   | Phase Switch Mode (psm: Auto=0, Force_1=1, Force_3=2)         | psm     |   | X |
| control#sessionChargeEnergyLimit | Number:Energy            | Wallbox stops charging after defined value, disable with 0    | dwo     | X | X |

*Note* The mode market price can be combined with an energy limit.

### Group '__meter__'

All members of the group `meter` are read only.

| Channel ID                       | Item Type                | Description                                                   | API key | v1| v2|
|----------------------------------|--------------------------|---------------------------------------------------------------|---------|---|---|
| meter#voltageL1                  | Number:ElectricPotential | Voltage on L1                                                 | nrg[0]  | X | X |
| meter#voltageL2                  | Number:ElectricPotential | Voltage on L2                                                 | nrg[1]  | X | X |
| meter#voltageL3                  | Number:ElectricPotential | Voltage on L3                                                 | nrg[2]  | X | X |
| meter#currentL1                  | Number:ElectricCurrent   | Current on L1                                                 | nrg[4]  | X | X |
| meter#currentL2                  | Number:ElectricCurrent   | Current on L2                                                 | nrg[5]  | X | X |
| meter#currentL3                  | Number:ElectricCurrent   | Current on L3                                                 | nrg[6]  | X | X |
| meter#powerL1                    | Number:Power             | Power on L1                                                   | nrg[7]  | X | X |
| meter#powerL2                    | Number:Power             | Power on L2                                                   | nrg[8]  | X | X |
| meter#powerL3                    | Number:Power             | Power on L2                                                   | nrg[9]  | X | X |
| meter#sessionChargedEnergy       | Number:Energy            | Amount of energy that has been charged in this session        | wh      | X | X |
| meter#totalChargedEnergy         | Number:Energy            | Amount of energy that has been charged since installation     | eto     | X | X |
| meter#temperature                | Number:Temperature       | Temperature of the circuit board of the Go-eCharger           | tma[1]  |   | X |
| meter#temperatureType2Port       | Number:Temperature       | Temperature of the type 2 port of the Go-eCharger             | tma[0]  |   | X |

### Group '__states__'

All members of the group `states` are read only.

| Channel ID                       | Item Type                | Description                                                   | API key | v1| v2|
|----------------------------------|--------------------------|---------------------------------------------------------------|---------|---|---|
| states#allowCharging             | Switch                   | If `ON` charging is allowed                                   | alw     | X | X |
| states#cableCurrent              | Number:ElectricCurrent   | Specifies the max current that can be charged with that cable | cbl     | X | X |
| states#adapterCurrentLimit       | Number:ElectricCurrent   | Is the 16A adapter used? Limits the current to 16A            | adi     | X | X |
| states#error                     | String                   | Error code of charger                                         | err     | X | X |
| states#chargingState             | String                   | Current charging state description                        | modelStatus |   | X |
| states#numberOfPhases            | Number                   | Always zero                                                   | pnp     |   | X |
| states#phasesAvailable           | Number                   | Number of connected phases                              | pha[3..5]     |   | X |
| states#phasesActive              | Number                   | Number of currently active phases                       | pha[0..2]     |   | X |
| states#pwmSignal                 | String                   | Signal status for PWM signal                                  | car     | X | X |

### Group '__other__'

All members of the group `other` are marked for their access.

| Channel ID                       | Item Type             |Acc| Description                                                  | API key | v1| v2|
|----------------------------------|-----------------------|---|--------------------------------------------------------------|---------|---|---|
| other#powerAll                   | Number:Power          |R  | Power over all three phases                                  | nrg[11] | X | X |
| other#transaction                | Number                |RW | 0 if no card, otherwise card ID                              | trx     |   | X |
| other#accessConfiguration        | String                |RW | Access configuration, for example OPEN, RFID ...             | ast     | X |   |

### Group '__info__'

All members of the group `info` are read only.

| Channel ID                      | Item Type                | Description                                                    | API key | v1| v2|
|---------------------------------|--------------------------|----------------------------------------------------------------|---------|---|---|
| info#firmware                   | String                   | Firmware Version                                               | lmo     | X | X |
| info#goeVariant                 | Number:Power |           | Either 11 or 22 (kW)                                           | var     |   | X |

