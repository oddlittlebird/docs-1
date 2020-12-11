# Index basics

Indexes allow fast access to documents, provided the indexed attribute(s) are used in a query. While GDN automatically indexes some system attributes, users are free to create extra indexes on non-system attributes of documents.

User-defined indexes can be created on collection level. Most user-defined indexes can be created by specifying the names of the index attributes. Some index types allow indexing just one attribute (e.g. `fulltext` index) whereas other index types allow indexing multiple attributes at the same time.

The system attributes `_id`, `_key`, `_from` and `_to` are automatically indexed by GDN, without the user being required to create extra indexes for them. `_id` and `_key` are covered by a collection's primary key, and `_from` and `_to` are covered by an edge collection's edge index automatically.

Using the system attribute `_id` in user-defined indexes is not possible, but indexing `_key`, `_rev`, `_from`, and `_to` is.

Creating new indexes is by default done under an exclusive collection lock. The collection is not available while the index is being created. This "foreground" index creation can be undesirable, if you have to perform it on a live system without a dedicated maintenance window.

For potentially long running index creation operations GDN storage-engine also supports creating indexes in "background". The collection remains (mostly) available during the index creation, see the section [Creating Indexes in Background](#creating-indexes-in-background) for more information.

GDN provides the following index types:

## Primary Index

For each collection there will always be a *primary index* which is a hash index for the [document keys](glossary#document-key) (`_key` attribute) of all documents in the collection. The primary index allows quick selection of documents in the collection using either the `_key` or `_id` attributes. It will be used from within C8QL queries automatically when performing equality lookups on `_key` or `_id`. 

There are also dedicated functions to find a document given its `_key` or `_id` that will always make use of the primary index:

```js
collection.document("<document-key>");
_document("<document-id>");
```

As the primary index is an unsorted hash index, it cannot be used for non-equality range queries or for sorting.

!!! note
    The primary index of a collection cannot be dropped or changed, and there is no mechanism to create user-defined primary indexes.

## Edge Index

Every [edge collection](glossary#edge-collection) also has an automatically created *edge index*. The edge index provides quick access to documents by either their `_from` or `_to` attributes. It can therefore be used to quickly find connections between vertex documents and is invoked when the connecting edges of a vertex are queried.

Edge indexes are used from within C8QL when performing equality lookups on `_from` or `_to` values in an edge collections. There are also dedicated functions to find edges given their `_from` or `_to` values that will always make use of the edge index:

```js
collection.edges("<from-value>");
collection.edges("<to-value>");
collection.outEdges("<from-value>");
collection.outEdges("<to-value>");
collection.inEdges("<from-value>");
collection.inEdges("<to-value>");
```

Internally, the edge index is implemented as a hash index, which stores the union of all `_from` and `_to` attributes. It can be used for equality lookups, but not for range queries or for sorting. Edge indexes are automatically created for edge collections. It is not possible to create user-defined edge indexes. However, it is possible to freely use the `_from` and `_to` attributes in user-defined indexes.

!!! note
    An edge index cannot be dropped or changed.

## Hash Index

A hash index can be used to quickly find documents with specific attribute values. The hash index is unsorted, so it supports equality lookups but no range queries or sorting.

A hash index can be created on one or multiple document attributes. A hash index will only be used by a query if all index attributes are present in the search condition, and if all attributes are compared using the equality (`==`) operator. Hash indexes are used from within C8QL and several query functions, e.g. `byExample`, `firstExample` etc.

Hash indexes can optionally be declared unique, then disallowing saving the same value(s) in the indexed attribute(s). Hash indexes can optionally be sparse.

The different types of hash indexes have the following characteristics:

- **unique hash index**: all documents in the collection must have different values for the attributes covered by the unique index. Trying to insert a document with the same key value as an already existing document will lead to a unique constraint violation. 

  This type of index is not sparse. Documents that do not contain the index attributes or that have a value of `null` in the index attribute(s) will still be indexed. A key value of `null` may only occur once in the index, so this type of index cannot be used for optional attributes.

  The unique option can also be used to ensure that [no duplicate edges](indexing-hash.html#ensure-uniqueness-of-relations-in-edge-collections) are created, by adding a combined index for the fields `_from` and `_to` to an edge collection.

- **unique, sparse hash index**: all documents in the collection must have different values for the attributes covered by the unique index. Documents in which at least one of the index attributes is not set or has a value of `null` are not included in the index. This type of index can be used to ensure that there are no duplicate keys in the collection for documents which have the indexed attributes set. As the index will exclude documents for which the indexed attributes are `null` or not set, it can be used for optional attributes.

- **non-unique hash index**: all documents in the collection will be indexed. This type of index is not sparse. Documents that do not contain the index attributes or that have a value of `null` in the index attribute(s) will still be indexed. Duplicate key values can occur and do not lead to unique constraint violations.
 
- **non-unique, sparse hash index**: only those documents will be indexed that have all the indexed attributes set to a value other than `null`. It can be used for optional attributes.

The amortized complexity of lookup, insert, update, and removal operations in unique hash indexes is O(1). 

Non-unique hash indexes have an amortized complexity of O(1) for insert, update, and removal operations. That means non-unique hash indexes can be used on attributes with low cardinality. 

If a hash index is created on an attribute that is missing in all or many of the documents, the behavior is as follows:

- if the index is sparse, the documents missing the attribute will not be indexed and not use index memory. These documents will not influence the update or removal performance for the index.

- if the index is non-sparse, the documents missing the attribute will be contained in the index with a key value of `null`. 

Hash indexes support [indexing array values](#indexing-array-values) if the index attribute name is extended with a `[*]`.

## Skiplist Index

A skiplist is a sorted index structure. It can be used to quickly find documents with specific attribute values, for range queries and for returning documents from the index in sorted order. Skiplists will be used from within C8QL and several query functions, e.g. `byExample`, `firstExample` etc.

Skiplist indexes will be used for lookups, range queries and sorting only if either all index attributes are provided in a query, or if a leftmost prefix of the index attributes is specified.

For example, if a skiplist index is created on attributes `value1` and `value2`, the following filter conditions can use the index (note: the `<=` and `>=` operators are intentionally omitted here for the sake of brevity):

```js
FILTER doc.value1 == ...
FILTER doc.value1 < ...
FILTER doc.value1 > ...
FILTER doc.value1 > ... && doc.value1 < ...

FILTER doc.value1 == ... && doc.value2 == ...
FILTER doc.value1 == ... && doc.value2 > ...
FILTER doc.value1 == ... && doc.value2 > ... && doc.value2 < ...
```

In order to use a skiplist index for sorting, the index attributes must be specified in the `SORT` clause of the query in the same order as they appear in the index definition. Skiplist indexes are always created in ascending order, but they can be used to access the indexed elements in both ascending or descending order. However, for a combined index (an index on multiple attributes) this requires that the sort orders in a single query as specified in the `SORT` clause must be either all ascending (optionally ommitted as ascending is the default) or all descending. 

For example, if the skiplist index is created on attributes `value1` and `value2` (in this order), then the following sorts clauses can use the index for sorting:

- `SORT value1 ASC, value2 ASC` (and its equivalent `SORT value1, value2`)
- `SORT value1 DESC, value2 DESC`
- `SORT value1 ASC` (and its equivalent `SORT value1`)
- `SORT value1 DESC`

The following sort clauses cannot make use of the index order, and require an extra sort step:

- `SORT value1 ASC, value2 DESC`
- `SORT value1 DESC, value2 ASC`
- `SORT value2` (and its equivalent `SORT value2 ASC`)
- `SORT value2 DESC` (because first indexed attribute `value1` is not used in sort clause)

!!! note
    The latter two sort clauses cannot use the index because the sort clause does not refer to a leftmost prefix of the index attributes.

Skiplists can optionally be declared unique, disallowing saving the same value in the indexed attribute. They can be sparse or non-sparse.

The different types of skiplist indexes have the following characteristics:

- **unique skiplist index**: all documents in the collection must have different values for the attributes covered by the unique index. Trying to insert a document with the same key value as an already existing document will lead to a unique constraint violation. 

  This type of index is not sparse. Documents that do not contain the index attributes or that have a value of `null` in the index attribute(s) will still be indexed. A key value of `null` may only occur once in the index, so this type of index cannot be used for optional attributes.

- **unique, sparse skiplist index**: all documents in the collection must have different values for the attributes covered by the unique index. Documents in which at least one of the index attributes is not set or has a value of `null` are not included in the index. This type of index can be used to ensure that there are no duplicate keys in the collection for documents which have the indexed attributes set. As the index will exclude documents for which the indexed attributes are `null` or not set, it can be used for optional attributes.

- **non-unique skiplist index**: all documents in the collection will be indexed. This type of index is not sparse. Documents that do not contain the index attributes or that have a value of `null` in the index attribute(s) will still be indexed. Duplicate key values can occur and do not lead to unique constraint violations.
 
- **non-unique, sparse skiplist index**: only those documents will be indexed that have all the indexed attributes set to a value other than `null`. It can be used for optional attributes.

The operational amortized complexity for skiplist indexes is logarithmically correlated with the number of documents in the index.

Skiplist indexes support [indexing array values](#indexing-array-values) if the index attribute name is extended with a `[*]``.

## Persistent Index

!!! note
    The index types *hash*, *skiplist* and *persistent* are equivalent. The types *hash* and *skiplist* are still allowed for backward compatibility in the APIs, but the web interface does not offer these types anymore.

The persistent index is a sorted index with persistence. The index entries are written to disk when documents are stored or updated. That means the index entries do not need to be rebuilt from the collection data when the server is restarted or the indexed collection is initially loaded. Thus using persistent indexes may reduce collection loading times.

The persistent index type can be used for secondary indexes at the moment. That means the persistent index currently cannot be made the only index for a collection, because there will always be the in-memory primary index for the collection in addition, and potentially more indexes (such as the edges index for an edge collection).

The index implementation is using the RocksDB engine, and it provides logarithmic complexity for insert, update, and remove operations. As the persistent index is not an in-memory index, it does not store pointers into the primary index as all the in-memory indexes do, but instead it stores a document's primary key. To retrieve a document via a persistent index via an index value lookup, there will therefore be an additional O(1) lookup into the primary index to fetch the actual document.

As the persistent index is sorted, it can be used for point lookups, range queries and sorting operations, but only if either all index attributes are provided in a query, or if a leftmost prefix of the index attributes is specified.

## TTL (time-to-live) Index

The TTL index type provided by GDN can be used for automatically removing expired documents from a collection. 

A TTL index is set up by setting an `expireAfter` value and by picking a single document attribute which contains the documents' creation date and time. Documents are expired after `expireAfter` seconds after their creation time. The creation time is specified as either a numeric timestamp (Unix timestamp) or a date string in format `YYYY-MM-DDTHH:MM:SS` with optional milliseconds. All date strings will be interpreted as UTC dates.

For example, if `expireAfter` is set to 600 seconds (10 minutes) and the index attribute is "creationDate" and there is the following document:

    { "creationDate" : 1550165973 }

This document will be indexed with a creation date time value of `1550165973`, which translates to the human-readable date `2019-02-14T17:39:33.000Z`. The document will expire 600 seconds afterwards, which is at timestamp `1550166573` (or `2019-02-14T17:49:33.000Z` in the human-readable version).

The actual removal of expired documents will not necessarily happen immediately. Expired documents will eventually removed by a background thread that is periodically going through all TTL indexes and removing the expired documents. The frequency for invoking this background thread can be configured using the `--ttl.frequency` startup option. 

There is no guarantee when exactly the removal of expired documents will be carried out, so queries may still find and return documents that have already expired. These will eventually be removed when the background thread kicks in and has capacity to remove the expired documents. It is guaranteed however that only documents which are past their expiration time will actually be removed.
  
!!! note 
    The numeric date time values for the index attribute should be specified in milliseconds since January 1st 1970 (Unix timestamp). To calculate the current timestamp from JavaScript in this format, there is `Date.now() / 1000`, to calculate it from an arbitrary Date instance, there is `Date.getTime() / 1000`.

Alternatively, the index attribute values can be specified as a date string in format `YYYY-MM-DDTHH:MM:SS` with optional milliseconds. All date strings will be interpreted as UTC dates.
    
The above example document using a datestring attribute value would be
 
    { "creationDate" : "2019-02-14T17:39:33.000Z" }

In case the index attribute does not contain a numeric value nor a proper date string, the document will not be stored in the TTL index and thus will not become a candidate for expiration and removal. Providing either a non-numeric value or even no value for the index attribute is a supported way of keeping documents from being expired and removed.

## Geo Index

Users can create additional geo indexes on one or multiple attributes in collections. A geo index is used to find places on the surface of the earth fast. 

The geo index stores two-dimensional coordinates. It can be created on either two separate document attributes (latitude and longitude) or a single array attribute that contains both latitude and longitude. Latitude and longitude must be numeric values.

The geo index provides operations to find documents with coordinates nearest to a given comparison coordinate, and to find documents with coordinates that are within a specifiable radius around a comparison coordinate.

The geo index is used via dedicated functions in C8QL, the simple queries functions and it is implicitly applied when in C8QL a SORT or FILTER is used with the distance function. Otherwise it will not be used for other types of queries or conditions.

## Fulltext Index

A fulltext index can be used to find words, or prefixes of words inside documents. A fulltext index can be created on a single attribute only, and will index all words contained in documents that have a textual value in that attribute. Only words with a (specifiable) minimum length are indexed. Word tokenization is done using the word boundary analysis provided by libicu, which is taking into account the selected language provided at server start. Words are indexed in their lower-cased form. The index supports complete match queries (full words) and prefix queries, plus basic logical operations such as `and`, `or` and `not` for combining partial results.

The fulltext index is sparse, meaning it will only index documents for which the index attribute is set and contains a string value. Additionally, only words with a configurable minimum length will be included in the index.

The fulltext index is used via dedicated functions in C8QL or the simple queries, but will not be enabled for other types of queries or conditions.

