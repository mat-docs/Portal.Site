# Protobuf extension

The  _MAT.OCS.Streaming.Codecs.Protobuf_  package provides a faster, more-compact serialization for telemetry data and samples. Prefer this codec when working with large topics.

#### Enabling the Codec

##### Enabling in your code

Bring the package into your project using NuGet.

To register the codec to parse incoming data, call:\
ProtobufCodecs.RegisterCodecs();

To use the codec by default to both send and receive data, call:\
ProtobufCodecs.RegisterCodecs(true);

<!-- ##### Enabling in the Gateway Service

This option is available from 0.7.0 (Beta 4).

Set the  _ProtobufCodecEnabled_  option to  _True_. -->

#### IDL

Use the IDL (attached to this page) to generate a parser in a wide range of languages.

For more information, see  [https://developers.google.com/protocol-buffers/](https://developers.google.com/protocol-buffers/)

-   [protocol.proto](../assets/protocol.proto)
