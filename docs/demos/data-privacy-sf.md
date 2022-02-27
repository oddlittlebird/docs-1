# Salesforce - Edge Caching and Data Privacy

Macrometa Lead Management Portal is a lead management `admin` and `user` portal to showcase the edge caching and data privacy capabilities of GDN.


## Setup

| **Federation**                                        | **Email**                              | **Passsword** | **App**|
| ----------------------------------------------------- | -------------------------------------- | ------------- |--------------|
| [Global Data Network](https://gdn.paas.macrometa.io/) | demo@macrometa.io | `xxxx`    | [User Management Portal](https://sf-pii.macrometa.io/) |

**Dataset: [sf-pii-users.csv](../demos/datasets/sf-pii-users.csv)**


## Solution

* Fabrics
    * `pii_eu` -- EU Fabric
    * `pii_global` - Global Fabric

* Stream Workers
    * `DataAnonymizer`
    * `DataAnonymizerUpdate`

* Collections
    * EU - `pii_users`
    * EU - `sharedrecords`
    * EU - `audit`
    * Global - `users`
    * Global - `locations`

**GitHub**

* Regular - https://github.com/Macrometacorp/demo-salesforce-pii
