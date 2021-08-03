# Integrating with Existing Systems

ATLAS [Remote Telemetry Architecture (RTA)](sdk/) defines web services to integrate with your existing environment:

<!-- has to be an object element otherwise the fonts don't load -->
<object type="image/svg+xml" data="../assets/rta-intro.svg" class="diagram" title="Simple integration with ATLAS"></object>

This can work with a wide range of storage technologies, such as:

* [InfluxDB](https://www.influxdata.com/products/influxdb/), [TimescaleDB](https://www.timescale.com/) and other time-series stores
* [MongoDB](https://www.mongodb.com/), [Couchbase](https://www.couchbase.com/) and other document databases
* [Parquet](https://parquet.apache.org/), [HDF5](https://www.hdfgroup.org/solutions/hdf5/) and other file formats
* [SQL Server](https://www.microsoft.com/en-gb/sql-server), [PostgreSQL](https://www.postgresql.org/), [MySQL](https://www.mysql.com/)...

No ATLAS plugin development is required, and we provide an implementation toolkit to get you up running with minimal development.
Includes support for [live monitoring](sdk/#live-monitoring) using Web Sockets.

[Integration Guide](sdk/){ .md-button .md-button--primary } [Quick-Start Tutorial](sdk/rta/devguide/tutorials/quick-start/){ .md-button } [REST API Reference](sdk/api/){ .md-button }
