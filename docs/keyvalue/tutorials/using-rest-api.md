# Getting Started

!!! note
    If you are new to Macrometa GDN, we strongly recommend reading **[Essentials](../../essentials.md)** of Macrometa GDN.

Macrometa GDN is a `geo-distribued real time coordination-free materialized views engine` supporting multiple data models. You can use GDN as a geo-replicated realtime key-value datastore or key-value database. 

In GDN, each document stored in a collection (aka table) contains a primary key `_key`. The rest of the document is considered as value. In the absence of any additional `secondary indexes` on the collection, the collection behaves like a simple key/value store.

The key-value store has no query languages. The permissible operations are `key lookups` (single & batch gets) and key/value pair `inserts`, `updates` and `deletes`. If no sharding attribute is speficed then `_key` is used for sharding the data. The simplicity of this model makes a key-value store fast, easy to use, scalable, portable and flexible.

You can enable `time_to_live (TTL)` during collection creation and use `expireAt` field to specify the expiration time for each document in the KV collection. 

## Pre-requisite

Let's assume your

* Tenant name is `nemo@nautilus.com` and 
* User password is `xxxxxx`.

## API Browser

Your best friend when working with REST APIs is the REST API browser available in [GDN](https://gdn.paas.macrometa.io) GUI. From there, you can execute various rest apis and see exactly what the inputs and outputs are.

![GDN API Browser](/images/gdn-api-browser.png)

## Connect to GDN

=== "Python"

    ```py
    import requests
    import json

    # Constants

    FEDERATION = "api-gdn.macrometa.io"
    FED_URL = "https://{}".format(FEDERATION)
    EMAIL = "nemo@nautilus.com"
    PASSWORD = "xxxxxx"
    FABRIC = "_system"
    AUTH_TOKEN = "bearer "

    # Create a HTTPS Session

    url = "{}/_open/auth".format(FED_URL)
    payload = {
        'email':EMAIL,
        'password':PASSWORD
        }
    headers = {
        'content-type': 'application/json'
        }

    response = requests.post(url, data = json.dumps(payload), headers = headers)

    if response.status_code == 200:
        resp_body = json.loads(response.text)
        AUTH_TOKEN += resp_body["jwt"]
        TENANT = resp_body["tenant"]
    else:
        raise Exception("Error while getting auth token. Code:{}, Reason:{}".format(response.status_code,response.reason))


    session = requests.session()
    session.headers.update({"content-type": 'application/json'})
    session.headers.update({"authorization": AUTH_TOKEN})
    ```

## Create Collection

Create a KV collection.

=== "Python"

    ```py
    # Create a collection

    url = FED_URL + "/_api/kv/" + COLLECTION_NAME

    resp = session.post(url, data = json.dumps(payload))
    resp = json.loads(resp.text)
    if "error" in resp.keys():
        print("ERROR: " + resp['errorMessage'])
    else:
        print("\nCollection Created: ", resp.text)


    ```

## Insert KV Pairs

Insert Key Value pairs into collection.

=== "Python"

    ```py

    # Insert KV pairs in a Collection
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

    url = FED_URL + "/_api/kv/" + COLLECTION_NAME +"/value"
    print(url)
    resp = session.put(url, data = json.dumps(data))
    print("\nMultiple Documents Inserted: ", resp.text)

    ```

## Get Value 

Get value for a given key.

=== "Python"

    ```py
    # Get Value for a given key

    KEY = "Monika"
    url = FED_URL + "/_api/kv/" + COLLECTION_NAME + "/value/" + KEY
    resp = session.get(url)
    print("\nDocument with specified Key is: ",resp.text)

    ```

## Get Count

Get size of the kv collection.

=== "Python"

    ```py
    # Get Collection Count

    url = FED_URL + "/_api/kv/" + COLLECTION_NAME + "/count"
    resp = session.get(url)
    print("\nNumber of kv pairs in your collection: ",resp.text)
    ```

## Update Value

Update value for a given key in the collection.


=== "Python"

    ```py
    # Update value for a key
    data =  {
        "_key": "Monika",
        "value": "Biology",
        "expireAt": 0
      }
    url = FED_URL + "/_api/kv/" + COLLECTION_NAME +"/value"
    resp = session.put(url, data = json.dumps(data))
    print("\nDocument Updated: ", resp.text)
    ```

## Delete Value

Delete value for a given key.

=== "Python"

    ```py
    # Delete Value for a Key
    url = FED_URL + "/_api/kv/" + COLLECTION_NAME +"/value/" + KEY
    resp = session.delete(url)
    print("\nDocument with specified Key Deleted: ", resp.text)

    # Delete Value for multiple Keys
    data = ["Alex", "Alice", "John"]
    url = FED_URL + "/_api/kv/" + COLLECTION_NAME +"/values"
    resp = session.delete(url, data = json.dumps(data))
    print("\nDocument with specified Key Deleted: ", resp.text)

    ```

## Get Collections

Get collections.

=== "Python"

    ```py
    # Get Collections
    url = FED_URL + "/_api/kv"
    resp = session.get(url)
    print("\nCollections : ",resp.text)

    ```

## Delete Collections

Delete collection.

=== "Python"

    ```py
    # Delete Collection

    url = FED_URL + "/_api/kv/" + COLLECTION_NAME 
    resp = session.delete(url)
    print("\nCollection Deleted: ", resp.text)

    ```

## Complete example

=== "Python"

    ```py
    import requests
    import json

    # Constants

    FEDERATION = "api-gdn.macrometa.io"
    FED_URL = "https://{}".format(FEDERATION)
    EMAIL = "nemo@nautilus.com"
    PASSWORD = "xxxxxx"
    FABRIC = "_system"
    AUTH_TOKEN = "bearer "
    COLLECTION_NAME = "students"

    # Create a HTTPS Session

    url = "{}/_open/auth".format(FED_URL)
    payload = {
        'email':EMAIL,
        'password':PASSWORD
        }
    headers = {
        'content-type': 'application/json'
        }

    response = requests.post(url, data = json.dumps(payload), headers = headers)

    if response.status_code == 200:
        resp_body = json.loads(response.text)
        AUTH_TOKEN += resp_body["jwt"]
        TENANT = resp_body["tenant"]
    else:
        raise Exception("Error while getting auth token. Code:{}, Reason:{}".format(response.status_code,response.reason))


    session = requests.session()
    session.headers.update({"content-type": 'application/json'})
    session.headers.update({"authorization": AUTH_TOKEN})

    # Get List of all regions

    url = FED_URL + "/_api/datacenter/all"
    dcl_resp = session.get(url)
    dcl_list = json.loads(dcl_resp.text)
    regions = []
    for dcl in dcl_list:
        dcl_url = dcl['tags']['url']
        regions.append(dcl_url)
    print("\nList of Regions: ",regions)

    # Create a collection

    url = FED_URL + "/_api/kv/" + COLLECTION_NAME

    resp = session.post(url, data = json.dumps(payload))
    resp = json.loads(resp.text)
    if "error" in resp.keys():
        print("ERROR: " + resp['errorMessage'])
    else:
        print("\nCollection Created: ", resp.text)

    # Insert KV pairs in a Collection
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

    url = FED_URL + "/_api/kv/" + COLLECTION_NAME +"/value"
    print(url)
    resp = session.put(url, data = json.dumps(data))
    print("\nMultiple Documents Inserted: ", resp.text)

    # Get Value for a given key

    KEY = "Monika"
    url = FED_URL + "/_api/kv/" + COLLECTION_NAME + "/value/" + KEY
    resp = session.get(url)
    print("\nDocument with specified Key is: ",resp.text)

    # Get Collection Count

    url = FED_URL + "/_api/kv/" + COLLECTION_NAME + "/count"
    resp = session.get(url)
    print("\nNumber of kv pairs in your collection: ",resp.text)

    # Update value for a key
    data =  {
        "_key": "Monika",
        "value": "Biology",
        "expireAt": 0
      }
    url = FED_URL + "/_api/kv/" + COLLECTION_NAME +"/value"
    resp = session.put(url, data = json.dumps(data))
    print("\nDocument Updated: ", resp.text)

    # Delete Value for a Key
    url = FED_URL + "/_api/kv/" + COLLECTION_NAME +"/value/" + KEY
    resp = session.delete(url)
    print("\nDocument with specified Key Deleted: ", resp.text)

    # Delete Value for multiple Keys
    data = ["Alex", "Alice", "John"]
    url = FED_URL + "/_api/kv/" + COLLECTION_NAME +"/values"
    resp = session.delete(url, data = json.dumps(data))
    print("\nDocument with specified Key Deleted: ", resp.text)

    # Get Collections
    url = FED_URL + "/_api/kv"
    resp = session.get(url)
    print("\nCollections : ",resp.text)


    # Delete Collection

    url = FED_URL + "/_api/kv/" + COLLECTION_NAME 
    resp = session.delete(url)
    print("\nCollection Deleted: ", resp.text)

    ```

=== "Javascript"

    ```js 
    class APIRequest {
      _headers = {
      Accept: "application/json",
      "Content-Type": "application/json",
      };
      
      constructor(url) {
      this._url = url;
      }
      
      login(email, password) {
      const endpoint = "/_open/auth";
      
      const self = this;
      
      return new Promise(function (resolve, reject) {
      self
      .req(endpoint, {
      body: { email, password },
      method: "POST",
      })
      .then(({ jwt, ...data }) => {
      self._headers.authorization = bearer ${jwt};
      resolve(data);
      })
      .catch(reject);
      });
      }
      
      _handleResponse(response, resolve, reject) {
      if (response.ok) {
      resolve(response.json());
      } else {
      reject(response);
      }
      }
      
      req(endpoint, { body, ...options } = {}) {
      const self = this;
      return new Promise(function (resolve, reject) {
      fetch(self._url + endpoint, {
      headers: self._headers,
      body: body ? JSON.stringify(body) : undefined,
      ...options,
      }).then((response) => self._handleResponse(response, resolve, reject));
      });
      }
      }
      const EMAIL = "nemo@nautilus.com";
      const PASSWORD = "xxxxxx";
      const FEDERATION_URL = "https://api-gdn1.prod.macrometa.io";
      
      const COLLECTION_NAME = "students";
      
      const run = async function () {
      try {
      const connection = new APIRequest(FEDERATION_URL);
      
      /* -------------------- Login (nemo@nautilus.com/xxxxxxx) -------------------- */
      
      await connection.login(EMAIL, PASSWORD);
      
      console.log("Login Successfully using", EMAIL);
      
      /* -------------------------- Create Collection ------------------------- */
      
      const collection = await connection.req(
      `/_fabric/_system/_api/kv/${COLLECTION_NAME}`,
      {
      method: "POST",
      }
      );
      
      console.log("COLLECTION CREATED SUCCESSFULLY", collection);
      
      /* ---------------------------- Insert KV Pairs ---------------------------- */
      
      const document = await connection.req(
      `/_fabric/_system/_api/kv/${COLLECTION_NAME}/value`,
      {
      body: [
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
      ,
      method: "PUT",
      }
      );
      
      console.log("KV PAIRS INSERTED SUCCESSFULLY", document);
      
      /* ----------------------------- Get Value for Given Key ----------------------------- */
      let key = "Monika";
      const readVal = await connection.req(
      `/_fabric/_system/_api/kv/${COLLECTION_NAME}/value/${key}`
      );
      
      console.log("VALUE FOR SPECIFIED KEY IS", readVal);
      
      /* ---------------------------- Get Collection Count ---------------------------- */
      
      const collCount = await connection.req(
      `/_fabric/_system/_api/kv/${COLLECTION_NAME}/count`,
      {
      method: "GET",
      }
      );
      
      console.log("COLLECTION COUNT", collCount);
      
      /* ----------------------------- Update Value for a Key ----------------------------- */
      
      const updateValue = await connection.req(
      `/_fabric/_system/_api/kv/${COLLECTION_NAME}/value`,
      {
      body: [
      {
      "_key": "John",
      "value": "Biology",
      "expireAt": 0
      }
      ]
      ,
      method: "PUT",
      }
      );
      
      console.log("KV PAIR UPDATED SUCCESSFULLY", updateValue);
      /* --------------------------- Delete Value for Key ---------------------------- */
      
      const deletedValue = await connection.req(
      `/_fabric/_system/_api/kv/${COLLECTION_NAME}/value/${key}`,
      { method: "DELETE" }
      );

      
      /* --------------------------- Delete Collection ---------------------------- */
      const deletedCollection = await connection.req(
      `/_fabric/_system/_api/kv/${COLLECTION_NAME}`,
      { method: "DELETE" }
      );
      
      console.log("COLLECTION DELETED SUCCESSFULLY", deletedCollection);
      } catch (e) {
      console.error(e);
      }
      };
      
      run();
    ```
