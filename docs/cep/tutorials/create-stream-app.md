# Create Stream Application

## Introduction

Stream applications are declarative specs that define the processing logic to process the events sent to the stream processor. A stream app definition contains the following configurations:

<table>
<thead>
<tr class="header">
<th>Configuration</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Stream</td>
<td>A logical series of events ordered in time with a uniquely identifiable name, and set of defined attributes with specific data types defining its schema.</td>
</tr>
<tr class="even">
<td>Source</td>
<td>This consumes data from external sources (such as <code>TCP</code> , <code> Kafka </code> , <code> HTTP </code> , etc) in the form of events, then converts each event (that can be in <code>XML</code> , <code>             JSON</code> , <code> binary</code> , etc. format) to a stream event, and passes that to a stream for processing.</td>
</tr>
<tr class="odd">
<td>Sink</td>
<td>This takes events arriving at a stream, maps them to a predefined data format (such as <code>  XML </code> , <code>JSON,</code> <code>binary</code> , etc), and publishes them to external endpoints (such as <code> E-mail </code> , <code> TCP </code> , <code> Kafka </code> , <code>HTTP </code> , etc).</td>
</tr>
<tr class="even">
<td>Table</td>
<td>A structured representation of data stored with a defined schema. Stored data can be backed by In-Memory, or external data stores such as RDBMS, MongoDB, etc. The tables can be accessed and manipulated at runtime.</td>
</tr>
<tr class="odd">
<td>Executional Element</td>
<td><p>An executional element can be one of the following:</p>
<ul>
<li>Stateless query: Queries that only consider currently incoming events when generating an output. e.g., filters</li>
<li>Stateful query: Queries that consider both currently incoming events as well as past events when generating an output. e.g., windows, sequences, patterns, etc.</li>
<li>Partitions: Collections of stream definitions and queries separated from each other within a Stream application for the purpose of processing events in parallel and in isolation</li>
</ul></td>
</tr>
</tbody>
</table>

Macrometa provide in-build source, sink and store explained in the later section of this document.

## Creating a Stream Application

To create a stream application follow the steps below:

1. Open the GUI. Click on `Stream Apps` tab.
1. Click on **New** to start defining a new stream application.
1. Enter a **Name** as `SweetProductionAnalysis` or feel free to chose any other name for the stream application.
1. Enter a **Description**.
1. Add the following sample stream application.

    ```
    @source(type = 'c8db', collection='SweetProductionData', @map(type='json'))
    define stream SweetProductionStream (name string, amount double);

    @sink(type= 'c8streams', stream='ProductionAlertStream', @map(type='json'))
    define stream ProductionAlertStream (name string, amount double);

    select *
    from SweetProductionStream
    insert into ProductionAlertStream;
    ```

1. Click `Save` to save the stream app.
1. Select all the regions to deploy your application in.
1. Click on `Save`.

## Source

### C8Streams

**Syntax**

```
@source(type="c8streams", stream.list="<STRING>", replication.type="<STRING>", @map(...)))
```

**Example**

```
@source(type="c8streams", stream.list="OrderStream", replication.type="local", @map(type='json')))
define stream OrderStream(product_id string, quantity integer)
```

If `@source` annotation is not provided, `c8stream` is considered as a the default source. Stream application will use the c8stream with the default query parameters explained in the chart below. In the above example, stream can also be defined as
```
define stream OrderStream(product_id string, quantity integer)
```

**Query Parameters**

<table>
<thead>
<tr class="header">
<th>Name</th>
<th>Description</th>
<th>Default Value</th>
<th>Possible Data Types</th>
<th>Optional</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>stream.list</td>
<td>This specifies the list of streams to which the source must listen. This list can be provided as a set of comma-separated values e.g. <code>stream_one,stream_two</code></td>
<td></td>
<td>STRING</td>
<td>No</td>
</tr>
<tr class="even">
<td>replication.type</td>
<td>Specifies if the replication type of the streams. Possible values can be <code>local</code> and <code>global</code></td>
<td>local</td>
<td>STRING</td>
<td>Yes</td>
</tr>
</tbody>
</table>

### C8DB

**Syntax**

    @source(type = 'c8db', collection="STRING", replication.type="STRING", collection.type="STRING", @map(...))

**Example**

    @source(type = 'c8db', collection='SweetProductionData', @map(type='json'))
    define stream SweetProductionStream (name string, amount double);

