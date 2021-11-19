# Basic samples of Write
The following chapters demonstrate the simple usage of Advanced Streams through basic samples, covering all the bare-minimum steps to implement Telematry Data, Telemetry Samples and Event **write** to Kafka or Mqtt streams.\
The [full source code of the samples is here](./src).

First of all you need to configure the [dependencies](https://github.com/mat-docs/Atlas.AdvancedStreams.Examples/blob/main/MAT.OCS.Streaming.Samples/Samples/Basic/TData.cs#L102-L113)
```cs
const string brokerList = "localhost:9092"; // The host and port where the Kafka broker is running
const string groupName = "dev"; // The group name
const string topicName = "data_in"; // The existing topic's name in the Kafka broker. The *_annonce topic name must exist too. In this case the data_in_announce
var dependencyServiceUri = new Uri("http://localhost:8180/api/dependencies/"); // The URI where the dependency services are running

var client = new KafkaStreamClient(brokerList); // Create a new KafkaStreamClient for connecting to Kafka broker
var dataFormatClient = new DataFormatClient(new HttpDependencyClient(dependencyServiceUri, groupName)); // Create a new DataFormatClient
var httpDependencyClient = new HttpDependencyClient(dependencyServiceUri, groupName); // DependencyClient stores the Data format, Atlas Configuration

var atlasConfigurationId = new AtlasConfigurationClient(httpDependencyClient).PutAndIdentifyAtlasConfiguration(AtlasConfiguration); // Uniq ID created for the AtlasConfiguration
var dataFormat = DataFormat.DefineFeed().Parameter(ParameterId).BuildFormat(); // Create a dataformat based on the parameters, using the parameter id
var dataFormatId = dataFormatClient.PutAndIdentifyDataFormat(dataFormat); // Uniq ID created for the Data Format
```

The DependencyService is used to handle requests for AtlasConfigurations and DataFormats. You must provide an URI for this service. 
The DataFormatClient handles the data formats through the DependencyService for the given group name.
DataFormat is required when writing to stream, as it is used to define the structre of the data and dataFormatId is used to retrieve dataformat from the dataFormatClient.

AtlasConfigurationId is needed only if you want to display your data in Atlas10.

If you want to connect to MQTT, create a client of MqttStreamClient instead of KafkaStreamClient:
```cs
var client = new MqttStreamClient(new MqttConnectionConfig(brokerList, "userName", "password"));
```

#### SSL connection

To connect to your Kafka broker through https using your SSL certificates, you must use provide the following configuration details to *KafkaStreamClient* constructor:
```cs
var sslConfigurationDetails = new Dictionary<string, string>();
sslConfigurationDetails.Add("security.protocol", "ssl");
sslConfigurationDetails.Add("ssl.ca.location", @"C:\certificates\ca-cert");
sslConfigurationDetails.Add("ssl.certificate.location", @"C:\\certificates\certificate.pem");
sslConfigurationDetails.Add("ssl.key.location", @"C:\certificates\key.pem");
sslConfigurationDetails.Add("ssl.key.password", "password");


var client = new KafkaStreamClient(brokerList, sslConfigurationDetails); // Create a new KafkaStreamClient for connecting to Kafka broker, using SSL connection details
```

#### SSL connection

To connect to your Kafka broker through https using your SSL certificates, you must provide the following configuration details to *KafkaStreamClient* constructor:
```cs
var sslConfigurationDetails = new Dictionary<string, string>();
sslConfigurationDetails.Add("security.protocol", "ssl");
sslConfigurationDetails.Add("ssl.ca.location", @"C:\certificates\ca-cert");
sslConfigurationDetails.Add("ssl.certificate.location", @"C:\\certificates\certificate.pem");
sslConfigurationDetails.Add("ssl.key.location", @"C:\certificates\key.pem");
sslConfigurationDetails.Add("ssl.key.password", "password");


var client = new KafkaStreamClient(brokerList, sslConfigurationDetails); // Create a new KafkaStreamClient for connecting to Kafka broker, using SSL connection details
```

[Open the output topic](https://github.com/mat-docs/Atlas.AdvancedStreams.Examples/tree/main/MAT.OCS.Streaming.Samples/Samples/Basic/TData.cs#L115) using the preferred client (KafkaStreamClient or MqttStreamClient) and the topicName.
```cs
using (var outputTopic = client.OpenOutputTopic(topicName)) // Open a KafkaOutputTopic
{
}
```

[Create a SessionTelemetryDataOutput](https://github.com/mat-docs/Atlas.AdvancedStreams.Examples/tree/main/MAT.OCS.Streaming.Samples/Samples/Basic/TData.cs#L118) and configure session output [properties](https://github.com/mat-docs/Atlas.AdvancedStreams.Examples/tree/main/MAT.OCS.Streaming.Samples/Samples/Basic/TData.cs#L118-L125).
```cs
var output = new SessionTelemetryDataOutput(outputTopic, dataFormatId, dataFormatClient);
output.SessionOutput.AddSessionDependency(DependencyTypes.DataFormat, dataFormatId); // Add session dependencies to the output
output.SessionOutput.AddSessionDependency(DependencyTypes.AtlasConfiguration, atlasConfigurationId);

output.SessionOutput.SessionState = StreamSessionState.Open; // set the sessions state to open
output.SessionOutput.SessionStart = DateTime.Now; // set the session start to current time
output.SessionOutput.SessionIdentifier = "data_" + DateTime.Now; // set a custom session identifier
```

You must add dataFormatId and atlasConfigurationId to session dependencies to be able to use them during the streaming session.

Once it is done, [send the session](https://github.com/mat-docs/Atlas.AdvancedStreams.Examples/tree/main/MAT.OCS.Streaming.Samples/Samples/Basic/TData.cs#L126) details to the output.
```cs
output.SessionOutput.SendSession();
```


### Telemetry Data
You will need **TelemetryData** to write to the output. In this example we [generate some random telemetry data](https://github.com/mat-docs/Atlas.AdvancedStreams.Examples/tree/main/MAT.OCS.Streaming.Samples/Samples/Basic/TData.cs#L128) for the purpose of demonstration.
```cs
var telemetryData = GenerateData(10, (DateTime)output.SessionOutput.SessionStart); // Generate some telemetry data
```

[Bind the feed to **DataOutput**](https://github.com/mat-docs/Atlas.AdvancedStreams.Examples/tree/main/MAT.OCS.Streaming.Samples/Samples/Basic/TData.cs#L130-L131) by its name. You can use the default feedname or use a custom one.
```cs
const string feedName = ""; // As sample DataFormat uses default feed, we will leave this empty.
var outputFeed = output.DataOutput.BindFeed(feedName); // bind your feed by its name to the Data Output
```

[Enqueue and send](https://github.com/mat-docs/Atlas.AdvancedStreams.Examples/tree/main/MAT.OCS.Streaming.Samples/Samples/Basic/TData.cs#L133) the telemetry data.
```cs
Task.WaitAll(outputFeed.EnqueueAndSendData(telemetryData)); // enqueue and send the data to the output through the outputFeed
```

### Telemetry Samples
You will need **TelemetrySamples** to write to the output. In this example we [generate some random telemetry samples](https://github.com/mat-docs/Atlas.AdvancedStreams.Examples/tree/main/MAT.OCS.Streaming.Samples/Samples/Basic/TSamples.cs#L125) for the purpose of demonstration.
```cs
var telemetrySamples = GenerateSamples(10, (DateTime)output.SessionOutput.SessionStart); // Generate some telemetry samples
```

[Bind the feed to **SamplesOutput**](https://github.com/mat-docs/Atlas.AdvancedStreams.Examples/tree/main/MAT.OCS.Streaming.Samples/Samples/Basic/TSamples.cs#L127-L128) by its name. You can use the default feedname or use a custom one.
```cs
const string feedName = ""; // As sample DataFormat uses default feed, we will leave this empty.
var outputFeed = output.SamplesOutput.BindFeed(feedName); // bind your feed by its name to the SamplesOutput
```

[Send Samples](https://github.com/mat-docs/Atlas.AdvancedStreams.Examples/tree/main/MAT.OCS.Streaming.Samples/Samples/Basic/TSamples.cs#L130).
```cs
Task.WaitAll(outputFeed.SendSamples(telemetrySamples)); // send the samples to the output through the outputFeed
```

### Events
You will need **Events** to write to the output. In this example we [generate some random event samples](https://github.com/mat-docs/Atlas.AdvancedStreams.Examples/tree/main/MAT.OCS.Streaming.Samples/Samples/Basic/EventsWrite.cs#L67) for the purpose of demonstration.
```cs
var events = GenerateEvents(20, (DateTime)output.SessionOutput.SessionStart); // Generate some events data
```

Then you can just [send the events using `SendEvent` method on **EventsOuput**](https://github.com/mat-docs/Atlas.AdvancedStreams.Examples/tree/main/MAT.OCS.Streaming.Samples/Samples/Basic/EventsWrite.cs#L68).
```cs
var tasks = events.Select(ev => output.EventsOutput.SendEvent(ev)).ToArray(); // enqueue and send the events to the output through the EventsOutput
```

### Closing session

Once you sent all your data, don't forget to [set the session state to closed](https://github.com/mat-docs/Atlas.AdvancedStreams.Examples/tree/main/MAT.OCS.Streaming.Samples/Samples/Basic/TData.cs#L135) and [send the session details](https://github.com/mat-docs/Atlas.AdvancedStreams.Examples/tree/main/MAT.OCS.Streaming.Samples/Samples/Basic/TData.cs#L136).
```cs
output.SessionOutput.SessionState = StreamSessionState.Closed; // set session state to closed. In case of any unintended session close, set state to Truncated
output.SessionOutput.SendSession(); // send session
```