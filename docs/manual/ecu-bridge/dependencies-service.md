# Dependencies Service

_REST service containing schemas describing the streaming data._

<!-- This is the service responsible for the storage and retrieval of data formats used in ATLAS Advanced Streams (AAS). It supports the storage of two forms of formats:

- Data Formats for the parsing of AAS messages
- Atlas Configuration to support rendering the content of the AAS messages -->

This microservice stores reference data off-stream. This improves efficiency, helps support late-joining streams, and time-based data expiry. 
ATLAS Advanced Streams uses this service to describe topic parameter selection and ATLAS configuration – but it can be used for any reference data associated with a stream. 

Think of this as equivalent to managing CFG and PGV files - it takes that job away from Kafka for simpler management.

More details can be found in the [API Reference](https://mclarenappliedtechnologies.zendesk.com/hc/en-us/articles/115003531373-API-Reference-Dependencies-Service)

### Prerequisites

- .NET Core 2.1

### Installation

Run the installer archived in the ECU Bridge release zip file:

`MAT Streaming Dependencies Service.msi`

### Configuration

THe service will auto-start without further configuration.

The configuration file is located at:
`C:\Program Files\McLaren Applied Technologies\Streaming Dependencies Service\appsettings.json`

Requests against the dependency service include two white-listed elemetns:
- `DependencyGroups`: Separates the dependencies group into namespaces. Default is `dev`
- `DependencyTypes`: Describes the dependencies' context. Default is `dataFormat,atlasConfiguration`

Extend these options as necessary after deployment. 

Refer to [API Reference - Dependencies Service](https://mclarenappliedtechnologies.zendesk.com/hc/en-us/articles/115003531373-API-Reference-Dependencies-Service)

### Verification

Check the service has started.

Browse to [http://localhost:8180/api/dependencies/](http://localhost:8180/api/dependencies/) and check for a textual usage statement. This URI - modified appropriately for the host server via config - is supplied to the ATLAS Streaming Recorder, and to the MAT.OCS.Streaming API. Always include the trailing slash.