# Quick Start with Key Value Store

!!! note
    If you are new to Macrometa GDN, we strongly recommend reading **[Essentials](../essentials.md)** of Macrometa GDN.

Macrometa GDN is a `geo-distribued real time coordination-free materialized views engine` supporting multiple data models. You can use GDN as a geo-replicated realtime key-value datastore or key-value database. 

In GDN, each document stored in a collection (aka table) contains a primary key `_key`. The rest of the document is considered as value. In the absence of any additional `secondary indexes` on the collection, the collection behaves like a simple key/value store.

The key-value store has no query languages. The permissible operations are `key lookups` (single & batch gets) and key/value pair `inserts`, `updates` and `deletes`. If no sharding attribute is speficed then `_key` is used for sharding the data. The simplicity of this model makes a key-value store fast, easy to use, scalable, portable and flexible.

You can enable `time_to_live (TTL)` during collection creation and use `expireAt` field to specify the expiration time for each document in the KV collection. 

## Pre-requisite

Let's assume your

* tenant name is `nemo@nautilus.com` and 
* user password is `xxxxxx`.

## Driver download

=== "python"

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

=== "javascript"

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

The first step in using GDN is to establish a connection to a local region. When this code executes, it initializes the server connection to the region URL you sepcified. You can create an API key from the GUI or REST API.

=== "Python"

    ``` py
    from c8 import C8Client

    # Simple Way
    print("--- Connecting to C8")
    client = C8Client(protocol='https', host='gdn.paas.macrometa.io', port=443,
                            email='nemo@nautilus.com', password="xxxxxx",
                            geofabric='_system')

    # OR Using token
    client = C8Client(protocol='https', host='gdn.paas.macrometa.io', port=443,
    token="XXXX")

    # OR Using API Key
    client = C8Client(protocol='https', host='gdn.paas.macrometa.io', port=443,
    apikey="guest.gdn1guest.E3GazUtRolhs9LO36hdwFmXFt938hgjzuka74RFj4zzkiQdl2hsTVVD2qQeSHdX737674d")
    ```

=== "Javascript"

    ``` js
    const jsc8 = require("jsc8");

    // Simple Way
    const client = new jsc8({url: "https://gdn.paas.macrometa.io", token: "", fabricName= '_system'});
    // ----- OR -----
    const client = new jsc8({url: "https://gdn.paas.macrometa.io", apiKey: "guest.gdn1guest.E3GazUtRolhs9LO36hdwFmXFt938hgjzuka74RFj4zzkiQdl2hsTVVD2qQeSHdX737674d", fabricName= '_system'});

    // To use advanced options
    const client = new jsc8("https://gdn.paas.macrometa.io"); 
    ```

## Create Collection

Create a Collection for saving the Key Value Pairs

=== "Python"

    ``` py
    from c8 import C8Client

    key = "guest.gdn1guest.E3GazUtRolhs9LO36hdwFmXFt938hgjzuka74RFj4zzkiQdl2hsTVVD2qQeSHdX737674d"
    collection_name = "students"

    # Create a connection to gdn1
    client = C8Client(protocol='https', host='gdn.paas.macrometa.io', port=443,
    apikey=key)

    # Create a new collection if it does not exist
    if client.has_collection(collection_name):
        print("Collection exists")
    else:
        client.create_collection_kv(name=collection_name)

    ```

=== "Javascript"

    ``` js
    // Add this snippet in previously created main function
    let coll = await client.getKVCollections();
    console.log("Existing Collections: ", coll.result);
    try{
        await client.createKVCollection(collectionName);
        console.log("Collection Created Successfully");
    }
    catch(e){
        console.log("Collection creation did not succeed due to " + e);
    }
    ```

## Insert Key Value Pairs

Insert key value pairs into the collection.

=== "Python"

    ``` py

    from c8 import C8Client

    key = "guest.gdn1guest.E3GazUtRolhs9LO36hdwFmXFt938hgjzuka74RFj4zzkiQdl2hsTVVD2qQeSHdX737674d"
    collection_name = "students"

    # Create a connection to gdn1
    client = C8Client(protocol='https', host='gdn.paas.macrometa.io', port=443,
    apikey=key)
    # Insert Key Value pairs
    data = [
      {
        "_key": "John",
        "value": "Science",
        "expireAt": 0
      },
      {
        "_key": "Alice",
        "value": "Maths",
        "expireAt": 0
      },
      {
        "_key": "Alex",
        "value": "Physics",
        "expireAt": 0
      },
      {
        "_key": "Monika",
        "value": "Chemistry",
        "expireAt": 0
      }
    ]

    client.insert_key_value_pair(collection_name, data)
    print("KV Pairs Inserted")
    ```

=== "Javascript"

    ``` js
    // Insert Key Value pairs
    var data = [
      {
        "_key": "John",
        "value": "Science",
        "expireAt": 0
      },
      {
        "_key": "Alice",
        "value": "Maths",
        "expireAt": 0
      },
      {
        "_key": "Alex",
        "value": "Physics",
        "expireAt": 0
      },
      {
        "_key": "Monika",
        "value": "Chemistry",
        "expireAt": 0
      }
    ]
    try{
        await client.insertKVPairs(collectionName, data);
        console.log("Key Value pairs inserted successfully.");
    }
    catch(e){
        console.log("Key Value Pairs not inserted due to " + e);
    }
    ```

