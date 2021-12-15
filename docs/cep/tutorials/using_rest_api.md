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

## Stream Processing

Macrometa Stream Processing engine allows you to integrate streaming data and take action based on streaming data. 

Typically the stream processing use cases involve collecting, analyzing and, integrate or acting on data generated during business activities by various sources i.e.,

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

    FEDERATION = "api-gdn.paas.macrometa.io"
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
    const FEDERATION_NAME = "api-gdn.paas.macrometa.io";
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
