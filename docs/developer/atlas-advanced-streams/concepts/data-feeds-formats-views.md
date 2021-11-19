# Data Feeds, Formats, and Views

#### Data Feeds

ATLAS Advanced Streams divides telemetry data into named feeds.

A feed is essentially a sub-stream of data: a defined list of parameters at a specific frequency.

This has some useful properties:

-   Data can be streaming at the accuracy required, reducing bandwidth and processing effort
-   Multiplexing data from different sources is easier
-   Clients can bind onto feeds they are interested in

Feed names are arbitrary, but should be kept short as they are repeated in every data message.

For convenience, there is the concept of a "default" feed, which has an empty name ("").

#### Data Formats

The telemetry data messages are not self-describing, which implies there is a dependency describing the data format.

A data format can be defined using the library using a fluent builder syntax:
```
DataFormat.DefineFeed().
  Parameter(DataAggregateSelection.Avg, "vCar:Chassis").
  Parameters(DataAggregateSelection.Min | DataAggregateSelection.Max, "gLat:Chassis", "gLong:Chassis").
  BuildFormat();
```

In JSON this looks like:
```json
{
  "feeds": {
    "": {
      "parameters": [
        {
          "identifier": "vCar:Chassis",
          "aggregates": "avg"
        },
        {
          "identifier": "gLat:Chassis",
          "aggregates": "min, max"
        },
        {
          "identifier": "gLong:Chassis",
          "aggregates": "min, max"
        }
      ],
      "frequency": 100.0
    }
  }
}
```
This JSON is published as a dependency using the library  `DataFormatClient`, and every telemetry data message includes the resulting dependency identifier.

The data format id is in every message to allow a long-running stream to transition to including additional parameters. This would define a new data format, and therefore a new dependency identifier.

#### Binding a View

The library allows a client to define a view onto a feed: a list of parameters and aggregates it expects to see.

The effect of this view is that upstream sources can insert additional parameters into a feed without breaking client compatibility, as the view presents a stable list of parameters to the client code.