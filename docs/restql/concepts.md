# Concepts

Query Workers (aka `Query as API`) are named, parameterized C8QL queries stored in GDN that can be executed from a dedicated REST endpoint. C8QL is a mix of SQL & Javascript providing rich set of primitives to query & update GDN.

!!! note
    When the user saves a Query Worker, the query is automatically converted to an API and is deployed globally to serve the users from the region closest to them with local latencies.

!!! note
    We recommend using Query Workers to build applications backed by GDN as opposed to querying with C8QL directly from application code or setting up a centralized middleware.

Query Workers can be created and updated using the GDN Console or by using the REST API directly. Each Query Worker is tied to a specific `query text` and parameter set. You can set default values for query parameters (making them optional during executions of your Query Workers), or you can make them mandatory for each execution (failing to pass along will result in an error).

Each Query Worker is exposed as its own endpoint and is protected. The Query Workers are organized by the fabric (or database) enabling you to have different Query Workers for different `geo-regions` as well as for different `fabrics within same region`.
