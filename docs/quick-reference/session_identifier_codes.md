# Session Identifier Codes

Session Identifiers (aka _Names_) help you identify recorded sessions.

These codes are macros to fill in parts of the identifier automatically.  
You can include them in the identifier when configuring a recorder.

| Macro  | Description
| ------ | -------------------------------------------- |
| `%y`   | Year (Short form - 2 digits: 00 to 99)       |
| `%Y`   | Year (Long form - 4 digits: 0000 to 9999)    |
| `%m`   | Month (2 digits: 01 to 12)                   |
| `%d`   | Day of month (2 digits: 01 to 31)            |
| `%H`   | Hour (2 digits: 00 to 23)                    |
| `%M`   | Minute (2 digits: 00 to 59)                  |
| `%S`   | Second (2 digits: 00 to 59)                  |
| `%c`   | Circuit (text)                               |
| `%r`   | Driver (text)                                |
| `%n`   | Session Number (Text or number)              |
| `%O`   | Unit Data Source (long-form)                 |
| `%o`   | Unit Data Source (short-form)                |
| `%a`   | Car (text)                                   |
| `%e`   | Engine Description (text)                    |
| `%t`   | Race/Test (text)                             |

## Unit Data Sources

Unit Data Source has a long form (`%O`) and short form (`%o`).

Long-form expansion:

* `Wide band Telemetry`
* `Burst Memory`
* `DATALab Memory`

Short-form expansion:

* `T` (Wide-band Telemetry)
* `B` (Burst Memory)
* `D` (DATALab Memory)