## Get Value

Get value for a given key.

=== "Python"

    ``` py
    from c8 import C8Client

    key = "guest.gdn1guest.E3GazUtRolhs9LO36hdwFmXFt938hgjzuka74RFj4zzkiQdl2hsTVVD2qQeSHdX737674d"
    collection_name = "students"

    # Create a connection to gdn1
    client = C8Client(protocol='https', host='gdn.paas.macrometa.io', port=443,
    apikey=key)
    # Get value for a key
    print("Value for the provided key: ",client.get_value_for_key(collection_name, "Monika"))

    ```

=== "Javascript"

    ``` js
    const result = await client.getValueForKey(collectionName, 'Monika');
    console.log("Value for provided key: ", result);
    ```

## Get KV Count

Get Key-Value count from a given collection.

=== "Python"

    ```py
    from c8 import C8Client

    key = "guest.gdn1guest.E3GazUtRolhs9LO36hdwFmXFt938hgjzuka74RFj4zzkiQdl2hsTVVD2qQeSHdX737674d"
    collection_name = "students"

    # Create a connection to gdn1
    client = C8Client(protocol='https', host='gdn.paas.macrometa.io', port=443,
    apikey=key)

    # Get KV count of a collection
    print("Number of kv pairs in your collection: ",client.get_kv_count(collection_name))

    ```

=== "Javascript"

    ```js

    // Get KV count of a collection
    const count = await client.getKVCount(collectionName);
    console.log("Number of kv pairs in your collection: ", count.count);
    ```

## Update Value

Update value for a given key.

=== "Python"

    ```py

    from c8 import C8Client

    key = "guest.gdn1guest.E3GazUtRolhs9LO36hdwFmXFt938hgjzuka74RFj4zzkiQdl2hsTVVD2qQeSHdX737674d"
    collection_name = "students"

    # Create a connection to gdn1
    client = C8Client(protocol='https', host='gdn.paas.macrometa.io', port=443,
    apikey=key)

    # Update value for a key
    data = {
        "_key": "John",
        "value": "Biology",
        "expireAt": 0
    }
    client.insert_key_value_pair(collection_name, data)
    print("Updated the specified KV pair")

    ```

=== "Javascript"

    ``` js
    //Update value for a key
    data = {
        "_key": "John",
        "value": "Biology",
        "expireAt": 0
    }
    try{
        client.insertKVPairs(collectionName, data)
        console.log("Updated the specified KV pair")
    }
    catch(e){
      console.log("Key Value Pair not updated due to " + e)

    }
    ```

## Delete Key Value

Delete KV pairs from a collection.

=== "Python"

    ``` py
    from c8 import C8Client

    key = "guest.gdn1guest.E3GazUtRolhs9LO36hdwFmXFt938hgjzuka74RFj4zzkiQdl2hsTVVD2qQeSHdX737674d"
    collection_name = "students"

    # Create a connection to gdn1
    client = C8Client(protocol='https', host='gdn.paas.macrometa.io', port=443,
    apikey=key)

    # Delete entry for a key
    print("Deleted Entry for the specified Key: ",client.delete_entry_for_key(collection_name, "John"))

    # Delete entry for multiple keys
    print("Deleted Entries for the list of keys: ",client.delete_entry_for_keys(collection_name, ["Monika", "Alex", "Alice"]))

    ```

=== "Javascript"

    ``` js
    try{
        // Delete entry for a key
        await client.deleteEntryForKey(collectionName, 'John');

        // Delete entries for multiple keys
        await client.deleteEntryForKeys(collectionName, ["Monika", "Alex", "Alice"])
    }
    catch(e){
        console.log("Failed to delete entries due to " + e)

    }
    ```

## Delete Collection

Delete KV collection

=== "Python"

    ``` py
    from c8 import C8Client

    key = "guest.gdn1guest.E3GazUtRolhs9LO36hdwFmXFt938hgjzuka74RFj4zzkiQdl2hsTVVD2qQeSHdX737674d"
    collection_name = "students"

    # Create a connection to gdn1
    client = C8Client(protocol='https', host='gdn.paas.macrometa.io', port=443,
    apikey=key)

    # Delete Collection
    print("Collection Deleted: ",client.delete_collection_kv(collection_name))

    ```

=== "Javascript"

    ``` js
    // Delete Collection
    try{
        await client.deleteKVCollection(collectionName)
        console.log("Collection Deleted")
    }
    catch(e){
        console.log("Failed to delete collection due to " + e)
    }

    ```

## Complete Example

Complete code example written using above code snippets can be found below.

