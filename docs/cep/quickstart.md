# Quick Start with Stream Processing

## Overview

Macrometa GDN allows you to integrate streaming data and take action based on streaming data. Typically the stream processing use cases involve collecting, analyzing and, integrate or acting on data generated during business activities by various sources i.e.,
 
| Stage | Description |
|-------|-------------|
| **Collect** | Receive or capture data from various data sources. |
| **Analyze** | Analyze data to identify interesting patterns and to extract information. |
| **Act** | Take actions based on the results and findings done via processing the data. The action can be executing some random code, calling an external service, or triggering a complex integration. |
| **Integrate** | Make processed data available for consumers to consume globally in right format with very low latencies. |

!!! note
    If you are new to Macrometa GDN, we strongly recommend reading **[Essentials](../essentials.md)** of Macrometa GDN.


![GDN Essentials](images/gdn-cep-overview.png)

The stream processing can be used for

* `Transforming` your data from one format to another (e.g., to/from XML, JSON, AVRO, etc.).
* `Enriching data` received from a specific source by combining it with databases, services, etc., via inline calculations and custom functions.
* `Correlating data` streams by joining multiple streams to create an aggregate stream.
* `Cleaning data` by filtering it and by modifying the content (e.g., obfuscating) in messages.
* `Deriving insights` by identifying interesting patterns and sequences of events in data streams.
* `Summarizing data` as and when it is generated using temporal windows and incremental time series aggregations.
* `Realtime ETL` for Collections, tailing files, scraping HTTP Endpoints, etc.
* `Streaming Integrations` i.e., integrating streaming data as well as trigger actions based on data streams. The action can be a single request to a service or a complex enterprise integration flow.

## Pre-requisite

Let's assume your

* tenant name is `nemo@nautilus.com` and 
* user password is `xxxxxxxx`.

## Driver download

=== "Python"

    ``` py

    pyC8 requires Python 3.5+. Python 3.6 or higher is recommended

    To install pyC8, simply run

        $ pip3 install pyC8

    or, if you prefer to use conda:

        conda install -c conda-forge pyC8

    or pipenv:

        pipenv install --pre pyC8

    Once the installation process is finished, you can begin developing applications in Python.

    ```

=== "Javascript"

    ``` js

    With Yarn or NPM

        yarn add jsc8
        (or)
        npm install jsc8

    If you want to use the driver outside of the current directory, you can also install it globally using the `--global` flag:

        npm install --global jsc8

    From source,

        git clone https://github.com/macrometacorp/jsc8.git
        cd jsC8
        npm install
        npm run dist

    ```

## Connect to GDN

The first step in using GDN is to establish a connection to a local region. When this code executes, it initializes the server connection to the region URL you sepcified.

=== "Python"

    ``` py
    from c8 import C8Client

    print("--- Connecting to C8")
    client = C8Client(protocol='https', host='gdn.paas.macrometa.io', port=443,
                            email='nemo@nautilus.com', password='xxxxx',
                            geofabric='_system')
    ```

=== "Javascript"

    ``` js
    const jsc8 = require("jsc8");

    // Simple Way
    const client = new jsc8({url: "https://gdn.paas.macrometa.io", token: "XXXX", fabricName= '_system'});
    // ----- OR -----
    const client = new jsc8({url: "https://gdn.paas.macrometa.io", apiKey: "XXXX", fabricName= '_system'});

    // To use advanced options
    const client = new jsc8("https://gdn.paas.macrometa.io"); 
    ```

## Validate Stream Application

Validate the Stream Application before saving for any syntax errors.

=== "Python"

    ``` py
    stream_app_definition = """
    @App:name('Sample-Cargo-App')
    @App:description('Basic Stream application to demonstrate reading data from input stream and store it in the collection. The stream and collections will be created automatically if they do not already exist.')

    /**
    Testing the Stream Application:
        1. Open Stream SampleCargoAppDestStream in Console. The output can be monitored here.

        2. Upload following data into SampleCargoAppInputTable C8DB Collection
            {"weight": 1}
            {"weight": 2}
            {"weight": 3}
            {"weight": 4}
            {"weight": 5}

        3. Following messages would be shown on the SampleCargoAppDestStream Stream Console
            [1]
            [2]
            [3]
            [4]
            [5]
    */

    -- Defines Table SampleCargoAppInputTable to process events having sensorId and temperature(F).
    @source(type = 'c8db', collection = "SampleCargoAppInputTable", collection.type="doc", replication.type="global", @map(type='json'))
    define stream SampleCargoAppInputTable (weight int);

    -- Define Stream SampleCargoAppDestStream
    @sink(type = 'c8streams', stream = "SampleCargoAppDestStream", replication.type="local")
    define stream SampleCargoAppDestStream (weight int);

    -- Data Processing
    @info(name='Query')
    select weight
    from SampleCargoAppInputTable
    insert into SampleCargoAppDestStream;
    """

    print("--- Validating Stream Application Definition")
    print(client.validate_stream_app(data=script_app))
    ```

