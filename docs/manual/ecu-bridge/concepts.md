# ECU Bridge

## Prerequisites

- .NET 4.6.2
- ATLAS 10

## Configuration

Unzip the ECU Bridge 0.13.0.zip file

Configuration is located in _Config.json_, which specifies:

- Wideband recorder(s)
- System Monitor directories
- Kafka connection string

Here is a reference example, for a localhost ADS, Kafka broker and a dependencies service:

```json 
{
  "TestMode": false, 
  "RecorderType": "kafka",
  "ReceiverType": "Wideband",
  "ReceiverSettings": {
    "WidebandReceiver": {
      "Host": "localhost",
      "StreamName": "Default"
    }
  },
  "MetricsSettings": {
    "Enabled": true,
    "Port":  9505 
  },
  "RdaUnlockSettings": {
    "UnlockFileDirectories": "X:\\config\\PUL"
  },
  "RecorderSettings": {
    "PgvSettings": {
      "Path": "X:\\config"
    },
    "ConfigSettings": {
      "Path": "X:\\config\\Cfgs"
    },
    "KafkaSettings": {
      "DependencyUri": "http://localhost:8180/api/dependencies/",
      "BrokerList": "localhost:9092",
      "SessionIdentifier": "Example identifier %r %y%m%d%H%M%S",
      "TopicName": "AllSamples",
      "SessionDetails": {
        "Driver": "$NDriverInitial1$$NDriverInitial2$$NDriverInitial3$"
      }
    }
  }
}
```

This is completely independent of ATLAS registry settings, but does still require specific "ECU Bridge" license.

For a detailed configuration reference, consult the _readme_ file included in the zip distribution.

## Running the service

Review the _Config.json_, and edit the paths and service urls to match your environment.

> Note: System Monitor settings applied in ATLAS/ADS are ignored

Create the ropic using the Topic Management Service (runs by default on http://localhost:8182). In the sample file above, the output topic is names _AllSamples_.

To run the application from a command prompt:

```
MAT.TAP.Recorders.Standalone.exe
```

## Replaying Raw Files

Modify the configuration file to specify file replay, as follows:

```json
...
"ReceiverSettings": {
    "FileReceiver": {
      "File": "X:\\data\\races\\Race.ssn_16.raw_tm",
      "PlaybackSpeed": 1,
      "Loop": false
    }
},
...
```

<!-- TODO: Find out if the following section is still valid
refer to: https://mclarenappliedtechnologies.zendesk.com/knowledge/articles/360010656973/en-us?brand_id=1083465
## Limitations

This preview service forwards only samples and laps.

It does support backfill, but does not yet provide resampling, latency or coverage control.
Events are not yet supported.

You cannot select subsets of parameters - this service streams everything. This is ideal for use cases centered on export and data movement, but less useful for model execution. -->