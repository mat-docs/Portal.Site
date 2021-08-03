# Session Identifier Macros

Session Identifiers (aka _names_) help you identify recorded sessions.

ATLAS provides formatting macros to fill in parts of the identifier automatically.  
You can include them in the identifier when configuring a recorder.

!!! tip

    These macros can also be used in Session Details (metadata).

## Time Codes

These codes format the session identifier from the time of recording:

| Macro  | Description
| ------ | -------------------------------------------- |
| `%y`   | Year (Short form - 2 digits: 00 to 99)       |
| `%Y`   | Year (Long form - 4 digits: 0000 to 9999)    |
| `%m`   | Month (2 digits: 01 to 12)                   |
| `%d`   | Day of month (2 digits: 01 to 31)            |
| `%H`   | Hour (2 digits: 00 to 23)                    |
| `%M`   | Minute (2 digits: 00 to 59)                  |
| `%S`   | Second (2 digits: 00 to 59)                  |

!!! example

    `%y%m%d%H%M%S` = `210730140617` _at 2021-07-30 14:06:17 local time_

## Session Details

These codes incorporate standard session details (metadata):

| Macro  | Session Detail
| ------ | -------------------------------------------- |
| `%a`   | _Car_                                        |
| `%c`   | _Circuit_                                    |
| `%e`   | _Engine_                                     |
| `%r`   | _Driver_                                     |
| `%n`   | _Session Number_                             |
| `%t`   | _Race/Test_                                  |
| `%O`   | _Unit Data Source_ (populated automatically) |

## Parameter Data

To extract parameter values, use `${param}$`, where `{param}` is the parameter identifier or name.

This is commonly used together with text conversions &mdash; to spell out driver initials, for example.

!!! example

    `$NDriverInitial1$$NDriverInitial2$$NDriverInitial3$_$NFOMRacingNo$` = `HAM_44`

If the parameter is not present &mdash; for example, while configuration is being processed &mdash; the macro will not be expanded.
