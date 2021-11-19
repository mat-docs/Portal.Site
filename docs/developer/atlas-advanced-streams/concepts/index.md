# Design Concept
#### Backbone

The core idea is to provide a backbone for streaming data in and out of the ATLAS ecosystem.

In particular, to perform external processing, simulation or aggregation of data, a process can:

-   be written in any common language
-   run anywhere in the infrastructure (track, factory or cloud)
-   receive decoded data without having to host a full ATLAS recorder
-   describe and generate new parameters or aggregates
-   publish the result to any other streaming process, ATLAS clients or store

Sources could include:

-   ATLAS recorders
-   Simulations
-   Sensors

Sinks could include:

-   ATLAS / SQL Race
-   Live displays
-   Big data streaming frameworks
-   Arbitrary data files

#### Key Advantages for Developers

-   Few dependencies
-   Easy to insert and export data
-   Easy to integrate with models
-   Standard model to connect distributed systems
-   API designed specifically for streaming data

#### Key Advantages for System Administrators

-   No multicast
-   Simple configuration to communicate to developers
-   All the complexity is contained in easily-managed services
-   Kafka infrastructure is easy to deploy and scale as needed
-   Will support Kerberos authentication and access control in all components