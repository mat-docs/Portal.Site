# Advanced Samples

Advanced samples cover the usual use cases for reading, writing and reading and linking telemetry data in an structured and organized code.
According to that each sample .cs file has a `Write()`, `Read()` and `ReadAndLink()` methods and all of the sample files rely on the same structure. You can use them as working samples copying to your application code.
The .cs files in the [Samples folder](https://github.com/mat-docs/MAT.OCS.Streaming.Samples/tree/main/MAT.OCS.Streaming.Samples/Samples) have documenting and descriptive comments, but lets take a look at a simple and a more complex sample in depth.

## Writing Telemetry Data with a parameter and default feed name to a Kafka topic.

First of all you need to create or use an [AtlasConfiguration](https://github.com/mat-docs/MAT.OCS.Streaming.Samples/tree/main/MAT.OCS.Streaming.Samples/Samples/Advanced/TDataSingleFeedSingleParameter.cs#L27-L53). You need to set the [details](https://github.com/mat-docs/MAT.OCS.Streaming.Samples/tree/main/MAT.OCS.Streaming.Samples/Samples/Advanced/TDataSingleFeedSingleParameter.cs#L13-L22) what `AppGroupId`, `ParameterGroupId`, `ParameterID` you want to use.

Once you have your AtlasConfiguration design, you need to set [details](https://github.com/mat-docs/MAT.OCS.Streaming.Samples/tree/main/MAT.OCS.Streaming.Samples/Samples/Advanced/TDataSingleFeedSingleParameter.cs#L117-L120) for the DependencyService URI, the stream broker address, the group name and the output topic name where you want to write. 
The DependencyService is used to handle requests for AtlasConfigurations and DataFormats, you must provide an URI for this service. 

A [KafkaStreamAdapter](https://github.com/mat-docs/MAT.OCS.Streaming.Samples/tree/main/MAT.OCS.Streaming.Samples/Samples/Advanced/TDataSingleFeedSingleParameter.cs#L121) is used to manage Kafka streams.

Using the KafkaStreamAdapter you must [open and output topic](https://github.com/mat-docs/MAT.OCS.Streaming.Samples/tree/main/MAT.OCS.Streaming.Samples/Samples/Advanced/TDataSingleFeedSingleParameter.cs#L122) in Kafka.

You need to persist your configuration, setup your DataFormat and setup a streaming session to be able to write to your broker. The [Writer.cs](https://github.com/mat-docs/MAT.OCS.Streaming.Samples/tree/main/MAT.OCS.Streaming.Samples/Samples/Advanced/Writer.cs) code sample covers all of these, you only need to [use a Writer object](https://github.com/mat-docs/MAT.OCS.Streaming.Samples/tree/main/MAT.OCS.Streaming.Samples/Samples/Advanced/TDataSingleFeedSingleParameter.cs#L124) with the required parameters.

In this example we are usign the [default feed name](https://github.com/mat-docs/MAT.OCS.Streaming.Samples/tree/main/MAT.OCS.Streaming.Samples/Samples/Advanced/TDataSingleFeedSingleParameter.cs#L126), which is the empty string.
You must take care about [opening](https://github.com/mat-docs/MAT.OCS.Streaming.Samples/tree/main/MAT.OCS.Streaming.Samples/Samples/Advanced/TDataSingleFeedSingleParameter.cs#L127) and [closing](https://github.com/mat-docs/MAT.OCS.Streaming.Samples/tree/main/MAT.OCS.Streaming.Samples/Samples/Advanced/TDataSingleFeedSingleParameter.cs#L134) the session, otherwise it would mark the session as [truncated](https://github.com/mat-docs/MAT.OCS.Streaming.Samples/tree/main/MAT.OCS.Streaming.Samples/Samples/Advanced/Writer.cs#L52-L-57), just as if any error would occur during session usage.

In the samples we are working with random [generated data](https://github.com/mat-docs/MAT.OCS.Streaming.Samples/tree/main/MAT.OCS.Streaming.Samples/Samples/Advanced/TDataSingleFeedSingleParameter.cs#L79-L113), while in real scenarios they would come as real data from external systems.

To write to the output topic you only need to [invoke the Write method](https://github.com/mat-docs/MAT.OCS.Streaming.Samples/tree/main/MAT.OCS.Streaming.Samples/Samples/Advanced/TDataSingleFeedSingleParameter.cs#L132) on your writer object, passing in the feed name and the telemetry data. The Writer object already "knows" your AtlasConfiguration, the DataFormat and the output topic name.


## Reading Telemetry Data for a parameter from a Kafka stream.

First of all you need to create or use an [AtlasConfiguration](https://github.com/mat-docs/MAT.OCS.Streaming.Samples/tree/main/MAT.OCS.Streaming.Samples/Samples/Advanced/TDataSingleFeedSingleParameter.cs#L27-L53). You need to set the [details](https://github.com/mat-docs/MAT.OCS.Streaming.Samples/tree/main/MAT.OCS.Streaming.Samples/Samples/Advanced/TDataSingleFeedSingleParameter.cs#L13-L22) what AppGroupId, ParameterGroupId, ParameterID you want to use.

Once you have your AtlasConfiguration design, you need to set [details](https://github.com/mat-docs/MAT.OCS.Streaming.Samples/tree/main/MAT.OCS.Streaming.Samples/Samples/Advanced/TDataSingleFeedSingleParameter.cs#L117-L120) for the DependencyService URI, the stream broker address, the group name and the output topic name where you want to write. 
The DependencyService is used to handle requests for AtlasConfigurations and DataFormats, you must provide an URI for this service. 

A [KafkaStreamAdapter](https://github.com/mat-docs/MAT.OCS.Streaming.Samples/tree/main/MAT.OCS.Streaming.Samples/Samples/Advanced/TDataSingleFeedSingleParameter.cs#L145) is used to manage Kafka streams.

Using the KafkaStreamAdapter you must [open and output stream](https://github.com/mat-docs/MAT.OCS.Streaming.Samples/tree/main/MAT.OCS.Streaming.Samples/Samples/Advanced/TDataSingleFeedSingleParameter.cs#L146) in Kafka.

You need connect to the DependencyClient and the DataFormatClient and perist the StreamPipelineBuilder that was created by the KafkaStreamAdapter. Using a [Reader object](https://github.com/mat-docs/MAT.OCS.Streaming.Samples/tree/main/MAT.OCS.Streaming.Samples/Samples/Advanced/TDataSingleFeedSingleParameter.cs#L147) takes care about it.

You can start reading a stream with the [Read method](https://github.com/mat-docs/MAT.OCS.Streaming.Samples/tree/main/MAT.OCS.Streaming.Samples/Samples/Advanced/TDataSingleFeedSingleParameter.cs#L174) on your reader object. This takes 2 arguments, the first one is more trivial, it is the parameter id. The second must be a user specified method, aligning to the [TelemetryDataHandler](https://github.com/mat-docs/MAT.OCS.Streaming.Samples/tree/main/MAT.OCS.Streaming.Samples/Samples/Advanced/Models.cs#L12) delegate. With the help of this you can handle the streamed Telemetry Data as you would like to. In another example you will see how can you link it directly to another output topic.

Lastly, in our sample code we [invoke the Write()](https://github.com/mat-docs/MAT.OCS.Streaming.Samples/tree/main/MAT.OCS.Streaming.Samples/Samples/Advanced/TDataSingleFeedSingleParameter.cs#L151) method while the streaming session is live, to have some input to see that the streaming is working. Our sample delegates, called as Models are in the [Models.cs](https://github.com/mat-docs/MAT.OCS.Streaming.Samples/tree/main/MAT.OCS.Streaming.Samples/Samples/Advanced/Models.cs) and in this example we use the [TraceData method](https://github.com/mat-docs/MAT.OCS.Streaming.Samples/tree/main/MAT.OCS.Streaming.Samples/Samples/Advanced/Models.cs#L14-L27) to trace the streamed telemetry data deatils.