=== "Javascript"

    ``` js
    // Add this snippet in previously created main function
    const appDefinition = `
        @App:name('Sample-Cargo-App')
        @App:description('Basic Stream application to demonstrate reading data from input stream and store it in the collection. The stream and collections will be created automatically if they do not already exist.')

        /**
         Testing the Stream Application:
            1. Open Stream SampleCargoAppDestStream in Console. The output can be monitored here.

            2. Upload following data into SampleCargoAppInputTable C8DB Collection
                {"weight": 1}
                {"weight": 2}
                {"weight": 3}
                {"weight": 4}
                {"weight": 5}

            3. Following messages would be shown on the SampleCargoAppDestStream Stream Console
                [1]
                [2]
                [3]
                [4]
                [5]
        */

        -- Defines Table SampleCargoAppInputTable to process events having sensorId and temperature(F).
        @source(type = 'c8db', collection = "SampleCargoAppInputTable", collection.type="doc", replication.type="global", @map(type='json'))
        define stream SampleCargoAppInputTable (weight int);

        -- Define Stream SampleCargoAppDestStream
        @sink(type = 'c8streams', stream = "SampleCargoAppDestStream", replication.type="local")
        define stream SampleCargoAppDestStream (weight int);

        -- Data Processing
        @info(name='Query')
        select weight
        from SampleCargoAppInputTable
        insert into SampleCargoAppDestStream ;`

    console.log("--- Validating Stream Application Definition");
    result = await client.validateStreamApp(appDefinition);
    ```

## Save Stream Application

=== "Python"

    ``` py
    # The stream app will be created by default in the local region. Optionally, you can send dclist to deploy stream
    # app in all / selected regions
    print("--- Creating Stream Application")
    print(client.create_stream_app(data=script_app))
    ```

=== "Javascript"

    ``` js
    // The stream app will be created by default in the local region. Optionally, you can send dclist to deploy stream
    // app in all / selected regions
    console.log("--- Creating Stream Application");
    result = await client.createStreamApp([], appDefinition);
    ```

## Enable or Disable Stream Application

=== "Python"

    ``` py

    print("Activate", client.activate_stream_app('Sample-Cargo-App', True))

    print("Deactivate", client.activate_stream_app('Sample-Cargo-App', False))

    ```

=== "Javascript"

    ``` js
    console.log("--- Activating `Sample-Cargo-App`");
    const result = await client.activateStreamApp("Sample-Cargo-App", true);

    console.log("--- Deactivating `Sample-Cargo-App`");
    const result = await client.activateStreamApp("Sample-Cargo-App", false);
    ```

To operate on created apps, you need to create an instance of the Stream Application


## Update Stream Application

Let's update an Stream Application to store all the input data into it and another collection called `SampleCargoAppDestTable`. 

=== "Python"

    ``` py

    client = C8Client(protocol='https', host='gdn.paas.macrometa.io', port=443)

    # For the "mytenant" tenant, connect to "test" fabric as tenant admin.
    # This returns an API wrapper for the "test" fabric on tenant 'mytenant'
    # Note that the 'mytenant' tenant should already exist.
    tenant = client.tenant(email='nemo@nautilus.com', password='xxxxx')
    fabric = tenant.useFabric('_system')

    # To operate on created apps, you need to create an instance of the app
    app = fabric.stream_app("Sample-Cargo-App")

    # Update the app using
    data = "@App:name(\'Sample-Cargo-App\')\\n\\n-- Stream\\ndefine stream srcCargoStream (weight int);\\n\\n-- Table\\ndefine table destCargoTable (weight int, totalWeight long);\\n\\n-- Data Processing\\n@info(name=\'Query\')\\nselect weight, sum(weight) as totalWeight\\nfrom srcCargoStream\\ninsert into destCargoTable;"
    regions = []
    result = fabric.update(data,regions)
    print(result)

    ```

=== "Javascript"

    ``` js
    @store(type = 'c8db', stream = "SampleCargoAppDestTable")
    define table SampleCargoAppDestTable (weight int);
    ```

