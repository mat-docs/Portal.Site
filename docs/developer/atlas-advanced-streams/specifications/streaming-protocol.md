# Streaming protocol
#### Topics, Streams and Sessions

**Topic**

A topic should represent a meaningful subset of parameters, where a consumer is likely to be interested in most of the data on the topic. It can carry multiple concurrent sessions, or streams.

**Stream**

Streams are labelled sequences of messages within a topic. There can be many concurrent streams in one topic, which is particularly useful if there are many sources of data.

**Session**

Similar to the ATLAS Session concept, and necessary for interaction with the ATLAS ecosystem. Implemented on top of Streams.


#### Time

Stream sessions use nanosecond data precision, relative to a specified epoch (also in ns).

For more information, read the section  [It's all about Time](../concepts/its-all-about-time.md).

#### Message structure

Kafka messages have a key and a value.

##### Keys

In ATLAS Advanced Streams, the key is always structured in two parts, like this:

`<type>:<stream id>`

The  `type`  indicates the message content, which allows a client to select an appropriate parser for the message value. This article includes a defined list of message types, but a conforming client  _must_  accept (and likely ignore) other message types to allow extensibility.

The  `stream id`  distinguishes the stream to which the message belongs. Messages with the same stream id must be sent on the same partition to maintain message sequencing; note that this means that default Kafka stream of distributing messages by key hash must not be used.

Keys must be encoded as UTF-8 with no  [BOM](https://en.wikipedia.org/wiki/Byte_order_mark).

##### Values

The value format is determined by the message type.

Values are typically either empty, or well-formed JSON - see attached schemas.  
JSON values should be encoded as UTF-8 with no BOM.

Clients must not assume that  _all_  messages are JSON; future versions of the protocol may include more compact binary encodings, with their own message types.

#### Messages

##### $start

Indicates the start of a stream - enables clients to identify partial streams.\
Clients must not send stream messages before sending $start.\
This message has an empty payload.

##### $end

Indicates the end of a stream.\
Clients must not send stream messages after sending $end.\
This message has an empty payload.

##### session

Identifies the stream as a session.\
Contains descriptive metadata, traceability and references to dependencies.

The session message is repeated at intervals to ensure that a client can join a live session and establish all necessary context, and as a heartbeat so that clients can determine whether the upstream process generating the session is still alive.

Example:
```json
{
  "id": "f2b8755c-c426-4cdc-9e51-ee7cd95a7879",
  "state": "open",
  "identifier": "random_walk",
  "dependencies": {
    "atlasConfiguration": ["e7548e787967"],
    "dataFormat": ["9a9627b7bc25"]
  },
  "activity": {
    "start": "2017-11-19T15:00:00Z",
    "durationNanos": 30000000
  }
}
```

Notice the declaration of both  `dataFormat`  and  `atlasConfiguration`  dependencies. This is a minimum requirement to use the ATLAS 10 stream recorder.

[session.schema.json](../assets/session.schema.json)

##### tdata

Contains telemetry data, on a common timebase.

Example:
```json
{
  "epoch": 1511091963784000000,
  "time": [10000000, 20000000],
  "data": [
    {
      "status": [1, 1],
      "avg": [0.0, 10.0]
    }
  ],
  "feed": "",
  "format": "9a9627b7bc25"
}
```

Aggregate arrays can include:  `avg`,  `min`,  `max`, &  `first`.\
Status values reflect results of data retrieval, and are defined as the following bitwise flags:

|Value|Name|Description|
|---|---|---|
|0|Missing|_Missing sample_|
|1|Sample|_Valid sample; if there is at least one sample in the interval._|
|2|Default|_Default sample; typically due to sensor failure._|
|4|Before Start|_Data was requested before the first available sample._|
|8|After End|_Data was requested after the last available sample._|
|16|Incomplete|_Interval included missing samples. Common when down-sampling._|
|32|Interpolated|_Sample is interpolated. Also common._|
|64|Pending|_Sample is being fetched. Unlikely to be seen in streams._|
|128|Gap|_Gap in data._|


These statuses provide detailed information for models that require it, but in general, all values are useful except when NaN.\
See  [Data Feeds, Formats and Views](../concepts/data-feeds-formats-views.md)  for information about the  `feed`  and  `format`  fields.

[tdata.schema.json](../assets/tdata.schema.json)

##### tsamples

Contains unaggregated telemetry data. Essentially used for data ingest - where aggregates and statuses are not useful. The `data` field is a dictionary of parameters containing a one to one representation of timestamps and values.

Example:
```json
{
  "feed": "5hz",
  "format": "9eb6ba4140f1",
  "data": {
    "parameter1": {
      "values": [
        53.424999237060547,
        53.325000762939453,
      ],
      "epoch": 1581033600000000000,
      "time": [
        37132482000000,
        37132682000000,
      ]
    },
    "parameter2": {
      "values": [
        47.724998474121094,
        47.5099983215332,
      ],
      "epoch": 1581033600000000000,
      "time": [
        37132882000000,
        37133082000000,
      ]
    },
  }
}
```

[tsamples.schema.json](../assets/tsamples.schema.json)

#### events

A discrete event at a given timestamp. These could be events such as gear shifts or doors opening.
The `values` array contains any numerical values associated with the event. The parameters of which are
described in the event definition.

Example:

```json
{
  "epoch": 1511091963784000000,
  "time": 10000000,
  "id": "IO2-2B02:TAG320BIOS",
  "status": "Active",
  "values": [
    10.0, 20.0
  ]
}
```

[events.schema.json](../assets/events.schema.json)

##### lap

Denotes a lap trigger.

Example:
```json
{
  "number": 0,
  "epoch": 1511049600000000000,
  "time": 50639840000000,
  "type": "outLap",
  "triggerSource": 3
}
```

Trigger sources include:

|Value|Name|
|---|---|
|0|Main straight|
|1|Pit lane|
|2|Default|
|3|Telemetry start|
|4|Telemetry end|

These triggers give rise to business logic to describe the lap type, which is one of:  
`fastLap`,  `pitLane`,  `outLap`, or  `inLap`. The exact business logic is outside the scope of this specification and may vary slightly by race formula.

[lap.schema.json](../assets/lap.schema.json)

##### sync

Sync messages create synchronization points across message types.

See  [It's all about Time](../concepts/its-all-about-time.md)  for more information.

-   [lap.schema.json](../assets/lap.schema.json)
-   [session.schema.json](../assets/session.schema.json)
-   [tsamples.schema.json](../assets/tsamples.schema.json)
-   [tdata.schema.json](../assets/tdata.schema.json)