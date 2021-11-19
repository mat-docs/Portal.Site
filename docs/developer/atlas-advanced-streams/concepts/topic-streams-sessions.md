# Topics, Streams and Sessions

#### Topics

A topic should represent a meaningful subset of parameters, where a consumer is likely to be interested in most of the data on the topic. It can carry multiple concurrent sessions, or streams.

For some use cases, it will make sense to send data from multiple origins over the same topic - for example, capturing data from a set of sensors.

To distinguish data from only a few major sources - such as each car - you may prefer to create separate topics, so they can be processed and recorded separately.

#### Streams

Streams are labelled sequences of messages within a topic. There can be many concurrent streams in one topic, which is particularly useful if there are many sources of data.

The ATLAS Advanced Streams API separates messages by stream to feed them into your code.

#### Sessions

Sessions are intended to be a close analogue of the ATLAS Session concept. A session represents a period of captured telemetry data. This could be a race, a test session or a journey.

They are implemented on top of streams, which means that you can have multiple concurrent sessions in a topic, and that the API will separate the sessions to feed them into your code.

The API includes special support for sessions:

-   List live and recently-historic sessions on a topic
-   Selectively replay a specific session

##### Identity

Sessions must declare an  `id`.

This forms part of the concept of data traceability, further qualified by:

`type`

Type of session. Defaults to  `StreamSession`; should not exceed 32 characters.

`quality`

A measure of the completeness of the data, as a number in the range 0.0 (no useful data) - 1.0 (complete). This reflects the limitations of streaming data over unreliable links with limited bandwidth. Defaults to  `0.9`.

##### Identifier and Details

Equivalent to the ATLAS concepts, the session identifier is equivalent to a filename, with details as attributes describing arbitrary metadata about the session.

The identifier and details can change dynamically as the session progresses.

##### Activity and Heartbeat

Sessions declare a start time and duration. Clients should update the duration as they are writing the session, as - like a file size - it is a useful reflection of the contents of the session.

The start time is a reflection of the data contained in the session, but the session also has a heartbeat, which reflects wall clock time as the session is streamed. The  `MAT.OCS.Streaming`  library maintains the heartbeat automatically.

The heartbeat services a secondary purpose: By repeating the current Session state at intervals, a client can acquire the session just by listening to the stream for a short period.

##### Lifecycle

Sessions declare a state, which transitions through this lifecycle:

`waiting`

The session is announced and described, but no data is being sent.  
This state is primarily intended to allow pipeline processing to initialize and process dependencies early, reducing latency through successive pipeline stages. Not currently exploited by the gateway service.

`open`

The session is sending data. The session description can still dynamically update after transitioning to Open.

`closed`

The session has stopped sending data, and downstream resources can be released. Clients should not send further session data after this point.  
This state has several sub-states:

`truncated`

The session closed without all data being sent.

`failed`

The session was truncated due to an error.

`abandoned`

The session was truncated due to being abandoned - perhaps due to a crash in an upstream process. This state would generally be set by a watchdog or cleanup utility, based on the lack of a heartbeat.

A session may not transition through all states. Entering a  `waiting`  state does not guarantee that the session will become  `open`, though it should enter  `closed`, or one of the  `closed`  sub-states. Once closed, however, it should not be re-opened.

##### Model

Sessions can declare model attributes:

`group`

Identifies the model category (e.g. "aero"). Should not exceed 255 characters.

`version`

Distinguishes between iterations of the model with different parameterisation. Defaults to  `0`  if no version is specified, so number from  `1`  upwards to use versioning.

`configuration`

Optional information capturing the model parameterisation.

These attributes characterise code being run in a pipeline, so that downstream clients can determine how a stream session has been transformed.

##### Sources

Sessions can declare a list of sources - which are themselves sessions. This recursive definition creates traceability back through a processing pipeline.

##### Dependencies

Dependencies are reference data stored outside the stream, that may be required to understand the stream content. The session declares them as lists of unique dependency identifiers, grouped together by type.

Dependencies are stored by the  [Dependencies Service](#MAT-Streaming-Dependencies-Service).

This concept creates separation between the lifetime of the stream and the dependencies. Stream messages may be retained for a comparatively short period, while the dependencies may be referenced indefinitely by many streams.