Also add an query to store all the input data into `SampleCargoAppDestTable`.

```js
 -- Data Processing
 @info(name='Dump')
 select weight
 from SampleCargoAppInputTable
 insert into SampleCargoAppDestTable;
```

Now, the code to update an Stream Application will look like

=== "Javascript"

    ``` js
    const updatedAppDefinition = `
        @App:name('Sample-Cargo-App')
        @App:description('Basic Stream application to demonstrate reading data from input stream and store it in the collection. The stream and collections will be created automatically if they do not already exist.')

        /**
        Testing the Stream Application:
            1. Open Stream SampleCargoAppDestStream in Console. The output can be monitored here.

            2. Upload following data into SampleCargoAppInputTable C8DB Collection
                {"weight": 1}
                {"weight": 2}
                {"weight": 3}
                {"weight": 4}
                {"weight": 5}

            3. Following messages would be shown on the SampleCargoAppDestStream Stream Console
                [1]
                [2]
                [3]
                [4]
                [5]

            4. Following messages would be stored into SampleCargoAppDestTable
                {"weight":1}
                {"weight":2}
                {"weight":3}
                {"weight":4}
                {"weight":5}
        */

        -- Defines Table SampleCargoAppInputTable to process events having sensorId and temperature(F).
        @source(type = 'c8db', collection = "SampleCargoAppInputTable", collection.type="doc", replication.type="global", @map(type='json'))
        define stream SampleCargoAppInputTable (weight int);

        -- Define Stream SampleCargoAppDestStream
        @sink(type = 'c8streams', stream = "SampleCargoAppDestStream", replication.type="local")
        define stream SampleCargoAppDestStream (weight int);

        @store(type = 'c8db', stream = "SampleCargoAppDestTable")
        define table SampleCargoAppDestTable (weight int);

        -- Data Processing
        @info(name='Query')
        select weight
        from SampleCargoAppInputTable
        insert into SampleCargoAppDestStream;

        -- Data Processing
        @info(name='Dump')
        select weight
        from SampleCargoAppInputTable
        insert into SampleCargoAppDestTable;`

    console.log("--- Updating Stream Application `Sample-Cargo-App`");
    result = await app.updateApplication([], updatedAppDefinition);
    ```

## Run an Adhoc Query

Available in the advanced operations of python driver.
Refer example at the end of the page.

=== "Python"

    ``` py

    client = C8Client(protocol='https', host='gdn.paas.macrometa.io', port=443)

    # For the "mytenant" tenant, connect to "test" fabric as tenant admin.
    # This returns an API wrapper for the "test" fabric on tenant 'mytenant'
    # Note that the 'mytenant' tenant should already exist.
    tenant = client.tenant(email='nemo@nautilus.com', password='xxxxx')
    fabric = tenant.useFabric('_system')

    # To operate on created apps, you need to create an instance of the app
    app = fabric.stream_app("Sample-Cargo-App")

    # fire query on app using
    q = "select * from SampleCargoAppDestTable limit 3"
    result = app.query(q)
    print(result)
    ```

=== "Javascript"

    ``` js
    console.log("--- Running adhoc query on the store `SampleCargoAppDestTable` used in Stream Application. It should get all records which you inserted into `SampleCargoAppInputTable`");
    await app.activateStreamApplication(true);
    const q = "select * from SampleCargoAppDestTable limit 3";
    result = await app.query(q);
    console.log(result);
    ```

## Delete Stream Application

=== "Python"

    ``` py
    print("--- Deleting Stream Application `Sample-Cargo-App`")
    result = client.delete_stream_app('Sample-Cargo-App')
    ```

=== "Javascript"

    ``` js
    console.log("--- Deleting Stream Application `Sample-Cargo-App`");
    result = await client.deleteStreamApp()'Sample-Cargo-App';
    ```

## Get Sample Stream Apps

You can try out several Stream Apps which are pre-loaded and ready to run.

=== "Python"

    ``` py
    print("--- You can try out several Stream Apps which are pre-loaded and ready to run.")
    print("Samples", client.get_stream_app_samples())

    ```

=== "Javascript"

    ``` js
    console.log("--- You can try out several Stream Apps which are pre-loaded and ready to run.");
    result = await client.getStreamAppSamples();
    console.log('Sample Stream Applications');
    console.log(result);
    ```

## Complete Example

Complete code example written using above code snippets can be found below.
There are two ways in which you can use the python driver, both are shown below.

