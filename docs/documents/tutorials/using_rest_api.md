# Using REST APIs

Today’s applications are required to be highly responsive and always online. They need to be deployed in datacenters closer to their users and can access data instantly across the globe. 

Macrometa global data network (GDN) is a fully managed realtime materialzed view engine that provides access to data instantly to Apps & APIs in a simple & single interface. 

!!! note
    If you are new to Macrometa GDN, we strongly recommend reading **[Essentials](../../essentials.md)** of Macrometa GDN.

## Pre-Requiste

A tenant account (and credentials) with Macrometa GDN.

## API Browser

Your best friend when working with REST APIs is the REST API browser available in [GDN](https://gdn.paas.macrometa.io) GUI. From there, you can execute various rest apis and see exactly what the inputs and outputs are.

![GDN API Browser](/images/gdn-api-browser.png)

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

    FEDERATION = "api-gdn.macrometa.io"
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
    const FEDERATION_URL = "https://api-gdn1.prod.macrometa.io";

    const COLLECTION_NAME = "api_tutorial_documents";

    const run = async function () {
      try {
        const connection = new APIRequest(FEDERATION_URL);

        /* -------------------- Login (nemo@nautilus.com/xxxxxxx) -------------------- */

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

    FEDERATION = "api-gdn.macrometa.io"
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

## Query as API

Globally distributed applications need a geo distributed fast data platform that can transparently replicate the data anywhere in the world to enable the applications to operate on a copy of the data that's close to its users. Similarly the applications need geo-replicated and local streams to handle pub-sub, ETL and real-time updates from the fast data platform.

Macrometa GDN is a geo-distributed realtime data service with turnkey global distribution and transparent multi-master replication. You can run globally distributed, low-latency workloads with GDN. This article is an introduction to using GDN via its REST APIs.

=== "Python"

    ``` py

    ## Using RESTQL
    import requests
    import json

    # Constants

    FEDERATION = "api-gdn.macrometa.io"
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
    const FEDERATION_URL = "https://api-gdn1.prod.macrometa.io";

    const QUERY_NAME = "api_query_tutorial";
    const QUERY_PARAMS = { "@collection": "api_query_tutorial" };

    const run = async function () {
      try {
        const connection = new APIRequest(FEDERATION_URL);

        /* -------------------- Login (nemo@nautilus.com/xxxxxxx) -------------------- */

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
