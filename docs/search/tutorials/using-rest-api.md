# Using REST APIs

Today’s applications are required to be highly responsive and always online. They need to be deployed in datacenters closer to their users and can access data instantly across the globe. 

Macrometa global data network (GDN) is a fully managed realtime materialzed view engine that provides access to data instantly to Apps & APIs in a simple & single interface. 

!!! note
    If you are new to Macrometa GDN, we strongly recommend reading **[Essentials](../../essentials.md)** of Macrometa GDN.

## Pre-Requiste

A tenant account (and credentials) with Macrometa GDN.

## API Browser

Your best friend when working with REST APIs is the REST API browser available in [GDN](https://gdn.paas.macrometa.io) GUI. From there, you can execute various rest apis and see exactly what the inputs and outputs are.

![GDN API Browser](../images/gdn-api-browser.png)

## Working with Documents

A **document** is a dictionary/object that is JSON serializable with the following properties:

- Contains the `_key` field, which identifies the document uniquely within a specific collection.
- Contains the `_id` field (also called the handle), which identifies the document uniquely across all collections within a fabric. This ID is a combination of the collection name and the document key using the format `{collection}/{key}` (see example below).
- Contains the `_rev` field. GDN supports MVCC (Multiple Version Concurrency Control) and is capable of storing each document in multiple revisions. Latest revision of a document is indicated by this field. The field is populated by GDN and is not required as input unless you want to validate a document against its current revision.

Here is an example of a valid document:

```json
{
  "_id": "students/bruce",
  "_key": "bruce",
  "_rev": "_Wm3dzEi--_",
  "first_name": "Bruce",
  "last_name": "Wayne",
  "address": {
    "street": "1007 Mountain Dr.",
    "city": "Gotham",
    "state": "NJ"
  },
  "is_rich": True,
  "friends": ["robin", "gordon"]
}
```

=== "Python"

    ``` py
    import requests
    import json

    # Constants

    FEDERATION = "api.gdn.paas.macrometa.io"
    FED_URL = "https://{}".format(FEDERATION)
    EMAIL = "nemo@nautilus.com"
    PASSWORD = "xxxxxx"
    FABRIC = "_system"
    COLLECTION_NAME = "testcollection"
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

    # Get List of all regions

    url = FED_URL + "/_api/datacenter/all"
    dcl_resp = session.get(url)
    dcl_list = json.loads(dcl_resp.text)
    regions = []
    for dcl in dcl_list:
        dcl_url = dcl['tags']['url']
        regions.append(dcl_url)
    print("\nList of Regions: ",regions)

    # Create a document collection
    # Note :- Create a test collection. Collection type = 2 for documents. Collection type = 3 for edges.

    url = FED_URL + "/_api/collection"
    payload = {
        "name": COLLECTION_NAME,
        "type": 2
    }
    resp = session.post(url, data = json.dumps(payload))
    resp = json.loads(resp.text)
    if "error" in resp.keys():
        print("ERROR: " + resp["errorMessage"])
    else:
        print("\nCollection Created: ", resp.text)


    # Insert a document into collection
    url = FED_URL + "/_api/document/" + COLLECTION_NAME
    payload = {'GPA': 3.5, 'first': 'Lola', 'last': 'Martin', '_key': 'Lola'}
    resp = session.post(url, data = json.dumps(payload))
    print("\nDocument Inserted: ", resp.text)

    # Data can either be a single document or a list of documents
    # Insert multiple documents

    url = FED_URL + "/_api/document/" + COLLECTION_NAME
    data = [
        {'GPA': 3.2, 'first': 'Abby', 'last': 'Page', '_key': 'Abby'},
        {'GPA': 3.6, 'first': 'John', 'last': 'Kim', '_key': 'John'},
        {'GPA': 4.0, 'first': 'Emma', 'last': 'Park', '_key': 'Emma'}
    ]
    resp = session.post(url, data = json.dumps(data))
    print("\nMultiple Documents Inserted: ", resp.text)

    # Read a Document with it's Document Id

    url = FED_URL + "/_api/document/" + COLLECTION_NAME + "/Lola"
    resp = session.get(url)
    print("\nDocument with id Lola is: ",resp.text)

    # Read multiple Documents

    url = FED_URL + "/_api/simple/lookup-by-keys"
    payload = {"collection": COLLECTION_NAME,
                "keys": ["Abby", "John", "Emma"] }
    resp = session.put(url, data = json.dumps(payload))
    resp = json.loads(resp.text)
    print("\nDocuments: ", resp["documents"])

    # Update a Single Document with it's Id
    url = FED_URL + "/_api/document/" + COLLECTION_NAME + "/John"
    payload =     {'GPA': 3.6, 'first': 'John', 'last': 'Andrews', '_key': 'John'},

    resp = session.patch(url, data = json.dumps(payload))
    print("\nUpdated Document with id Lola: ",resp.text)

    # Update  Documents
    url = FED_URL + "/_api/document/" + COLLECTION_NAME
    payload = [
        {'GPA': 4.6, 'first': 'Lola', 'last': 'Martin', '_key': 'Lola'},
        {'GPA': 3.2, 'first': 'Abby', 'last': 'Stutguard', '_key': 'Abby'}
    ]
    resp = session.patch(url, data = json.dumps(payload))
    print("\nUpdated Documents: ", resp.text)

    # Remove single document with it's Id
    url = FED_URL + "/_api/document/" + COLLECTION_NAME + "/John"
    resp = session.delete(url)
    print("\nDeletd Document with Id John: ", resp.text)


    # Remove a multiple document
    url = FED_URL + "/_api/document/" + COLLECTION_NAME
    payload = [
        {'GPA': 4.6, 'first': 'Lola', 'last': 'Martin', '_key': 'Lola'},
        {'GPA': 3.2, 'first': 'Abby', 'last': 'Stutguard', '_key': 'Abby'}
    ]
    resp = session.delete(url, data = json.dumps(payload))
    print("\nDeleted Documents: ", resp.text)
    ```

=== "Javascript"

    ``` js
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
              self._headers.authorization = `bearer ${jwt}`;
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
    const FEDERATION_URL = "https://api-gdn.paas.macrometa.io";

    const COLLECTION_NAME = "api_tutorial_documents";

    const run = async function () {
      try {
        const connection = new APIRequest(FEDERATION_URL);

        /* -------------------- Login (nemo@nautilus.com/xxxxxx) -------------------- */

        await connection.login(EMAIL, PASSWORD);

        console.log("Login Successfully using", EMAIL);

        /* -------------------------- Create Doc Collection ------------------------- */

        const collection = await connection.req(
          "/_fabric/_system/_api/collection",
          {
            body: { name: COLLECTION_NAME },
            method: "POST",
          }
        );

        console.log("COLLECTION CREATED SUCCESSFULLY", collection);

        /* ---------------------------- Insert Documents ---------------------------- */

        const document = await connection.req(
          `/_fabric/_system/_api/document/${COLLECTION_NAME}`,
          {
            body: { new: true },
            method: "POST",
          }
        );

        console.log("DOCUMENT CREATED SUCCESSFULLY", document);

        /* ----------------------------- Read Documents ----------------------------- */

        const readDocument = await connection.req(
          `/_fabric/_system/_api/document/${document._id}`
        );

        console.log("DOCUMENT READ SUCCESSFULLY", readDocument);

        /* ---------------------------- Update Documents ---------------------------- */

        const updateDocument = await connection.req(
          `/_fabric/_system/_api/document/${document._id}`,
          {
            method: "PATCH",
            body: { new: false },
          }
        );

        console.log("DOCUMENT UPDATED SUCCESSFULLY", updateDocument);

        /* ----------------------------- Read Documents ----------------------------- */

        const updatedReadDocument = await connection.req(
          `/_fabric/_system/_api/document/${document._id}`
        );

        console.log("DOCUMENT UPDATED READ SUCCESSFULLY", updatedReadDocument);

        /* ------------------------------- Delete Docs ------------------------------ */
        const deletedDocument = await connection.req(
          `/_fabric/_system/_api/document/${document._id}`,
          {
            method: "DELETE",
          }
        );
        console.log("DOCUMENT DELETED SUCCESSFULLY", deletedDocument);

        /* --------------------------- Delete Collection. --------------------------- */
        const deletedCollection = await connection.req(
          `/_fabric/_system/_api/collection/${COLLECTION_NAME}`,
          { method: "DELETE" }
        );

        console.log("DOCUMENT DELETED SUCCESSFULLY", deletedCollection);
      } catch (e) {
        console.error(e);
      }
    };

    run();

    ```

## Query using C8QL

CRUD Operations can also be done using C8QL

=== "Python"

    ``` py
    #Using C8QL

    FEDERATION = "api.gdn.paas.macrometa.io"
    FED_URL = "https://{}".format(FEDERATION)

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



    # Insert documents to the collection
    resp = session.post(url, json={
        "query": "INSERT{'name' : 'Julie', 'company' : 'ABC', '_key' : 'Julie'}" \
                "INTO testcollection"
    })

    # Read from the collection
    resp = session.post(url, json={
        "query": "FOR doc IN testcollection RETURN doc"
    })

    # Update documents in the collection
    resp = session.post(url, json={
        "query": "FOR c IN testcollection UPDATE {'company':'XYZ'} IN testcollection"
    })

    # Delete documents in the collection
    resp = session.post(url, json={
        "query": "FOR c IN testcollection REMOVE c IN testcollection"
    })
    ```

## Pub-Sub with Streams

**GDN streams** is a high-performance solution for server-to-server messaging.

It provides,

- Seamless geo-replication of messages across regions,
- Very low publish and end-to-end latency,
- Seamless scalability to over a million topics.
- Multiple subscription modes (`exclusive`, `shared`, and `failover`) for streams.
- Guaranteed message delivery with persistent message storage.

`Streams` are built on the _publish-subscribe_ pattern, aka pub-sub. In this pattern, producers publish messages to streams. Consumers can then subscribe to those streams, process incoming messages, and send an acknowledgement when processing is complete.

=== "Python"

    ``` py

    import requests
    import json
    from websocket import create_connection
    import base64
    import six

    # Constants

    FEDERATION = "api.gdn.paas.macrometa.io"
    FED_URL = "https://{}".format(FEDERATION)
    EMAIL = "nemo@nautilus.com"
    PASSWORD = "xxxxxx"
    FABRIC = "_system"
    STREAM_NAME = "teststream"
    AUTH_TOKEN = "bearer "
    TENANT_NAME = "xxxxxx"
    CONSUMER_NAME = "testconsumer"


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

    # Create a stream
    # Note:- For a global stream pass global=true and global=false for local stream
    url = FED_URL + "/_fabric/" + FABRIC + "/streams/" + STREAM_NAME + "'?global=true"
    resp = session.post(url)
    print("\nStream Created: ", resp.text)

    # Publish Messages
    /# Send message in body
    url = FED_URL + "/_fabric/" + FABRIC + "/streams/" + STREAM_NAME + "/publish'?global=true"
    resp = session.post(url)
    print("\nStream Created: ", resp.text)

    or

    stream_type = "c8local"
    producerurl = "wss://" + FEDERATION + "/_ws/ws/v2/producer/persistent/" + TENANT_NAME +\
                    "/" + stream_type + "." + FABRIC + "/" + stream_type + "s." + STREAM_NAME

    ws = create_connection(producerurl)
    payload = {
                    "payload": base64.b64encode(
                        six.b("Hello World")
                    ).decode("utf-8")
                }
    ws.send(json.dumps(payload))
    response = json.loads(ws.recv())
    if response['result'] == 'ok':
        print('Message published successfully')
    else:
        print('Failed to publish message:', response)
    ws.close()

    # Subscribe

    consumerurl = "wss://" + FEDERATION + "/_ws/ws/v2/consumer/persistent/" + TENANT_NAME +\
                    "/" + stream_type + "." + FABRIC + "/" + stream_type + "s." + STREAM_NAME +\
                    "/" + CONSUMER_NAME
    ws = create_connection(consumerurl)
    while True:
        msg = json.loads(ws.recv())
        if msg:
            print("received: {}".format(base64.b64decode(msg['payload'])))
            # Acknowledge successful processing
            ws.send(json.dumps({'messageId': msg['messageId']}))
            break
    ws.close()

    # Delete Subscription/ Unsubscribe
    url = FED_URL + "/_api/streams/unsubscribe/" + CONSUMER_NAME
    resp = session.post(url, data = json.dumps(payload))
    print("Subsrcription Deleted: ", resp.text)

    ```

=== "Javascript"

    ``` js
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
              self._headers.authorization = `bearer ${jwt}`;
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
    const FEDERATION_NAME = "https://api-gdn.paas.macrometa.io";
    const FEDERATION_URL = `https://${FEDERATION_NAME}`;

    const STREAM_NAME = "api_tutorial_streams";
    const CONSUMER_NAME = "api_tutorial_streams_consumer";
    const IS_GLOBAL = true;

    const run = async function () {
      try {
        const connection = new APIRequest(FEDERATION_URL);

        /* -------------------- Login (nemo@nautilus.com/xxxxxxx) -------------------- */

        const { tenant } = await connection.login(EMAIL, PASSWORD);

        console.log("Login Successfully using", tenant);
        /* ------------------------------ Create Stream ----------------------------- */

        const stream = await connection.req(
          `/_fabric/_system/streams/${STREAM_NAME}?global=${IS_GLOBAL}`,
          {
            body: { name: STREAM_NAME },
            method: "POST",
          }
        );

        console.log("STREAM CREATED SUCCESSFULLY", stream);

        /* ----------------- Publish and Subscribe message to stream ---------------- */

        const region = IS_GLOBAL ? "c8global" : "c8local";
        const streamName = `${region}s.${STREAM_NAME}`;
        const url = IS_GLOBAL
          ? FEDERATION_NAME;
          : `api-${streamApp.streamApps[0].regions[0]}.prod.macrometa.io`

        const consumerUrl = `wss://${url}/_ws/ws/v2/consumer/persistent/${tenant}/${region}._system/${streamName}/${CONSUMER_NAME}`;

        const producerUrl = `wss://${url}/_ws/ws/v2/producer/persistent/${tenant}/${region}._system/${streamName}`;

        var consumer;
        var producer;
        var producer_interval;

        /* -------------------------- Initalizing Consumer -------------------------- */

        const initConsumer = () => {
          return new Promise((resolve) => {
            consumer = new WebSocket(consumerUrl);

            consumer.onopen = function () {
              console.log("WebSocket:Consumer is open now for " + streamName);
              resolve();
            };

            consumer.onerror = function () {
              console.log(
                "Failed to establish WebSocket:Consumer connection for " +
                  streamName
              );
            };

            consumer.onclose = function () {
              console.log("Closed WebSocket:Consumer connection for " + streamName);
            };

            consumer.onmessage = function (message) {
              var receivedMsg = message.data && JSON.parse(message.data);

              console.log(
                `WebSocket:Consumer message received at ${new Date()}`,
                receivedMsg
              );

              const ackMsg = { messageId: receivedMsg.messageId };
              consumer.send(JSON.stringify(ackMsg));
            };
          });
        };

        /* -------------------------- Initalizing Producer -------------------------- */

        const initProducer = () => {
          producer = new WebSocket(producerUrl);

          producer.onopen = function () {
            console.log("WebSocket:Producer is open now for " + streamName);
            producer_interval = setInterval(function () {
              console.log(`WebSocket:Producer message sent at ${new Date()}`);
              producer.send(JSON.stringify({ payload: `test` }));
            }, 10000);
          };

          producer.onclose = function (e) {
            console.log("Closed WebSocket:Producer connection for " + streamName);
            clearInterval(producer_interval);
          };

          producer.onerror = function (e) {
            console.log(
              "Failed to establish WebSocket:Producer connection for " + streamName
            );
          };
        };

        initConsumer().then(() => {
          initProducer();
        });

        await new Promise((resolve) => setTimeout(resolve, 1 * 40 * 1000));

        consumer.close();
        console.log("CONSUMER CLOSING...");
        producer.close();
        console.log("PRODUCER CLOSING...");

        await new Promise((resolve) => setTimeout(resolve, 5000));

        /* ------------------------ Unsubscribe from stream. ------------------------ */

        const consumerUnsubscribe = await connection.req(
          `/_fabric/_system/_api/streams/unsubscribe/${CONSUMER_NAME}`,
          {
            method: "POST",
          }
        );

        console.log(
          `${CONSUMER_NAME} UNSUBSCRIBED SUCCESSFULLY`,
          consumerUnsubscribe
        );

        /* ------------------------------ Delete topic ------------------------------ */
      } catch (e) {
        console.error(e);
      }
    };

    run();

    ```

## Query as API (RESTQL)

Globally distributed applications need a geo distributed fast data platform that can transparently replicate the data anywhere in the world to enable the applications to operate on a copy of the data that's close to its users. Similarly the applications need geo-replicated and local streams to handle pub-sub, ETL and real-time updates from the fast data platform.

Macrometa GDN is a geo-distributed realtime data service with turnkey global distribution and transparent multi-master replication. You can run globally distributed, low-latency workloads with GDN. This article is an introduction to using GDN via its REST APIs.

=== "Python"

    ``` py

    ## Using RESTQL
    import requests
    import json

    # Constants

    FEDERATION = "api.gdn.paas.macrometa.io"
    FED_URL = "https://{}".format(FEDERATION)
    EMAIL = "nemo@nautilus.com"
    PASSWORD = "xxxxxx"
    FABRIC = "_system"
    AUTH_TOKEN = "bearer "
    TENANT_NAME = "xxxxxx"
    READ_QUERY = "FOR doc IN @@collection RETURN doc"
    QUERY_NAME = "read"
    QUERY_PARAMS = {"@collection": "api_query_tutorial"}
    INSERT_QUERY =  "FOR i IN 1..100 INSERT { result: i } INTO @@collection"
    UPDATE_QUERY =  "FOR doc IN @@collection FILTER doc.result >= 35 UPDATE doc._key WITH { qualified :true } IN @@collection"
    DELETE_QUERY =  "FOR c IN @@collection REMOVE c IN @@collection"


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

    # Create a RESTQL.
    url = FED_URL + "/_api/restql"

    # Save Read Query
    payload = {
      "query": {
        "name": QUERY_NAME,
        "parameter": QUERY_PARAMS,
        "value": READ_QUERY
      }
    }

    resp = session.post(url, data = json.dumps(payload))
    print("\nRead Query Saved: ", resp.text)

    # Save Insert Query
    payload = {
      "query": {
        "name": "insert",
        "value": INSERT_QUERY,
        "parameter": QUERY_PARAMS,

      }
    }

    resp = session.post(url, data = json.dumps(payload))
    print("\nInsert Query Saved: ", resp.text)

    # Save Update Query
    payload = {
      "query": {
        "name": "update",
        "value": UPDATE_QUERY,
        "parameter": QUERY_PARAMS,

      }
    }

    resp = session.post(url, data = json.dumps(payload))
    print("\nUpdate Query Saved: ", resp.text)

    payload = {
      "query": {
        "name": "delete",
        "value": DELETE_QUERY,
        "parameter": QUERY_PARAMS,

      }
    }

    resp = session.post(url, data = json.dumps(payload))
    print("\nDelete Query Saved: ", resp.text)


    # Execute Saved query

    url = FED_URL + "/_api/restql/execute/insert"
    payload = {
              "bindVars": QUERY_PARAMS,
            }
    resp = session.post(url, data = json.dumps(payload))
    print("\nInsert Query Executed: ", resp.text)

    url = FED_URL + "/_api/restql/execute/" + QUERY_NAME
    payload = {
              "bindVars": QUERY_PARAMS,
            }
    resp = session.post(url, data = json.dumps(payload))
    print("\nRead Query Executed: ", resp.text)

    url = FED_URL + "/_api/restql/execute/update"
    payload = {
              "bindVars": QUERY_PARAMS,
            }
    resp = session.post(url, data = json.dumps(payload))
    print("\nUpdate Query Executed: ", resp.text)

    url = FED_URL + "/_api/restql/execute/delete"
    payload = {
              "bindVars": QUERY_PARAMS,
            }
    resp = session.post(url, data = json.dumps(payload))
    print("\nDelete Query Executed: ", resp.text)


    # Update Saved Query
    url = FED_URL + "/_api/restql/" + QUERY_NAME

    payload = {
      "query": {
        "parameter": QUERY_PARAMS,
        "value": READ_QUERY
      }
    }

    resp = session.put(url, data = json.dumps(payload))
    print("Query Updated: ", resp.text)

    # Delete Saved Queries

    url = FED_URL + "/_api/restql/" + QUERY_NAME
    resp = session.delete(url)
    print("Read Query Deleted: ", resp.text)

    url = FED_URL + "/_api/restql/insert"
    resp = session.delete(url)
    print("Insert Query Deleted: ", resp.text)

    url = FED_URL + "/_api/restql/update"
    resp = session.delete(url)
    print("Update Query Deleted: ", resp.text)

    url = FED_URL + "/_api/restql/delete"
    resp = session.delete(url)
    print("Delete Query Deleted: ", resp.text)
    ```

=== "Javascript"

    ``` js
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
              self._headers.authorization = `bearer ${jwt}`;
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
    const FEDERATION_URL = "https://api-gdn.paas.macrometa.io";

    const QUERY_NAME = "api_query_tutorial";
    const QUERY_PARAMS = { "@collection": "api_query_tutorial" };

    const run = async function () {
      try {
        const connection = new APIRequest(FEDERATION_URL);

        /* -------------------- Login (nemo@nautilus.com/xxxxxx) -------------------- */

        await connection.login(EMAIL, PASSWORD);

        console.log("Login Successfully using", EMAIL);

        /* ------------------------ Saving a Restql Query (with params) ----------------------- */

        const QUERY = "FOR doc IN @@collection RETURN doc";

        const query = await connection.req("/_fabric/_system/_api/restql", {
          body: {
            query: {
              name: QUERY_NAME,
              value: QUERY,
              parameter: QUERY_PARAMS,
            },
          },
          method: "POST",
        });

        console.log("QUERY CREATED SAVED SUCCESSFULLY", query);

        /* ----------------------- Updating a Restql Query (with params) ---------------------- */

        const updatedQuery = await connection.req(
          `/_fabric/_system/_api/restql/${QUERY_NAME}`,
          {
            body: {
              query: {
                value: QUERY,
                parameter: QUERY_PARAMS,
              },
            },
            method: "PUT",
          }
        );

        console.log("QUERY UPDATED  SUCCESSFULLY", updatedQuery);

        /* ----------------------- Executing a Restql Query (with params) ---------------------- */

        const execute = () =>
          connection.req(`/_fabric/_system/_api/restql/execute/${QUERY_NAME}`, {
            body: {
              bindVars: QUERY_PARAMS,
            },
            method: "POST",
          });

        /* -------------------  Insert Query using Cursor (with params) ------------------- */

        const INSERT_QUERY =
          "FOR i IN 1..100 INSERT { result: i } INTO @@collection";

        await connection.req(`/_fabric/_system/_api/cursor`, {
          body: {
            id: "tutorialQuery",
            query: INSERT_QUERY,
            bindVars: QUERY_PARAMS,
          },
          method: "POST",
        });

        console.log("DOCUMENTS INSERTED SUCCESSFULLY");

        const insertResults = await execute();

        console.log("DATA AFTER INSERT", insertResults);

        /* ------------------- Update Query using Cursor (with params) ------------------- */
        const CURSOR_QUERY =
          "FOR doc IN @@collection FILTER doc.result >= 35 UPDATE doc._key WITH { qualified :true } IN @@collection";

        await connection.req(`/_fabric/_system/_api/cursor`, {
          body: {
            id: "tutorialQuery",
            query: CURSOR_QUERY,
            bindVars: QUERY_PARAMS,
          },
          method: "POST",
        });
        console.log("DOCUMENTS UPDATED SUCCESSFULLY");

        const updateResults = await execute();

        console.log("DATA AFTER UPDATE", updateResults);

        /* ------------------- Remove Query using Cursor (with params) ------------------- */

        const REMOVE_QUERY = "FOR doc IN @@collection REMOVE doc IN @@collection";

        await connection.req(`/_fabric/_system/_api/cursor`, {
          body: {
            id: "tutorialQuery",
            query: REMOVE_QUERY,
            bindVars: QUERY_PARAMS,
          },
          method: "POST",
        });

        console.log("DOCUMENTS DELETED SUCCESSFULLY");

        const removeResults = await execute();

        console.log("DATA AFTER DELETE", removeResults);

        /* ----------------------------- Delete RESTQL Query with Name ----------------------------- */

        const deleteQuery = await connection.req(
          `/_fabric/_system/_api/restql/${QUERY_NAME}`,
          {
            method: "DELETE",
          }
        );

        console.log("QUERY DELETED  SUCCESSFULLY", deleteQuery);
      } catch (e) {
        console.error(e);
      }
    };

    run();

    ```

## Working with Graphs

**Edge documents (edges)** are similar to standard documents but with two additional required fields `_from` and `_to`. Values of these fields must be the handles of "from" and "to" vertex documents linked by the edge document in question. Here is an example of a valid edge document:

```json
{
  "_id": "friends/001",
  "_key": "001",
  "_rev": "_Wm3dyle--_",
  "_from": "students/john",
  "_to": "students/jane",
  "closeness": 9.5
}
```

A **Graph** consists of vertices and edges. Edges are stored as documents in edge collections. A vertex can be a document of a document collection or of an edge collection (so edges can be used as vertices). Which collections are used within a named graph is defined via edge definitions. A `named graph` can contain more than one edge definition, at least one is needed. Graphs allow you to structure your models in line with your domain and group them logically in collections and giving you the power to query them in the same graph queries.

In SQL you commonly have the construct of a relation table to store `n:m` relations between two data tables. An `edge collection` is somewhat similar to these relation tables. `Vertex collections` resemble the data tables with the objects to connect.

While simple graph queries with fixed number of hops via the relation table may be doable in SQL with several nested joins, graph databases can handle an arbitrary number of these hops over edge collections - this is called `traversal`. Also edges in one edge collection may point to several vertex collections. Its common to have attributes attached to edges, i.e. a label naming this interconnection.

Edges have a direction, with their relations `_from` and `_to` pointing from one document to another document stored in vertex collections. In queries you can define in which directions the edge relations may be followed i.e.,

- OUTBOUND: `_from` → `_to`
- INBOUND: `_from` ← `_to`
- ANY: `_from` ↔ `_to`.

An **edge collection** contains edge documents and shares its namespace with all other types of collections. You can manage edge documents via standard collection API wrappers, but using edge collection API wrappers provides additional safeguards:

- All modifications are executed in transactions.
- Edge documents are checked against the edge definitions on insert.

To create `edge collection` use same endpoint `/_fabric/{fabric_name}/_api/collection` and pass `type:3` in payload.

=== "Python"

    ``` py
    import requests
    import json

    # Constants

    FEDERATION = "api.gdn.paas.macrometa.io"
    FED_URL = "https://{}".format(FEDERATION)
    EMAIL = "nemo@nautilus.com"
    PASSWORD = "xxxxxx"
    FABRIC = "_system"
    AUTH_TOKEN = "bearer "
    TENANT_NAME = "xxxxxx"
    COLLECTION_NAME_1 = "teachers"
    COLLECTION_NAME_2 = "lectures"
    EDGE_COLL_NAME = "teach"
    GRAPH_NAME = "lectureteacher"


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

    # Create Doc Collections and Insert Data to Document Collections


    url = FED_URL + "/_api/collection"
    payload = { 'name': COLLECTION_NAME_1 }

    resp = session.post(url,data=json.dumps(payload))
    result = json.loads(resp.text)
    print("\nDocument Collection1 Created: ",result)

    payload = { 'name': COLLECTION_NAME_2 }

    resp = session.post(url,data=json.dumps(payload))
    result = json.loads(resp.text)
    print("\nDocument Collection2 Created: ",result)

    payload = [
        {
            '_key':'Jean',
            'firstname': 'Jean',
            'lastname':'Picard',
            'email':'jean.picard@macrometa.io'
        },
        {
            '_key':'James',
            'firstname': 'James',
            'lastname':'Kirk',
            'email':'james.kirk@macrometa.io'
        },
        {
            '_key': 'Han',
            'firstname': 'Han',
            'lastname':'Solo',
            'email':'han.solo@macrometa.io'
        },
        {
            '_key': 'Bruce',
            'firstname': 'Bruce',
            'lastname':'Wayne',
            'email':'bruce.wayne@macrometa.io'
        }
    ]

    url = FED_URL + "/_api/document/" + COLLECTION_NAME_1
    resp = session.post(url,data=json.dumps(payload))
    result = json.loads(resp.text)
    print("\nDocuments Inserted: ",result)

    payload = [
        {'_id': 'lectures/CSC101', 'difficulty': 'easy', '_key':'CSC101', 'firstname':'Jean'},
        {'_id': 'lectures/CSC102', 'difficulty': 'hard', '_key':'CSC102','firstname':'Jean'},
        {'_id': 'lectures/CSC103', 'difficulty': 'hard', '_key':'CSC103','firstname':'Jean'},
        {'_id': 'lectures/CSC104', 'difficulty': 'moderate', '_key':'CSC104','firstname':'Jean'}

    ]

    url = FED_URL + "/_api/document/" + COLLECTION_NAME_2
    resp = session.post(url,data=json.dumps(payload))
    result = json.loads(resp.text)
    print("\nDocuments Inserted: ",result)

    # Create Edge Collection

    payload = { 'name': EDGE_COLL_NAME, "type":3 }

    url = FED_URL + "/_api/collection"
    resp = session.post(url,data=json.dumps(payload))
    result = json.loads(resp.text)
    print("\nEdge Collection Created: ",result)
    payload = [
        {
        '_key': 'Jean-CSC101',
        '_from': 'teachers/Jean',
        '_to': 'lectures/CSC101',
        'online': False
        },
        {
        '_key': 'Jean-CSC102',
        '_from': 'teachers/Jean',
        '_to': 'lectures/CSC102',
        'online': True
        },
        {
        '_key': 'Jean-CSC103',
        '_from': 'teachers/Jean',
        '_to': 'lectures/CSC103',
        'online': False
        },
        {
        '_key': 'Bruce-CSC101',
        '_from': 'teachers/Bruce',
        '_to': 'lectures/CSC101',
        'online': True
        }

    ]

    url = FED_URL + "/_api/document/" + EDGE_COLL_NAME
    resp = session.post(url,data=json.dumps(payload))
    result = json.loads(resp.text)
    print("\nDocuments Inserted: ",result)
    # Create a Graph
    payload ={
      "edgeDefinitions": [
        {
          "collection": EDGE_COLL_NAME,
          "from": [
            "teachers"
          ],
          "to": [
            "lectures"
          ]
        }
      ],
      "name": GRAPH_NAME,
      "options": {}
    }

    url = FED_URL + "/_api/graph"
    resp = session.post(url,data=json.dumps(payload))
    result = json.loads(resp.text)
    print("\nGraph Created: ",result)

    # Graph Traversal
    # Note :- To use Outbound Traversal use direction: out and direction: in for Inbound Traversal
    params = {
        "vertex": "Jean",
        "direction": "out"
    }

    url = FED_URL + "/_api/edges/" + EDGE_COLL_NAME

    resp = session.get(url,params=params)
    result = json.loads(resp.text)
    print("\nGraph Traversal: ",result)

    # Delete Graph and Collections
    # Note:- If you want to delete just the graph and keep collections then
    # set dropCollection to False
    params = {"dropCollection": True}

    url = FED_URL + "/_api/graph/" + GRAPH_NAME

    resp = session.delete(url,params=params)
    result = json.loads(resp.text)
    print("Graph and Collections Deleted: ", result)
    ```

=== "Javascript"

    ``` js
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
              self._headers.authorization = `bearer ${jwt}`;
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
    TBD
    ```

## Stream Processing

Macrometa Stream Processing engine allows you to integrate streaming data and take action based on streaming data. Typically the stream processing use cases involve collecting, analyzing and, integrate or acting on data generated during business activities by various sources i.e.,

- **Collect**: Receive or capture data from various data sources.

- **Analyze**: Analyze data to identify interesting patterns and to extract information.

- **Act**: Take actions based on the results and findings done via processing the data. The action can be executing some random code, calling an external service, or triggering a complex integration.

- **Integrate**: Make processed data available for consumers to consume globally in right format with very low latencies.

=== "Python"

    ``` py
    import requests
    import json
    from websocket import create_connection
    import base64
    import six
    import time
    # Constants

    FEDERATION = "api-gdn-us-west.prod.macrometa.io"
    FED_URL = "https://{}".format(FEDERATION)
    EMAIL = "nemo@nautilus.com"
    PASSWORD = "xxxxxx"
    FABRIC = "_system"
    AUTH_TOKEN = "bearer "
    TENANT_NAME = "xxxxxx"
    STREAM_NAME = "tutorialAppInputStream"
    STREAM_APP_NAME = "stream_app_tutorial"
    STREAM_APP ='''
      @App:name('stream_app_tutorial')

      define function concatFn[javascript] return string {
          var str1 = data[0];
          var str2 = data[1];
          var str3 = data[2];
          var response = str1 + str2 + str3;
          return response;
      };

      -- Stream
      define stream tutorialAppInputStream (deviceID string, roomNo int, temperature double);

      -- Table
      define table tutorialAppOutputTable (id string, temperature double);

      @info(name='Query')
      select concatFn(roomNo,'-',deviceID) as id, temperature
      from tutorialAppInputStream
      insert into tutorialAppOutputTable;
    '''
    INPUT_DATA = [
          {
            "deviceID": "AD11",
            "roomNo": 200,
            "temperature": 18,
          },
          { "deviceID": "AD11",
            "roomNo": 201,
            "temperature": 47 },
        ]
    SELECT_QUERY = "FOR doc IN tutorialAppOutputTable return doc"

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

    # Create a Stream Application

    url = FED_URL + "/_api/streamapps"
    payload = {
      "definition": STREAM_APP,
      "regions": ["gdn-us-west1"]
    }

    resp = session.post(url, data=json.dumps(payload))
    result = json.loads(resp.text)
    print("\nStream App Created: ", result)

    # Activate Stream Application

    url = FED_URL + "/_api/streamapps/" + STREAM_APP_NAME + "/active?active=true"
    resp = session.patch(url)
    result = json.loads(resp.text)
    print("\nStream App Activated: ", result)

    # Wait for all inputs and outputs to initialize
    time.sleep(20)

    # Publish Messages to the input stream
    stream_type = "c8local"
    producerurl = "wss://" + FEDERATION + "/_ws/ws/v2/producer/persistent/" + TENANT_NAME +\
                    "/" + stream_type + "." + FABRIC + "/" + stream_type + "s." + STREAM_NAME

    ws = create_connection(producerurl)
    payload = {
                    "payload": base64.b64encode(
                        six.b(json.dumps(INPUT_DATA[0]))
                    ).decode("utf-8")
                }
    ws.send(json.dumps(payload))
    response = json.loads(ws.recv())
    if response['result'] == 'ok':
        print('Message published successfully')
    else:
        print('Failed to publish message:', response)

    payload = {
                    "payload": base64.b64encode(
                        six.b(json.dumps(INPUT_DATA[1]))
                    ).decode("utf-8")
                }
    ws.send(json.dumps(payload))
    response = json.loads(ws.recv())
    if response['result'] == 'ok':
        print('Message published successfully')
    else:
        print('Failed to publish message:', response)

    ws.close()

    # Verify results from the collection

    url = FED_URL + "/_api/cursor"
    payload= {
      "id": "tutorialStreamAppQuery",
      "query": SELECT_QUERY,
      "bindVars": {},
    }
    resp = session.post(url, data=json.dumps(payload))
    result = json.loads(resp.text)
    print("\nStream App Results: ", result)
    # Delete Stream Apllication

    url = FED_URL + "/_api/streamapps/" + STREAM_APP_NAME
    resp = session.delete(url)
    result = json.loads(resp.text)
    print("\nStream App Deleted: ", result)

    ```

=== "Javascript"

    ``` js
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
              self._headers.authorization = `bearer ${jwt}`;
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
    const FEDERATION_NAME = "api.gdn.paas.macrometa.io";
    const FEDERATION_URL = `https://${FEDERATION_NAME}`;

    const IS_GLOBAL = true;
    const STREAM_NAME = `tutorialAppInputStream`;
    const STREAM_APP_NAME = `strean_app_tutorial`;
    const STREAM_APP = `@App:name('strean_app_tutorial')
      @App:description('This application demonstrates how to use user defined function in the stream app')

      define function concatFn[javascript] return string {
          var str1 = data[0];
          var str2 = data[1];
          var str3 = data[2];
          var response = str1 + str2 + str3;
          return response;
      };

      -- Stream
      define stream tutorialAppInputStream (deviceID string, roomNo int, temperature double);

      -- Table
      define table tutorialAppOutputTable (id string, temperature double);

      @info(name='Query')
      select concatFn(roomNo,'-',deviceID) as id, temperature
      from tutorialAppInputStream
      insert into tutorialAppOutputTable;`;

    const run = async function () {
      try {
        const connection = new APIRequest(FEDERATION_URL);

        /* -------------------- Login (nemo@nautilus.com/xxxxxx) -------------------- */

        const { tenant } = await connection.login(EMAIL, PASSWORD);

        console.log("Login Successfully using", tenant);

        /* ---------------------------- Create StreamApp ---------------------------- */
        const streamApp = await connection.req("/_fabric/_system/_api/streamapps", {
          body: {
            definition: STREAM_APP,
            regions: [],
          },
          method: "POST",
        });

        console.log("STREAM APP CREATED SUCCESSFULLY", streamApp);

        /* --------------------------- Activate StreamApp --------------------------- */

        await connection.req(
          `/_fabric/_system/_api/streamapps/${STREAM_APP_NAME}/active?active=true`,
          {
            method: "PATCH",
          }
        );

        console.log("ACTIVATING STREAM APP...", STREAM_APP_NAME);

        await new Promise((resolve) => setTimeout(resolve, 10000));

        console.log("STREAM APP ACTIVATED SUCCESSFULLY");

        /* ------------------ Publish messages to Sample StreamApp ------------------ */
        const region = IS_GLOBAL ? "c8global" : "c8local";
        const streamName = `${region}s.${STREAM_NAME}`;
        const url = IS_GLOBAL
          ? FEDERATION_NAME;
          : `api-${streamApp.streamApps[0].regions[0]}.prod.macrometa.io`

        const producerUrl = `wss://${url}/_ws/ws/v2/producer/persistent/${tenant}/${region}._system/${streamName}`;

        /* -------------------------- Initalizing Producer -------------------------- */

        const producer = new WebSocket(producerUrl);

        producer.onopen = function () {
          console.log("WebSocket:Producer is open now for " + streamName);
        };

        producer.onerror = function () {
          console.log(
            "Failed to establish WebSocket:Producer connection for " + streamName
          );
        };

        producer.onclose = function () {
          console.log("Closed WebSocket:Producer connection for " + streamName);
        };

        producer.onmessage = function () {
          console.log("WebSocket:Producer message sent successfully");
        };

        await new Promise((resolve) => setTimeout(resolve, 10000));

        const INPUT_DATA = [
          {
            deviceID: "AD11",
            roomNo: 200,
            temperature: 18,
          },
          { deviceID: "AD11", roomNo: 201, temperature: 47 },
        ];

        producer.send(
          JSON.stringify({
            payload: btoa(JSON.stringify(INPUT_DATA[0])),
          })
        );

        await new Promise((resolve) => setTimeout(resolve, 10000));

        producer.send(
          JSON.stringify({
            payload: btoa(JSON.stringify(INPUT_DATA[1])),
          })
        );

        await new Promise((resolve) => setTimeout(resolve, 10000));

        producer.close();

        /* ----------------------------- Verify results ----------------------------- */

        const SELECT_QUERY = "FOR doc IN tutorialAppOutputTable return doc";

        const result = await connection.req(`/_fabric/_system/_api/cursor`, {
          body: {
            id: "tutorialStreamAppQuery",
            query: SELECT_QUERY,
            bindVars: {},
          },
          method: "POST",
        });

        console.log("INPUT SENT --->", INPUT_DATA);
        console.log("OUTPUT DATA --->", result.results);

        /* ---------------------------- Delete StreamApp ---------------------------- */
        const deletion = await connection.req(
          `/_fabric/_system/_api/streamapps/${STREAM_APP_NAME}`,
          {
            method: "DELETE",
          }
        );

        console.log("STREAM APP DELETED SUCCESSFULLY", deletion);
      } catch (e) {
        console.error(e);
      }
    };

    run();
    ```
