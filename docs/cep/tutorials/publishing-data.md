# Publishing Data

## Introduction

Once information is processed by the stream processor, the output is presented as events in a streaming manner. This output can be published to databases, files, cloud-based applications or other streaming applications.

For the stream processor to publish events, the following is required.

* A `message schema`: The messages selected to be published by a streaming integration flow are identified by their 
schemas. This schema is defined via an output stream definition. 

* A `sink`: The output can be published to different interfaces including streaming applications, cloud-based applications, databases, and files. There are different sink types to support the different interfaces. The output can also be published in a range of formats. In order to select the required interface and format for a specific streaming integration flow, you need to configure a sink in the relevant stream application via the `@sink` annotation.
 
A sink configuration consists of three parts.
 
   + **`@sink`**: This annotation defines the sink type via which the data is published, and allows you to configure the sink parameters (which change depending on the sink type). For the complete list of supported sink types, see [Stream Query Guide - Sink](../../reference/cep/query-guide/#sink).
   
   + **`@map`**: This annotation specifies the format in which the data is published, and allows you to configure the mapping parameters (which change based of the mapping type/format selected). For the complete list of supported mapping types, see [Stream Query Guide - Sink Mapper](../../reference/cep/query-guide/#sink-mapper).
   
   + **`@attributes`**: This annotation specifies a custom mapping based on which events in the streaming integration flow that need to be published are identified. This is useful when the attributes of the output messages you want the Streaming Integrator to publish are different to the corresponding attribute name in the stream definition. e.g., In a scenario where the Streaming Integrator is publishing the average temperature per second, the temperature can be referred to as  `avgTemp` in the output stream definition in your Stream application. However, you want to publish it with the `Temperature` to the streaming application to which you are publishing. In this instance, you need a custom mapping to indicate that `Temperature` is the same as `avgTemp`.
   

## Publishing data using an event sink

This section explains how to configure a basic sink without mapping. 

### Defining event sink in the stream application

To create a stream application with the sink configuration defined inline, follow the steps below.

1. Open the GUI and start creating a new Stream application. For more information, see [Creating a Stream Application](create-stream-app.md).

2. Enter a name for the stream application as shown below.
    ```
    @App:name("<Stream_Application_Name>)
    ```

    example:
    ```
    @App:name("SalesTotalsApp")
    ```
   
3. Define the output stream based on the schema in which you want to publish data. The format is as follows.
    ```
    define stream <Stream_Name>(attribute1_name attribute1_type, attribute2_name attribute2_type, ...);
    ```

    example: 
    ```
    define stream PublishSalesTotalsStream (transNo int, product string, price int, quantity int, salesValue long);
    ```
   
4. Connect a sink to the stream definition you added as follows.
    ```
    @sink(type='<SINK_TYPE>')

    define stream <Stream_Name>(attribute1_name attribute1_type, attribute2_name attribute2_type, ...);
    ```
    
    Here, the sink type needs to be selected based on the interface to which you want to publish the output. For more information, see [Supported sink types](#supported-event-sink-types). <br/>
    
    Example: If you want to publish the output as messages, you can add a sink with `c8streams` as the type.
    ```
    @sink(type='c8streams', stream.list="PublishSalesTotalsStream")
    define stream PublishSalesTotalsStream (transNo int, product string, price int, quantity int, salesValue long);
    ```
    
5. Add and configure parameters related to the sink type you selected as shown below.
    ```
    @sink(type='<SINK_TYPE>', <PARAMETER1_NAME>='<PARAMETER1_VALUE>', ...)
    define stream <Stream_Name>(attribute1_name attribute1_type, attribute2_name attribute2_type, ...);
    ```
    
    e.g., By adding a parameter named `prefix` to the log sink used as an example in the previous step, you can specify a prefix with which you want to see the output logs printed.
    ```
    @sink(type='c8streams', stream.list='Sales Totals')
    define stream PublishSalesTotalsStream (transNo int, product string, price int, quantity int, salesValue long);
    ```
    
6. Now let's complete adding the required stream app constructs to receive and process the input data.

    1. Add an input stream with a connected source configuration as shown below.

        e.g., Assuming that the schema of the input events are same as that of the output events, and that they are received via C8DB collection, you can add the input stream definition
        ```
        @source(type='c8db', collection='SweetProductionEP')
        define stream ConsumeSalesTotalsStream (transNo int, product string, price int, quantity int, salesValue long);
        ```
       
    2. Add a query to get the received events from the input stream and direct them to the output stream as follows.
        ```
        from <INPUT_STREAM_NAME>
        select <ATTRIBUTE1_Name>, <ATTRIBUTE2_NAME>, ... 
        group by <ATTRIBUTE_NAME>
        insert into <OUTPUT_STREAM_NAME>;
        ```
        e.g., Assuming that you are publishing the events with the existing values as logs in the output console without any further processing, you can define the query as follows.
        
        ```
        select *
        from ConsumeSalesTotalsStream
        group by product
        insert into PublishSalesTotalsStream;
        ``` 
   
7. Save the stream application.


## Supported event sink types

TBD

## Supported event formats

### Publishing data in default format

Stream processor publishes events in default format when it does not make any changes to the attribute names in the output stream before publishing. To understand how this is done, follow the procedure below:

1. Create a stream application with a sink configuration following the instructions in the [Defining event sink in the Stream application](#defining-event-sink-in-the-stream-application) section.

2. Add an `@map` annotation with the mapping type to the sink configuration as shown below.

    ```
    @sink(type='<SINK_TYPE>', @map(type='MAP_TYPE'))
    define stream <Stream_Name>(attribute1_name attribute1_type, attribute2_name attribute2_type, ...);
    ```
    
    The map type specifies the format in which the events are published. e.g., In the example that you used, you can 
    specify the output logs to be printed in the text format by specifying `text` as the mapping type.
    
    ```
    @sink(type='c8streams', stream.list='Sales Totals', @map(type=text))
    define stream PublishSalesTotalsStream (transNo int, product string, price int, quantity int, salesValue long);
    ```
    
3. Save the stream application. If you save the stream application that was created using the example configurations, 
the completed stream application is as follows.

    ```
    @App:name("SalesTotalsApp")
    @App:description("Description of the plan")
    
    @source(type='c8db', collection.name='SalesTotalsEP', @map(type='json'))
    define stream ConsumerSalesTotalsStream(transNo int, product string, price int, quantity int, salesValue long);
    
    @sink(type='c8stream', stream.list='Sales Totals', @map(type=text))
    define stream PublishSalesTotalsStream (transNo int, product string, price int, quantity int, salesValue long);
    
    select transNo, product, price, quantity, salesValue
    from ConsumerSalesTotalsStream
    group by product
    insert into PublishSalesTotalsStream;
    ```

### Publishing data in custom format

Stream processor publishes data in the custom format when it makes changes to the attribute names in the output stream before publishing. To understand how this is done, follow the procedure below:

!!! info
    In this section, you can update the same stream application that you saved in the [Publishing data in default format](#publishing-a-message-in-default-format) section.
    
1. Open your stream application with a sink configuration.

2. Within the `@map` annotation of the sink configuration, add an `@payload` annotation. There are two ways to configure this as follows:

    * Some mappers such as `xml`, `json`, and `text` accept only one output payload using the following format: 
        ```
        @payload( '<PAYLOAD>' )
        ```
        
        e.g., In the example, the mapping type is `text`. Therefore, you can add a message to be printed with the output by configuring the `@payload` annotation as follows.
     
        ```
        @payload( 'This is a test message from {{user}}.' )
        ```
     
    * Some mappers such as `key-value` accept series of mapping values defined as follows: 
        ```
        @payload( key1='mapping_1', 'key2'='user : {{user}}') 
        ```
        
3. Save the stream application.
