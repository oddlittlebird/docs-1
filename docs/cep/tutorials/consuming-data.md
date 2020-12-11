# Consuming Data

## Introduction

Typically the first step  in a stream processing flow is to consume the data to be cleaned, enriched, transformed or summarized to produce the required output.

For the stream processor to consume events, the following is required.

* A **message schema**: To identify the messages to select for stream processing. The schema of the messages is defined via a *stream*.

* A **source**: The messages are consumed from different sources including streaming applications, cloud-based applications, databases, and files. The source is defined via a *source configuration*.

A source configuration consists of the following:
  
- **`@source`**: This annotation defines the source type via which the messages are consumed, and allows you to configure the source parameters (which change depending on the source type). For the complete list of supported source types, see [Streams Query Guide - Source](../../cep/reference/query-guide.md#source)

- **`@map`**: This annotation specifies the format in which messages are consumed, and allows you to configure the mapping parameters (which change based of the mapping type/format selected). For the complete list of supported mapping types, see [Streams Query Guide - Source Mapper](../../cep/reference/query-guide.md#source-mapper)

- **`@attributes`**: This annotation specifies a custom mapping based on which events to be selected into the stream processing flow are identified. This is useful when the attributes of the incoming messages you want the stream processor to consume are different to the corresponding attribute name in the stream definition. 

    !!! example
        Say the stream processor is reading employee records. The employee name might be defined as `emp No` in the source from which you are extracting the records. However, the corresponding attribute name in the stream definition is `employeeNo` because that is how you want to refer to the attribute in the stream processor. In this instance, you need a custom mapping to indicate that `emp No` is the same as `employeeNo`.

## Stream Application for Consuming Data

Following are the steps to create a stream application:

1. Open the `Stream Apps` editor in GUI.

1. Click on **New** to start defining a new stream application.

1. Enter a **Name** for the stream application. For example `SalesTotalsApp`.

1. Enter a **Description**.

1. Define an input stream to define the schema based on which input events are selected to the streaming integrator flow as follows.

    ```
    define stream <Stream_Name>(attribute1_name attribute1_type, attribute2_name attribute2_type, ...)
    ```

    For example,

    ```
    define stream ConsumerSalesTotalsStream (transNo int, product string, price int, quantity int, salesValue long)
    ```

4. Connect a source to the input stream you added as follows.

    ```
    @source(type='<SOURCE_TYPE>')
    define stream <Stream_Name>(attribute1_name attribute1_type, attribute2_name attribute2_type, ...);
    ```

    For example, you can add a source of the `c8streams` type to the `ConsumerSalesTotalsStream` input stream in the example of the previous step.

    ```
    @source(type='c8streams')
    define stream ConsumerSalesTotalsStream (transNo int, product string, price int, quantity int, salesValue long);
    ```
    
5. Configure parameters for the source you added. 

    For example, you can specify the collection name for the `c8streams` source in the example used.
    
    ```
    @source(type='c8streams', stream.list='SalesTotalsEP')
    define stream ConsumerSalesTotalsStream (transNo int, product string, price int, quantity int, salesValue long);
    ```
       
6. Add an `@map` annotation to the source configuration as shown below.

    ```
    @source(type='<SOURCE_TYPE>', <PARAMETER1_NAME>='<PARAMETER1_VALUE>', @map(type='<MAP_TYPE>'))
    define stream <Stream_Name>(attribute1_name attribute1_type, attribute2_name attribute2_type, ...);
    ```

    In the example used, you can specify `JSON` as the map type as follows:

    ```
    @source(type='c8streams', stream.list='SalesTotalsEP', @map(type='json'))
    define stream ConsumerSalesTotalsStream (transNo int, product string, price int, quantity int, salesValue long);
    ```
    
    !!!info
        Mapping is explained in detail in the [Consuming a message in default format](#consume-messages-in-default-format) and [Consuming a message in custom format](#consume-messages-in-custom-format) sections. However, note that you need to add a mapping type to complete a source configuration. If no mapping type i specified, an error is indicated.
        
7.  Add stream query to specify how the output is derived and the name of an output stream to which this output is directed.
    ```sql
    select <ATTRIBUTE1_Name>, <ATTRIBUTE2_NAME>, ... 
    from <INPUT_STREAM_NAME>
    group by <ATTRIBUTE_NAME>
    insert into <OUTPUT_STREAM_NAME>;
    ```
    
    For example, you can define the query as follows.
    ```sql
    select *
    from ConsumerSalesTotalsStream
    group by product
    insert into PublishSalesTotalsStream;
    ```
    
8. Complete the stream application by defining an output stream with a connected sink configuration.

    !!!tip
        In the example used, you can define the `PublishSalesTotals` stream that you already specified as the output stream in the query, and connect a `c8stream` sink to it as follows. Publishing the output is explained in detail in the [Publishing Data guide](publishing-data.md).

    ```
    @sink(type='c8streams', stream='SalesTotals', replication.type='local')
    define stream PublishSalesTotalsStream (transNo int, product string, price int, quantity int, salesValue long);
    ```
        
9. Save the Stream Application. The completed application is as follows:

    ```
    @App:name("SalesTotalsApp")
    @App:description("Description of the plan")

    @source(type='c8stream', stream.list='SalesTotalsEP', stream.type='local', @map(type='json'))
    define stream ConsumerSalesTotalsStream(transNo int, product string, price int, quantity int, salesValue long);

    @sink(type='c8stream', stream.list='SalesTotals')
    define stream PublishSalesTotalsStream (transNo int, product string, price int, quantity int, salesValue long);

    select *
    from ConsumerSalesTotalsStream
    group by product
    insert into PublishSalesTotalsStream;
    ```

10. Publish the stream application to one or more regions via GUI/REST API.

## Supported message formats

### Consume messages in default format

Stream processor consumes a message in the default format when it makes no changes to the names of the attributes of the message schema before it processes the message. To understand how messages are consumed in default format, follow the procedure below.

1. Create a stream application with a source configuration following the instructions in the above subsection.

2. In the source configuration, make sure that an `@map` annotation is included with the mapping type as shown below.

    ```
    @source(type='<SOURCE_TYPE>', <PARAMETER1_NAME>='<PARAMETER1_VALUE>', @map(type='<MAP_TYPE>'))
    define stream <Stream_Name>(attribute1_name attribute1_type, attribute2_name attribute2_type, ...);
    ```
    
    The map type specifies the format in which the messages are received.
    e.g., In the example used, you can specify `JSON` as the map type as follows:
    
    ```
    @source(type='http', receiver.url='http://localhost:5005/SalesTotalsEP', @map(type='json'))
    define stream ConsumerSalesTotalsStream(transNo int, product string, price int, quantity int, salesValue long);
    ```
    
3. Save the stream application. If you save the stream application that was created using the example configurations, the completed stream application is as follows.

    ```
    @App:name("SalesTotalsApp")
    @App:description("Description of the plan")

    @source(type='c8streams', stream.list='SalesTotalsEP', @map(type='json'))
    define stream ConsumerSalesTotalsStream (transNo int, product string, price int, quantity int, salesValue long);

    @sink(type='c8streams', stream='SalesTotals', replication.type='local')
    define stream PublishSalesTotalsStream (product string, totalSale long);

    select product, sum(salesValue) as totalSale
    from ConsumerSalesTotalsStream#window.time(1 min)
    group by product
    insert into PublishSalesTotalsStream;
    ```
    
4. To check whether the above stream application works as expected, publish some messages.

    e.g., In the example used, the source type is c8stream. Therefore, you can publish messages similar to the following:
    
    ```json
    {
        "transNo": 1,
        "product": "DDT",
        "price": 100,
        "quantity": 100,
        "salesValue": 10000
    }
    ```

### Consume messages in custom format

Stream processor consumes a message in the custom format when it makes changes to the names of the attributes of the message schema before it processes the message. To understand how messages are consumed in custom format, follow the procedure below.

!!!info
    For this section, you can edit the same stream application that you saved in the [Consume messages in default format](#consume-messages-in-default-format) subsection.
    
1. Open your stream application with a source configuration.

2. In the `@map` annotation within the source configuration, add the `@attributes` annotation with mappings for different attributes. This can be done in two ways as shown below.

    * Defining attributes as keys and mapping content as values in the following format.

        ```
        @source(type='<SOURCE_TYPE>', <PARAMETER1_NAME>='<PARAMETER1_VALUE>', @map(type='<MAP_TYPE>', @attributes( attributeN='mapping_N', attribute1='mapping_1')))
        define stream <Stream_Name>(attribute1_name attribute1_type, attribute2_name attribute2_type, ...);
        ```
        !!! example 
            In the Stream application used as an example in the previous section, assume that when receiving events, the `transNo` attribute is received as `transaction` and the `salesValue` attribute is received as `sales`.  The mapping type is JSON. therefore, you can  add the mappings as JSONPath expressions.

        | **Stream Attribute Name** | **JSON Event Attribute Name** | **JSONPath Expression** |
        |---------------------------|-------------------------------|-------------------------|
        | `transNo`                 | `transaction`                 | `$.transaction`         |
        | `salesValue`              | `sales`                       | `$.sales`               |
        
        The mapping can be defined as follows.
        
        ```
        @source(type='http', receiver.url='http://localhost:5005/SalesTotalsEP', @map(type='json', @attributes(transNo = '$.transaction', salesValue = '$.sales')))
        define stream ConsumerSalesTotalsStream(transNo int, product string, price int, quantity int, salesValue long);
        ```
        
    * Defining the mapping content of all attributes in the same order as how the attributes are defined in stream definition.

        ```
        @source(type='<SOURCE_TYPE>', <PARAMETER1_NAME>='<PARAMETER1_VALUE>', @map(type='<MAP_TYPE>', @attributes( 'mapping_1', 'mapping_N')))
        define stream <Stream_Name>(attribute1_name attribute1_type, attributeN_name attributeN_type, ...);
        ``` 
        
        e.g., If you consider the same example, mapping can be defined as follows.
              
        ```
        @source(type='http', receiver.url='http://localhost:5005/SalesTotalsEP', @map(type='json', @attributes(transNo = '$.transaction', product = product, quantity = quantity, salesValue = '$.sales')))
        define stream ConsumerSalesTotalsStream(transNo int, product string, price int, quantity int, salesValue long);
        ```
