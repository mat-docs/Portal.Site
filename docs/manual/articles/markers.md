# Markers

## Overview

Markers are used to define a specific time or a time range within a session. They are user definable and can contain zero or more annotations to
describe what the marker represents.

!!! note
    The SQL Race API definition of the Marker class is available [here](https://mat-docs.github.io/Atlas.SQLRaceAPI.Documentation/api/MESL.SqlRace.Domain.Marker.html)


Structure of a marker
    ```
    "markers": {
        "Laps": [
            {
                "id": "6772bd79-ab6a-4e58-a1de-43c0ce1c1a02",
                "type": "lapTrigger",
                "label": "Lap 1",
                "startTime": 1620323936202890000,
                "properties": {
                    "lapNumber": {
                        "label": "Lap Number",
                        "value": 1
                    },
                    "triggerSource": {
                        "label": "Lap Trigger Source",
                        "value": 2
                    }
                }
            }
            // ...
        ]
    }
    ```

Applications of markers?

Algorithmic highlighting
Updatable so the marker can be created when the time range starts then updated as it completes (when running in live)
Groupable
Annotations
-lap number
-lap trigger byte
-user metadata
-algorithm confidence
-custom formatting for value

Markers can be moment-in-time so users can highlight a discrete point

Markers can be discontinuous regions so an algorithm can highlight a zone – for example, a grip-limited section
 
Markers can be continuous regions so a lap trigger can indicate the start of a lap that will continue until the next trigger

Markers are open ended to automatically extend to the start or end of the containing session