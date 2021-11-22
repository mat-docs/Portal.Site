# Architecture Overview

ATLAS Streams is designed to work as part of your infrastructure, rather than as interconnected desktop applications.

Here is the overall picture:![atlas-advanced-streams.drawio](./assets/atlas-advanced-streams.drawio.png)

Your code interacts with Kafka through a client library provided as [Nuget package](../../developer/nuget/index.md).

You will need to install supporting components either locally on your laptop, or somewhere central in your development network.

## ECU Bridge

The ECU Bridge is a standalone application bridging data between incoming ATLAS telemetry and Kafka.

![ecu-bridge.drawio.png](./assets/ecu-bridge.drawio.png)
