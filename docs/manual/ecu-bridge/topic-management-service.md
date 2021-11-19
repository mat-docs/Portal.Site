# Topic Management Service

Provides a REST API and a web user interface to set topics up for use with ATLAS Advanced Streams (AAS). The service is pre-configured for use with a local Kafka broker.

### Prerequisites

- [Java 8 x64 or later](#Java-installation)
- Kafka 0.11 or later

### Installation

Run the msi installer that you can find in the released ECU Bridge zipped folder.

`MAT Streaming Topic Management Service.msi`

If you are managing a Kafka cluster not located on `localhost` edit the service configuration:
`C:\Program Files\McLaren Applied Technologies\Streaming Topic Management Service\application.properties`

Update the `dmin-config.bootstrap.servers` to match your brokers:
- Single node: `server:9092`
- Kafka cluster: `server1:90902,server2:9092,server3:9092`

Start the service.

### Verification

Verify that the Kafka broker(s) are up.

Ensure the Topic Management Service is started.

Visit `http://localhost:8182/app` and verify that the application is up and running.