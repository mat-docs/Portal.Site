# Topic Management Service

_Front end to manage Kafka topics._

Apache Kafka uses the concept of named, persistent topics – which can be browsed and configured for the desired data retention and scalability. 

The Topic Management Service provides a REST API and a web user interface to set topics up for use with ATLAS Advanced Streams. 

Of course, Kafka can be managed with command-line tools, but this service provides a convenient web front-end to create, edit and delete topics.

### Prerequisites

- Java 8 x64 or later
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