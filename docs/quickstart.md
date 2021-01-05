---
template: overrides/main.html
---

# Address Book App _Quickstart_

Let’s take **5 minutes** to create a stateful-serverless backend for a simple Address Book. It's going to run globally distributed with local read-write latencies around 50ms.

We are going to walk through 4 steps:

- Create a collection and add some data to it.
- Query that data.
- Save the query (A saved query is called a Query Worker).
- Execute the Query Worker!

First things first, if you don't already have a Macrometa account go create a [free](https://macrometa.co/start) one and mosey on back.

## Step 1: Create a Collection

Let’s start by clicking the `COLLECTIONS` tab in the left nav and then `New Collection` on the right. Select the `Document` option, give it the name `addresses`, and save it.

Note: The collection `addresses` that you just created is now distributed to every location in the fabric!

![create-collection](/assets/images/addCollection.png)
## Step 2: Add some data to your new collection and query it

Now, click the `QUERIES` tab in the left nav to open the `C8QL` query editor. Copy and paste the query below into the editor and click `Run Query` a couple times. 

```SQL
INSERT {"firstname": "Captain",
        "lastname": "Nemo",
        "email": "nemo@nautilus.com",
        "zipcode": 94608 }
   INTO addresses
```

Your `Query Result` will be empty brackets, but if you click on `Profile` button you can checkout what just happened along with some performance details. Your data was just written to the location you're currently logged into and replicated across all of the nodes in your fabric.

Now, let's query the data you just added to your collection. Copy the below query and replace the `INSERT` query currently in the editor with it.

```SQL
FOR docs IN addresses RETURN docs 
```

You should see the data you just saved returned.

Ok, now we are going to save this query. Saving it will turn it into a `Query Worker`.

## Step 3: Save the Query

Click the `Save Query` button and name the saved query `getAddresses`

Now, in **around 50ms**, you have a globally distributed stateful-serverless endpoint.

## Step 4: Execute the Query Worker

Click on the `API REFERENCE` tab on the left and scroll down and to `Query as API`.

Select the second `POST` option to `Execute restQL by name`.  Click on `Try Out` and add `getAddresses` to `path` under `name`. Now click `Execute` down at the bottom and you'll see that a cURL command and REST endpoint is generated along with the result of the query.

![create-collection](/assets/images/queryWorkers.png)

Now pop that cURL command into your terminal to see it in action!

![create-collection](/assets/images/cURL.png)
## Next Steps

Read though our **[Essentials](essentials.md)** guide.