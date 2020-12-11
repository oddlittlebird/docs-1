# Basics

In this we cover basics of `streams`, `queries`, and how queries can be chained to one another. Similarly basics on `sources` and `sink` that are used to consume and publish events. Also about c8db `tables` aka collections that can be used to store and query events. Finally an introduction to the concept of `Stream Application`.

## Streams & Queries

This section provides introduction to `streams` and `queries`, and how multiple queries can be chained to one another.

There are multiple type of queries such as `window query`, `join query`, `pattern query`, etc. Below example explains how `pass-through` and `selection queries` work. For more info refer the [Stream Query Guide](query-guide.md).

### Example
Following is an example annotated with descriptive comments.

```
-- Defines `InputTemperatureStream` stream to pass events having `sensorId` and `temperature` attributes of types `string` and `double`.
define stream InputTemperatureStream (sensorId string, temperature double);

-- Optional `@info` annotation to name the query.
@info(name = 'Pass-through')

-- Query to consume events from `InputTemperatureStream`, produce new events by selecting all the attributes from the incoming events, and outputs them to `TemperatureStream`.
select *
from InputTemperatureStream
insert into TemperatureAndSensorStream;

@info(name = 'Simple-selection')


-- Selects only the `temperature` attribute from events, and outputs to `TemperatureOnlyStream`.
-- Consumes events from `TemperatureAndSensorStream`. The schema of the stream is inferred from the previous query, hence no need to be defined.
select temperature
from TemperatureAndSensorStream
insert into TemperatureOnlyStream;
```

### Events at each stream

When an event with values [`'aq-14'`, `35.4`] is sent to `InputTemperatureStream` stream it will get converted and travel through the streams as below.

* InputTemperatureStream : [`'aq-14'`, `35.4`]
* TemperatureAndSensorStream : ['aq-14', `35.4`]
* TemperatureOnlyStream : [`35.4`]

## Source & Sink

Sources and Sink are used to consume and publish events to external systems.

There are multiple source and sink types, but this example only explains c8db source, c8stream sink, and kafka sink. For more info refer the [Stream Query guide](query-guide.md).

### Example

```
-- C8DB source to consume `JSON` messages from.
@source(type='c8db', collection='TemparatureStream', collection.type="doc", replication.type="global", @map(type='json'))

-- Defines `TemperatureStream` stream having `sensorId` and `temperature` attributes of types `string` and `double`.
define stream TemperatureStream (sensorId string, temperature double);

-- C8Streams sink to log events arriving via `TemperatureOnlyStream` stream.
@sink(type='c8streams', stream="TemperatureOnlyStream", replication.type="local", @map(type='json'))

-- Defines `TemperatureOnlyStream` stream having `temperature` attribute of type `double`.
define stream TemperatureOnlyStream (temperature double);

@info(name = 'Simple-selection')
select temperature
from TemperatureStream
insert into TemperatureOnlyStream;
```

### Input

When a JSON message is written to the collection `TemparatureStream`, it will automatically get mapped to an event in the `TemperatureStream` stream.

```json
{
    "sensorId":"aq-14",
    "temperature":35.4
}
```

To process custom input messages, please refer the examples related to Input Data Mapping.

### Output

After processing, the event arriving at `TemperatureOnlyStream` will be emitted via `c8stream` and `kafka` sinks.

The message is published to `TemperatureOnlyStream` as

```json
{"temperature":"35.4"}
```

The `kafka` sink maps the event to a custom JSON message as below and publishes it to the `temperature` topic.

```json
{"temp":"35.4"}
```

To output messages using other message formats, pleases refer the examples related to Output Data Mapping.

## Table & Store

Provides introduction to tables and database backed stores that can be used to store events.

### Example

```
-- Defines `TemperatureStream` stream having `sensorId` and `temperature` attributes of types `string` and `double`.
define stream TemperatureStream (sensorId string, temperature double);

-- Defines `TemperatureLogTable` having `sensorId`, `roomNo`, and `temperature` attributes of types `string`, `string`, and `double`.
define table TemperatureLogTable (sensorId string, roomNo string, temperature double);

-- Defines `SensorIdInfoTable` table.
define table SensorIdInfoTable (sensorId string, roomNo string);

@info(name = 'Join-query')
-- Selects `sensorId`, `roomNo`, and `temperature` attributes from stream and table, and adds events to `TemperatureLogTable`.
select t.sensorId as sensorId, s.roomNo as roomNo, t.temperature as temperature
from TemperatureStream as t join SensorIdInfoTable as s
     on t.sensorId == s.sensorId
insert into TemperatureLogTable;
```

### Event at table and store

When `SensorIdInfoTable` table contains a recode [`'aq-14'`, `'789'`], and when an event with values [`'aq-14'`, `35.4`] is sent to `TemperatureStream` stream.

The event will get converted and added to the `TemperatureLogTable` table as below.

[`'aq-14'`, `'789'`, `35.4`]

### Retrieving values from tables and stores

The stored values can be retrieved by join tables and stores with the streams as in the `Join-query` depicted in the example, or using on-demand queries.

The data in `TemperatureDetailsTable` can be retrieved via on-demand queries as below, using the `On Demand Query REST API`.

```
select *
from TemperatureDetailsTable
```

## Stream Application

Provides introduction to the concept of Stream Application.

Stream App provides an isolated execution environment for processing the execution logic. It can be deployed and processed independently of other Stream Apps in the system. Stream Apps can use `inMemory`, `c8db` and `c8stream` sources and sinks to communicate between each other.

### Example

```
-- `C8DB` source to consume events from other Stream Apps.
define stream TemperatureStream (sensorId string, temperature double);

-- `InMemory` sink to publish events from other Stream Apps.
@sink(type='inMemory', topic='Temperature')
define stream TemperatureOnlyStream (temperature double);

@info(name = 'Simple-selection')
select temperature
from TemperatureStream
insert into TemperatureOnlyStream;
```

### Input

When an event [`'aq-14'`, `35.4`] is pushed via the `SensorDetail` topic of the `inMemory` transport from another Stream App, the event will be consumed and mapped to the `TemperatureStream` stream.

### Output

After processing, the event [`35.4`] arriving at `TemperatureOnlyStream` will be emitted via `Temperature` topic of the `inMemory` transport to other subscribed Stream Apps.