**Query Parameters**
    
<table>
<thead>
<tr class="header">
<th>Name</th>
<th>Description</th>
<th>Default Value</th>
<th>Possible Data Types</th>
<th>Optional</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>collection</td>
<td>This specifies the name of the c8db collection to which the source must listen.</td>
<td></td>
<td>STRING</td>
<td>No</td>
</tr>
<tr class="even">
<td>replication.type</td>
<td>Specifies if the replication type of the c8db collection. Possible values can be <code>local</code> and <code>global</code></td>
<td>local</td>
<td>STRING</td>
<td>Yes</td>
</tr>
<tr class="odd">
<td>collection.type</td>
<td>This specifies the type of the data collection contains. Possible values can be <code>doc</code> and <code>edge</code>.</td>
<td>doc</td>
<td>STRING</td>
<td>Yes</td>
</tr>
</tbody>
</table>

## Sink

### C8Streams

**Syntax**

    @sink(type="c8streams", stream="<STRING>", replication.type="<STRING>", @map(...)))

**Example**
    
    @sink(type= 'c8streams', stream='ProductionAlertStream', @map(type='json'))
    define stream ProductionAlertStream (name string, amount double);

**Query Parameters**

<table>
<thead>
<tr class="header">
<th>Name</th>
<th>Description</th>
<th>Default Value</th>
<th>Possible Data Types</th>
<th>Optional</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>stream</td>
<td>The streams to which the C8Stream sink needs to publish events.</td>
<td></td>
<td>STRING</td>
<td>No</td>
</tr>
<tr class="even">
<td>replication.type</td>
<td>Specifies if the replication type of the stream. Possible values can be <code>local</code> and <code>global</code></td>
<td>local</td>
<td>STRING</td>
<td>Yes</td>
</tr>
</tbody>
</table>

## Table

### C8DB

**Syntax**

    @store(type = 'c8db', collection="STRING", replication.type="STRING", collection.type="STRING", from="STRING", to="STRING")
    

**Example**

    @store(type = 'c8db', collection='SweetProductionCollection')
    define table SweetProductionCollection (name string, amount double);

If `@store` annotation is not provided, c8db is considered as a the default store. Stream application will use the c8db with the default query parameters explained in the chart below. In the above example, store can also be defined as

    define table SweetProductionCollection (name string, amount double);
    
<table>
<thead>
<tr class="header">
<th>Name</th>
<th>Description</th>
<th>Default Value</th>
<th>Possible Data Types</th>
<th>Optional</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>collection</td>
<td>This specifies the name of the c8db collection to which events must written.</td>
<td></td>
<td>STRING</td>
<td>No</td>
</tr>
<tr class="even">
<td>replication.type</td>
<td>Specifies if the replication type of the c8db collection. Possible values can be <code>local</code> and <code>global</code></td>
<td>local</td>
<td>STRING</td>
<td>Yes</td>
</tr>
<tr class="odd">
<td>collection.type</td>
<td>This specifies the type of the data collection contains. Possible values can be <code>doc</code> and <code>edge</code>.</td>
<td>doc</td>
<td>STRING</td>
<td>Yes</td>
</tr>
<tr class="even">
<td>from</td>
<td>If <code>collection.type</code> is specified as <code>edge</code>, this field indicates which field to be considered as a source node of the edge.</td>
<td>_from</td>
<td>STRING</td>
<td>Yes</td>
</tr>
<tr class="odd">
<td>to</td>
<td>If <code>collection.type</code> is specified as <code>edge</code>, this field indicates which field to be considered as a destination node of the edge.</td>
<td>_to</td>
<td>STRING</td>
<td>Yes</td>
</tr>
</tbody>
</table>

## Tutorials

Following tutorials cover various user scenarios using Macrometa Stream Processing.

* [Publishing Data](publishing-data.md)
* [Consuming Data](consuming-data.md)
* [Filtering Data](filtering-data.md)
* [Transforming Data](transforming-data.md)
* [Enriching Data](enriching-data.md)
* [Executing Scripts](executing-scripts.md) -->
* [Correlating Data](correlating-data.md)
* [Summarizing Data](summarizing-data.md)
* [Data Pipelines](routing-data.md)
* [Exposing Data as API](exposing-data-as-api.md)


Please refer to [Reference](../reference/overview.md) for additional stream processing examples.
