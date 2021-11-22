# It's all about Time

#### Timestamps and Epochs

ATLAS Advanced Streams measures time at nanosecond precision.

Whilst many systems use midnight on 1970-01-01 (UTC) as the time epoch (zero-point), TAG ECUs traditionally use midnight (local time) as the epoch, due to limitations in 48-bit floating point data precision.

To accommodate both approaches and ensure that all times are absolute, ATLAS Advanced Streams qualifies all times with an epoch - also in nanoseconds - relative to 1970. The absolute time, relative to 1970, is arrived at by summing the epoch and time together.

This scheme allows TAG ECU timestamps to be passed through unchanged, with the epoch indicating local time midnight.

#### Forward-only

In general, time within a session should increase monotonically.

There are few parts of the infrastructure that strictly require this guarantee, but it is fundamentally more difficult to create even simple stateful functions if a time period might be re-visited at any point.

#### Sync Points

Some processing might require information from multiple message types.

For example, a model might accumulate data until the end of a lap, and then take action depending on whether the lap was considered a new fastest lap.

Since there is no strict ordering between message types, it can be difficult to determine when all necessary information is available to complete a calculation. In the example above, does data arrive before, or after the lap trigger?

One way to resolve this is to add  `sync`  messages into the stream. These act like a barrier, informing downstream clients that information is complete and coherent up to that sync point. Sync messages should be sent at frequent intervals (e.g. 1Hz) to allow their use without unnecessary buffering.

In the example above, a client could buffer laps and telemetry data until a sync message is seen, and then process all data prior to the lap end time, with the knowledge that no telemetry data has been missed.
