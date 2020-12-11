---
template: overrides/main.html
---

# Address Book App _Quickstart_

Let’s take **5 minutes or less** to create a stateful-serverless backend for a simple Address Book. It's going to run globally distributed with local read-write latencies around 50ms.

First things first, if you don't already have a Macrometa account go create a [free](https://macrometa.co/start) one and mosey on back.

We are going to walk through 3 steps:

- Create a collection (like a table in SQL).
- Write and save queries for Create, Update and Delete records.
- Execute Query Workers.

## Step 1: Create a Collection

Let’s start by clicking the `COLLECTIONS`, select the `Document` option, give it the name `addresses`, and save it.

Note: The collection `addresses` that you just created is now distributed to every location in the fabric!

![create-collection](/assets/images/addCollection.png)
## Step 2: Save some queries

To store addresses in our address book app, we will use the following document format (or schema) for each contact:

```json
{
  "firstname": "Captain",
  "lastname": "Nemo",
  "email": "nemo@nautilus.com",
  "zipcode": 94608
}
```

Now, click the `QUERIES` tab to open the `C8QL` query editor. Then create the query shown in the screenshot to the right.

Name: `addAddress`
```SQL
INSERT { firstname: @firstname,
        lastname: @lastname,
        email: @email,
        zipcode: @zipcode }
   INTO addresses
```

Let's execute the query to make sure it works correctly. Do this by filling in some data in the form that's been generated to the right and then click the `Execute` button. You can do that a couple times if you like, but one entry will do the trick.

![create-collection](/assets/images/addData.png)

Now save the query with name `addAddress`.

In **less then a second** you have a globally distributed stateful-serverless endpoint.

![create-collection](/assets/images/savedQuery.png)
## Step 3: Execute Query Worker

Click on the `API Reference` tab and scroll down and to `Query as API`.

Now let’s execute our `getAddresses` endpoint.  Select the second `POST` option to `Execute restQL by name`.  Click on `Try Out` and add `getAddresses` to `path`. Now click `Execute` and you'll see that a cURL command and REST endpoint is generated.

![create-collection](/assets/images/queryWorkers.png)

Now pop that cURL command into your terminal to see it in action!

![create-collection](/assets/images/cURL.png)
## Next Steps

Read though our **[Essentials](essentials.md)** guide.