=== "Python"

    ``` py

    import time
    import traceback
    from c8 import C8Client

    # Simple Approach
    print("--- Connecting to C8")
    client = C8Client(protocol='https', host='gdn.paas.macrometa.io', port=443,
                            email='nemo@nautilus.com', password='xxxxx',
                            geofabric='_system')

    stream_app_definition = """
        @App:name('Sample-Cargo-App')
        @App:description('Basic Stream application to demonstrate reading data from input stream and store it in the collection. The stream and collections will be created automatically if they do not already exist.')

        /**
        Testing the Stream Application:
            1. Open Stream SampleCargoAppDestStream in Console. The output can be monitored here.

            2. Upload following data into SampleCargoAppInputTable C8DB Collection
                {"weight": 1}
                {"weight": 2}
                {"weight": 3}
                {"weight": 4}
                {"weight": 5}

            3. Following messages would be shown on the SampleCargoAppDestStream Stream Console
                [1]
                [2]
                [3]
                [4]
                [5]
        */

        -- Defines Table SampleCargoAppInputTable to process events having sensorId and temperature(F).
        @source(type = 'c8db', collection = "SampleCargoAppInputTable", collection.type="doc", replication.type="global", @map(type='json'))
        define stream SampleCargoAppInputTable (weight int);

        -- Define Stream SampleCargoAppDestStream
        @sink(type = 'c8streams', stream = "SampleCargoAppDestStream", replication.type="local")
        define stream SampleCargoAppDestStream (weight int);

        -- Data Processing
        @info(name='Query')
        select weight
        from SampleCargoAppInputTable
        insert into SampleCargoAppDestStream;
        """
    # Create a stream application
    print(client.create_stream_app(data=stream_app_definition))
    # Validate a stream application
    print(client.validate_stream_app(data=stream_app_definition))
    # Retrive a stream application
    print("Retrive", client.retrieve_stream_app())
    # Get a stream application handle for advanced operations
    print("Get App", client.get_stream_app('Sample-Cargo-App'))
    # Deactivate a stream application
    print("Deactivate", client.activate_stream_app('Sample-Cargo-App', False))
    # Activate a stream application
    print("Activate", client.activate_stream_app('Sample-Cargo-App', True))
    # Delete a stream application
    print(client.delete_stream_app('Sample-Cargo-App'))
    # Get stream application samples
    print("Samples", client.get_stream_app_samples())

    # Object Orientd Approach

    try:
        print("--- Connecting to C8")
        client = C8Client(protocol='https', host='gdn.paas.macrometa.io', port=443)

        demotenant = client.tenant(email='nemo@nautilus.com', password='xxxxx')
        print("--- Get geo fabric details")
        fabric = demotenant.useFabric('_system')
        print(fabric.fabrics_detail())

        stream_app_definition = """
        @App:name('Sample-Cargo-App')
        @App:description('Basic Stream application to demonstrate reading data from input stream and store it in the collection. The stream and collections will be created automatically if they do not already exist.')

        /**
        Testing the Stream Application:
            1. Open Stream SampleCargoAppDestStream in Console. The output can be monitored here.

            2. Upload following data into SampleCargoAppInputTable C8DB Collection
                {"weight": 1}
                {"weight": 2}
                {"weight": 3}
                {"weight": 4}
                {"weight": 5}

            3. Following messages would be shown on the SampleCargoAppDestStream Stream Console
                [1]
                [2]
                [3]
                [4]
                [5]
        */

        -- Defines Table SampleCargoAppInputTable to process events having sensorId and temperature(F).
        @source(type = 'c8db', collection = "SampleCargoAppInputTable", collection.type="doc", replication.type="global", @map(type='json'))
        define stream SampleCargoAppInputTable (weight int);

        -- Define Stream SampleCargoAppDestStream
        @sink(type = 'c8streams', stream = "SampleCargoAppDestStream", replication.type="local")
        define stream SampleCargoAppDestStream (weight int);

        -- Data Processing
        @info(name='Query')
        select weight
        from SampleCargoAppInputTable
        insert into SampleCargoAppDestStream;
        """

        print("--- Validating Stream Application Definition")
        result = fabric.validate_stream_app(stream_app_definition)
        assert result is not False, "Stream Application definition is invalid."

        # The stream app will be created by default in the local region. Optionally, you can send dclist to deploy stream
        # app in all / selected regions
        print("--- Creating Stream Application")
        result = fabric.create_stream_app(stream_app_definition, dclist=[])
        assert result is not False, "Unable to create Stream Application"

        print("--- Getting Stream Application instance `Sample-Cargo-App`")
        app = fabric.stream_app("Sample-Cargo-App")
        result = app.get()
        assert result is not False, "Unable to access Stream App `Sample-Cargo-App`"

        updated_stream_app_definition = """
            @App:name('Sample-Cargo-App')
            @App:description('Basic Stream application to demonstrate reading data from input stream and store it in the collection. The stream and collections will be created automatically if they do not already exist.')
        
            /**
            Testing the Stream Application:
                1. Open Stream SampleCargoAppDestStream in Console. The output can be monitored here.
        
                2. Upload following data into SampleCargoAppInputTable C8DB Collection
                    {"weight": 1}
                    {"weight": 2}
                    {"weight": 3}
                    {"weight": 4}
                    {"weight": 5}
        
                3. Following messages would be shown on the SampleCargoAppDestStream Stream Console
                    [1]
                    [2]
                    [3]
                    [4]
                    [5]
        
                4. Following messages would be stored into SampleCargoAppDestTable
                    {"weight":1}
                    {"weight":2}
                    {"weight":3}
                    {"weight":4}
                    {"weight":5}
            */
        
            -- Defines Table SampleCargoAppInputTable to process events having sensorId and temperature(F).
            @source(type = 'c8db', collection = "SampleCargoAppInputTable", collection.type="doc", replication.type="global", @map(type='json'))
            define stream SampleCargoAppInputTable (weight int);
        
            -- Define Stream SampleCargoAppDestStream
            @sink(type = 'c8streams', stream = "SampleCargoAppDestStream", replication.type="local")
            define stream SampleCargoAppDestStream (weight int);
        
            @store(type = 'c8db', stream = "SampleCargoAppDestTable")
            define table SampleCargoAppDestTable (weight int);
        
            -- Data Processing
            @info(name='Query')
            select weight
            from SampleCargoAppInputTable
            insert into SampleCargoAppDestStream;
        
            -- Data Processing
            @info(name='Dump')
            select weight
            from SampleCargoAppInputTable
            insert into SampleCargoAppDestTable;
        """
        print("--- Updating Stream Application `Sample-Cargo-App`")
        result = app.update(updated_stream_app_definition, dclist=[])
        assert result is not False, "Unable to update Stream Application `Sample-Cargo-App`"

        print("--- Enable Stream Application `Sample-Cargo-App`")
        # Enable / Disable app using change_state function
        # pass True to enable and False to disable the app
        result = app.change_state(True)
        assert result is not False, "Unable to enable Stream Application `Sample-Cargo-App`"

        time.sleep(5)
        input("Insert some documents into `SampleCargoAppInputTable` and press enter to continue...")

        print("--- Running adhoc query on the store `SampleCargoAppDestTable` used in Stream Application. "
              "It should get all records which you inserted into `SampleCargoAppInputTable`")
        q = "select * from SampleCargoAppDestTable limit 3"
        result = app.query(q)
        assert result is not False, "Unable run query on Stream Application `Sample-Cargo-App`"
        print(result['records'])

        print("--- Deleting Stream Application `Sample-Cargo-App`")
        result = app.delete()
        assert result is not False, "Unable to delete Stream Application `Sample-Cargo-App`"

        print("--- You can try out several Stream Apps which are pre-loaded and ready to run.")
        result = fabric.get_samples_stream_app()
        assert result is not False, "Unable to load Sample Stream Applications"
        print('Sample Stream Applications')
        print(result)

    except Exception as e:
        traceback.print_exc()
    ```

