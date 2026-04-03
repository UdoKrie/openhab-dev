# ephemeris binding

Hot fix for windows paths to custom files.

## Symptoms

ephemeris:custom thing reports  
`File is absent: <drive-letter>:\<path-to-file>`

## Root cause

class' `EphemerisManagerImpl` private function `getUrl()` fails to create a correct URL for the given path.

## Quick-fix

ephemeris binding creates an URL instead of a Path before calling getBankHolidayName()