=== "Python"

    ```py

    from c8 import C8Client

    key = "guest.gdn1guest.E3GazUtRolhs9LO36hdwFmXFt938hgjzuka74RFj4zzkiQdl2hsTVVD2qQeSHdX737674d"
    collection_name = "students"

    # Create a connection to gdn1
    client = C8Client(protocol='https', host='gdn.paas.macrometa.io', port=443,
    apikey=key)

    # client = C8Client(protocol='https', host='gdn.paas.macrometa.io', port=443,
    #                          email='nemo@nautilus.com', password="xxxxxx",
    #                          geofabric='_system')

    # OR Using token
    # client = C8Client(protocol='https', host='gdn.paas.macrometa.io', port=443,
    #  token="XXXX")


    # Create a new collection if it does not exist
    if client.has_collection(collection_name):
        print("Collection exists")
    else:
        client.create_collection_kv(name=collection_name)

    # Insert Key Value pairs
    data = [
      {
        "_key": "John",
        "value": "Science",
        "expireAt": 0
      },
      {
        "_key": "Alice",
        "value": "Maths",
        "expireAt": 0
      },
      {
        "_key": "Alex",
        "value": "Physics",
        "expireAt": 0
      },
      {
        "_key": "Monika",
        "value": "Chemistry",
        "expireAt": 0
      }
    ]

    client.insert_key_value_pair(collection_name, data)
    print("KV Pairs Inserted")

    # Get value for a key
    print("Value for the provided key: ",client.get_value_for_key(collection_name, "Monika"))

    # Get KV count of a collection
    print("Number of kv pairs in your collection: ",client.get_kv_count(collection_name))

    # Update value for a key
    data = {
        "_key": "John",
        "value": "Biology",
        "expireAt": 0
    }
    client.insert_key_value_pair(collection_name, data)
    print("Updated the specified KV pair")

    # Delete entry for a key
    print("Deleted Entry for the specified Key: ",client.delete_entry_for_key(collection_name, "John"))

    # Delete entry for multiple keys
    print("Deleted Entries for the list of keys: ",client.delete_entry_for_keys(collection_name, ["Monika", "Alex", "Alice"]))

    # Delete Collection
    print("Collection Deleted: ",client.delete_collection_kv(collection_name))
    ```

=== "Javascript"

    ```js

    const jsc8 = require("jsc8");

    const key = "guest.gdn1guest.E3GazUtRolhs9LO36hdwFmXFt938hgjzuka74RFj4zzkiQdl2hsTVVD2qQeSHdX737674d";
    const collectionName = "students";

    // Connect to gdn1
    const client = new jsc8({url: "https://gdn.paas.macrometa.io", apiKey: key});

    // Crete a authenticated instance with Token / Apikey
    // const client = new jsc8({url: "https://gdn.paas.macrometa.io", token: "XXXX", fabricName= '_system'});
    // const client = new jsc8({url: "https://gdn.paas.macrometa.io", apiKey: "XXXX", fabricName= '_system'});
    // await console.log("Authentication done!!...");

    // Or use Email & Password to Authenticate client instance
    // const client = new jsc8("https://gdn.paas.macrometa.io");

    // await client.login("nemo@nautilus.com", "xxxxx");

    async function main(){
    // Create a Collection  
    let coll = await client.getKVCollections();
    console.log("Existing Collections: ", coll.result);
    try{
        await client.createKVCollection(collectionName);
        console.log("Collection Created Successfully");
    }
    catch(e){
        console.log("Collection creation did not succeed due to " + e);
    }

    // Insert Key Value pairs
    var data = [
      {
        "_key": "John",
        "value": "Science",
        "expireAt": 0
      },
      {
        "_key": "Alice",
        "value": "Maths",
        "expireAt": 0
      },
      {
        "_key": "Alex",
        "value": "Physics",
        "expireAt": 0
      },
      {
        "_key": "Monika",
        "value": "Chemistry",
        "expireAt": 0
      }
    ]
    try{
        await client.insertKVPairs(collectionName, data);
        console.log("Key Value pairs inserted successfully.");
    }
    catch(e){
        console.log("Key Value Pairs not inserted due to " + e);
    }

    // Get value for a key
    const result = await client.getValueForKey(collectionName, 'Monika');
    console.log("Value for provided key: ", result);

    // Get KV count of a collection
    const count = await client.getKVCount(collectionName);
    console.log("Number of kv pairs in your collection: ", count.count);

    //Update value for a key
    data = {
        "_key": "John",
        "value": "Biology",
        "expireAt": 0
    }
    try{
        client.insertKVPairs(collectionName, data);
        console.log("Updated the specified KV pair");
    }
    catch(e){
      console.log("Key Value Pair not updated due to " + e);

    }
    try{
        // Delete entry for a key
        await client.deleteEntryForKey(collectionName, 'John');

        // Delete entries for multiple keys
        await client.deleteEntryForKeys(collectionName, ["Monika", "Alex", "Alice"]);
    }
    catch(e){
        console.log("Failed to delete entries due to " + e);

    }

    // Delete Collection
    try{
        await client.deleteKVCollection(collectionName);
        console.log("Collection Deleted");
    }
    catch(e){
        console.log("Failed to delete collection due to " + e);
    }


    }

    main();
    ```