=== "Javascript"

    ``` js
    const jsc8 = require("jsc8");
    console.log("--- Connecting to C8");

    // Crete a authenticated instance with Token / Apikey
    // const client = new jsc8({url: "https://gdn.paas.macrometa.io", token: "XXXX", fabricName= '_system'});
    // const client = new jsc8({url: "https://gdn.paas.macrometa.io", apiKey: "XXXX", fabricName= '_system'});
    // await console.log("Authentication done!!...");

    // Or use Email & Password to Authenticate client instance
    const client = new jsc8("https://gdn.paas.macrometa.io");

    await client.login("nemo@nautilus.com", "xxxxxx");

    async function main() {
        try {
            console.log("--- Get geo fabric details");
            let result = await client.get();

            const appDefinition =
                `@App:name('Sample-Cargo-App')
            @App:description('Basic Stream application to demonstrate reading data from input stream and store it in the collection. The stream and collections will be created automatically if they do not already exist.')

            /**
             Testing the Stream Application:
                1. Open Stream SampleCargoAppDestStream in Console. The output can be monitored here.

                2. Upload following data into SampleCargoAppInputTable C8DB Collection
                    {"weight": 1}
                    {"weight": 2}
                    {"weight": 3}
                    {"weight": 4}
                    {"weight": 5}

                3. Following messages would be shown on the SampleCargoAppDestStream Stream Console
                    [1]
                    [2]
                    [3]
                    [4]
                    [5]
            */

            -- Defines Table SampleCargoAppInputTable to process events having sensorId and temperature(F).
            @source(type = 'c8db', collection = "SampleCargoAppInputTable", collection.type="doc", replication.type="global", @map(type='json'))
            define stream SampleCargoAppInputTable (weight int);

            -- Define Stream SampleCargoAppDestStream
            @sink(type = 'c8streams', stream = "SampleCargoAppDestStream", replication.type="local")
            define stream SampleCargoAppDestStream (weight int);

            -- Data Processing
            @info(name='Query')
            select weight
            from SampleCargoAppInputTable
            insert into SampleCargoAppDestStream ;`

            console.log("--- Validating Stream Application Definition");
            result = await client.validateStreamApp(appDefinition);

            // The stream app will be created by default in the local region. Optionally, you can send dclist to deploy stream
            // app in all / selected regions
            console.log("--- Creating Stream Application");
            result = await client.createStreamApp([], appDefinition);

            console.log("--- Getting Stream Application instance `Sample-Cargo-App`");
            ressult = await client.getStreamApp("Sample-Cargo-App");

            console.log("--- Enable Stream Application `Sample-Cargo-App`");
            // Enable / Disable app using change_state function
            // pass true to enable and false to disable the app
            result = await client.activateStreamApp("Sample-Cargo-App", true);

            const updatedAppDefinition = `
            @App:name('Sample-Cargo-App')
            @App:description('Basic Stream application to demonstrate reading data from input stream and store it in the collection. The stream and collections will be created automatically if they do not already exist.')
      
            /**
            Testing the Stream Application:
                1. Open Stream SampleCargoAppDestStream in Console. The output can be monitored here.
      
                2. Upload following data into SampleCargoAppInputTable C8DB Collection
                    {"weight": 1}
                    {"weight": 2}
                    {"weight": 3}
                    {"weight": 4}
                    {"weight": 5}
      
                3. Following messages would be shown on the SampleCargoAppDestStream Stream Console
                    [1]
                    [2]
                    [3]
                    [4]
                    [5]
      
                4. Following messages would be stored into SampleCargoAppDestTable
                    {"weight":1}
                    {"weight":2}
                    {"weight":3}
                    {"weight":4}
                    {"weight":5}
            */
      
            -- Defines Table SampleCargoAppInputTable to process events having sensorId and temperature(F).
            @source(type = 'c8db', collection = "SampleCargoAppInputTable", collection.type="doc", replication.type="global", @map(type='json'))
            define stream SampleCargoAppInputTable (weight int);
      
            -- Define Stream SampleCargoAppDestStream
            @sink(type = 'c8streams', stream = "SampleCargoAppDestStream", replication.type="local")
            define stream SampleCargoAppDestStream (weight int);
      
            @store(type = 'c8db', stream = "SampleCargoAppDestTable")
            define table SampleCargoAppDestTable (weight int);
      
            -- Data Processing
            @info(name='Query')
            select weight
            from SampleCargoAppInputTable
            insert into SampleCargoAppDestStream;
      
            -- Data Processing
            @info(name='Dump')
            select weight
            from SampleCargoAppInputTable
            insert into SampleCargoAppDestTable;
      `
            const app = client.streamApp("Sample-Cargo-App");
            ressult = await app.retriveApplication();

            console.log("--- Updating Stream Application `Sample-Cargo-App`");
            result = await app.updateApplication([], updatedAppDefinition);

            console.log("--- Running adhoc query on the store `SampleCargoAppDestTable` used in Stream Application. It should get all records which you inserted into `SampleCargoAppInputTable`");
            const q = "select * from SampleCargoAppDestTable limit 3";
            result = await app.query(q);
            console.log(result);

            console.log("--- Deleting Stream Application `Sample-Cargo-App`");
            result = await app.deleteApplication();

            console.log("--- You can try out several Stream Apps which are pre-loaded and ready to run.");
            result = await client.getStreamAppSamples();
            console.log('Sample Stream Applications');
            console.log(result);
            
        } catch (e) {
            console.log(e);
        }
    }

    main();
    ```