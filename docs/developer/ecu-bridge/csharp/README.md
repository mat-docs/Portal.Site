# Introduction
ATLAS Streams API provides infrastructure for streaming data around the ATLAS technology platform.

Using this API, you can:
- Subscribe to streams of engineering values - no ATLAS recorder required
- Inject parameters and aggregates back into the ATLAS ecosystem
- Build up complex processing pipelines that automatically process new sessions as they are generated

With support for Apache Kafka, the streaming API also offers:
- Late-join capability - clients can stream from the start of a live session
- Replay of historic streams
- Fault-tolerant, scalable broker architecture

The [full source code of the samples is on GitHub](https://github.com/mat-docs/MAT.OCS.Streaming.Samples).

# Requirements
You need to install the following Nuget packages from MAT source

- MAT.OCS.Streaming
- MAT.OCS.Streaming.Kafka
- MAT.OCS.Streaming.Codecs.Protobuf
<!-- - MAT.OCS.Streaming.Mqtt -->