# Authentication

There are multiple ways users can access their Macrometa GDN account:

* User Authentication
* Token based Authentication
* API Keys

## User Authentication

Users can authenticate with Macrometa GDN via `email and password` or via their `social media accounts`.

**Code Samples:**

=== "Python"

```py
from c8 import C8Client
client = C8Client(protocol='https', host='gdn.paas.macrometa.io', port=443, 
                    email='nemo@nautilus.com', password='xxxxxx')
```

=== "JavaScript" 

```js
const jsc8 = require("jsc8");
const client = new jsc8({url: "https://gdn.paas.macrometa.io", token: "", fabricName: '_system'});
await client.login("nemo@nautilus.com", "xxxxxx");
```

## Token based Authentication

Users can also authenticate with Macrometa GDN via `Json web tokens`. The `JWT Tokens` in GDN expire after 12 hours unless renewed. So it is best to use API Keys for access by your apps & apis.

**Code Samples:**

=== "Python" 

``` py
from c8 import C8Client
client = C8Client(protocol='https', host='gdn.paas.macrometa.io', port=443, token=<your tokeb>)
```

=== "Javascript"

``` js
const jsc8 = require("jsc8");
const client = new jsc8({url: "https://gdn.paas.macrometa.io", token: "xxxxxx", fabricName: '_system'});
```

## API Keys

Most APIs today use an API Key to authenticate legitimate clients. API Keys are very simple to use from the consumer perspective:

* Get an API key from the service (in essence a shared secret).
* Add the key to an Authorization header.
* Call the API.

API keys never expire.

**Code Samples:**

=== "Python"

``` py
from c8 import C8Client
client = C8Client(protocol='https', host='gdn.paas.macrometa.io', port=443, apikey="xxxxxxx")
```

=== "Javascript"

``` js
const jsc8 = require("jsc8");
const client = new jsc8({url: "https://gdn.paas.macrometa.io", apiKey: "xxxxx", fabricName: '_system'});
```

!!! note
    In Macrometa GDN, granular permissions can be assigned to both `user accounts` and for `api keys`.
