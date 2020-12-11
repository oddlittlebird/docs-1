## Overview

Macrometa GDN Search (aka C8Search) provides information retrieval features, natively integrated into its query language and with support for all data models (`Key Value`, `Documents` and `Graphs`). It is primarily a full-text search engine, a much more powerful alternative to the [full-text index](../../documents/indexing/working-with-indexes#fulltext-indexes) type.

The search engine allows users to combine two information retrieval techniques: `boolean` and `generalized ranking retrieval`. The search results `approved` by the boolean model are `ranked by relevance` to the respective query using the Vector Space Model in conjunction with `BM25` or `TFIDF` weighting schemes.

At a high level, the search engine provides following capabilities to its users:

* Complex Searches with Boolean Operators
* Relevance-Based Matching
* Phrase and Prefix Matching
* Custom Ranking and Relevance Tuning
* Configurable Analyzers & Tokenization
* Return whole documents or projections of documents.
* Combinability of search queries with multiple supported data models & access patterns
* Geo Replicated Search indexes for instant results.

C8Search introduces the concept of `Views` which can be seen as virtual collections. Each `View` represents an inverted index to provide fast full-text searching over one or multiple linked collections and holds the configuration for the search capabilities, such as the attributes to index. 

The views can cover multiple or even all attributes of the documents in the linked collections. Search results can be sorted by their `similarity ranking` to return the best matches first using popular `scoring algorithms`.

Configurable `Analyzers` are available for text processing, such as for tokenization, language-specific word stemming, case conversion, removal of diacritical marks (accents) from characters and more. Analyzers can be used standalone or in combination with Views for sophisticated searching.

The C8Search features are integrated into C8QL as SEARCH operation and a set of C8QL functions.

## Example Use Cases

* Perform federated full-text searches over product descriptions for a web shop, with the product documents stored in various collections.
* Find information in a research database using stemmed phrases, case and accent insensitive, with irrelevant terms removed from the search index (stop word filtering), ranked by relevance based on term frequency (TFIDF).
* Query a movie dataset for titles with words in a particular order (optionally with wildcards), and sort the results by best matching (BM25) but favor movies with a longer duration.

## Views

Search Views enable sophisticated information retrieval queries such as full-text search for unstructured or semi-structured data over documents from different collections, filtering on multiple document attributes and sorting the documents that satisfy the search criteria by relevance.

Comparison with the [Full-text Index](../../documents/indexing/working-with-indexes#fulltext-indexes):

Feature                           | Search | Full-text Index
:---------------------------------|:-------------|:---------------
Term search                       | Yes          | Yes
Prefix search                     | Yes          | Yes
Boolean expressions               | Yes          | Restricted
Range search                      | Yes          | No
Phrase search                     | Yes          | No
Relevance ranking                 | Yes          | No
Configurable Analyzers            | Yes          | No
C8QL composable language construct	| Yes          | No
Indexed attributes per collection | Unlimited    | 1
Indexed collections               | Unlimited    | 1

Views guarantee the best execution plan (merge join) when querying multiple attributes, unlike collections with user-defined indexes.

### View Concepts

A View can be understood as abstraction over a transformation applied to documents of zero or more collections. The transformation is View-implementation specific and may even be as simple as an identity transformation thus making the View represent all documents available in the specified set of source collections. Currently there is a single supported View implementation, Search Views.

Search Views combine two information retrieval models: Boolean and generalized ranking retrieval. Each document "approved" by Boolean model gets its own rank from the ranking model.

For text retrieval, the Vector Space Model (VSM) is used as the ranking model. According to the model, documents and query are represented as vectors in a space formed by the _terms_ of the query. Typically terms are single words, keywords or even phrases. Value analysis such as splitting text into words (tokenization) and normalizing them is possible with the help of [Analyzers](#analyzers).

As this is application dependent GDN offers configurable Analyzers aside from a set of built-in Analyzers.

The document vectors that are closer to a query vector are more relevant. Practically the closeness is expressed as the cosine of the angle between two vectors, namely [cosine similarity](https://en.wikipedia.org/wiki/Cosine_similarity){:target="_blank"}.

In order to define how relevant document `d` is to the query `q` the following expression is evaluated:

`cos a = (d * q) / (|d| * |q|)`, where `d * q` is the dot product of the query vector `q` and document vector `d`, `|d|` is the norm of the vector `d`, `|q|` is the norm of the vector `q`.

The required vector components have to be computed upfront. Since the space is formed by the terms, _term weights_ can be used as the coordinates. There are a number of probability/statistical weighting models of which two are implemented for Search Views, probably the most famous schemes:

- [Okapi BM25](https://en.wikipedia.org/wiki/Okapi_BM25){:target="_blank"}
- [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf){:target="_blank"}

Under the hood both models rely on two main components:

* **Term frequency (TF)**: in the simplest case defined as the number of times that term `t` occurs in document `d`
* **Inverse document frequency (IDF)**: a measure of how much information the word provides, i.e. whether the term is common or rare across all documents

### View Integration

The collections to act as data source need to be _linked_ to a View. An Search Link is a uni-directional connection from an GDN collection to an Search View describing how data coming from the said collection should be made available in the given View. You can think of it as a data flow from a collection to a View. A View can have zero or more links, each to a distinct GDN collection within a database. The same collections may be linked to other Views too.

Search Views are not updated synchronously as the source collections change in order to minimize the performance impact. They are eventually consistent, with a configurable consolidation policy.

Document as well as edge collections can be linked, which means graphs can be treated as flat and interconnected data structure simultaneously. For example, one can find the most relevant vertices by searching and sorting via a View, then do a regular traversal within a specified depth.

Links can be managed by editing the [View Definition](#view-definition). It is possible to index all attributes or particular attributes (optionally including nested attributes). Any document attribute at any depth can be indexed. A list of Analyzers to process the values with can be defined for each such field.

The Analyzer(s) defined in the View and the one(s) specified in a query must match to produce a result. For example, if a field was only indexed using the `"identity"` Analyzer but the search expression compares the value to something using a different Analyzer (e.g. `"text_en"`) then nothing is found.

The elements of arrays are indexed individually by default, as if the source attribute had each element as value at the same time. Strings may get transformed by Analyzers into multiple tokens, which are handled similarly to an array of strings. See [C8QL SEARCH operation](../../c8ql/operations/search) for details. Primitive values other than strings (`null`, `true`, `false`, numbers) are indexed unchanged. The values of nested object are optionally indexed under the respective attribute path, including objects in arrays.

Views can be managed in the Web UI, via an HTTP API and through a JavaScript API.

Finally, Views can be queried with C8QL via the [SEARCH operation](../../c8ql/operations/search).

### Primary Sort Order

The index behind an Search View can have a primary sort order.

A direction can be specified upon View creation for each uniquely named attribute (ascending or descending), to enable an optimization for C8QL queries which iterate over a View and sort by one or multiple of the attributes. If the field(s) and the sorting direction(s) match then the the data can be read directly from the index without actual sort operation.

View definition example:

```json
{
  "links": {
    "coll1": {
      "fields": {
        "text": {
        }
      }
    },
    "coll2": {
      "fields": {
        "text": {
      }
    }
  },
  "primarySort": [
    {
      "field": "text",
      "direction": "asc"
    }
  ]
}
```

C8QL query example:

```js
FOR doc IN viewName
  SORT doc.text
  RETURN doc
```

Execution plan **without** a sorted index being used:

```bash
Execution plan:
 Id   NodeType            Est.   Comment
  1   SingletonNode          1   * ROOT
  2   EnumerateViewNode      1     - FOR doc IN viewName   /* view query */
  3   CalculationNode        1       - LET #1 = doc.`val`   /* attribute expression */
  4   SortNode               1       - SORT #1 ASC   /* sorting strategy: standard */
  5   ReturnNode             1       - RETURN doc
```

Execution plan with a the primary sort order of the index being utilized:

```bash
Execution plan:
 Id   NodeType            Est.   Comment
  1   SingletonNode          1   * ROOT
  2   EnumerateViewNode      1     - FOR doc IN viewName SORT doc.`val` ASC   /* view query */
  5   ReturnNode             1       - RETURN doc
```

To define more than one attribute to sort by, simply add more sub-objects to the `primarySort` array:

```json
  "primarySort": [
    {
      "field": "date",
      "direction": "desc"
    },
    {
      "field": "text",
      "direction": "asc"
    }
  ]
```

The optimization can be applied to View queries which sort by both fields as defined (`SORT doc.date DESC, doc.text`), but also if they sort in descending order by the `date` attribute only (`SORT doc.date DESC`). Queries which sort by `text` alone (`SORT doc.text`) are not eligible, because the View is sorted by `date` first. This is similar to skiplist indexes, but inverted sorting directions are not covered by the View index (e.g. `SORT doc.date, doc.text DESC`).

!!! note
    `primarySort` option is immutable: it can not be changed after View creation. It is therefore not possible to configure it through the Web UI. The View needs to be created via the HTTP or JavaScript API to set it.

### View Definition

An Search View is configured via an object containing a set of View-specific configuration directives and a map of link-specific configuration directives.

During `view creation` the following directives apply:

* **name** (string, _immutable_): the View name
* **type** (string, _immutable_): the value `"search"`
* any of the directives from the section [View Properties](#view-properties).

During `view modification` the following directives apply:

* **links** (object, _optional_): a mapping of `collection-name` / `collection-identifier` to one of:

  * `link creation` - link definition as per the section [Link properties](#link-properties)
  * `link removal` - JSON keyword *null* (i.e. nullify a link if present)

* any of the directives from the section [View Properties](#view-properties)

#### Link Properties

- **analyzers** (_optional_; type: `array`; subtype: `string`; default: `['identity' ]`)

    A list of analyzers, by name as defined via the [Analyzers](#analyzers), that should be applied to values of processed document attributes.

- **fields** (_optional_; type: `object`; default: `{}`)

    An object `{ attribute-name: [Link properties], … }` of fields that should be processed at each level of the document. Each key specifies the document attribute to be processed. Note that the value of `includeAllFields` is also consulted when selecting fields to be processed. It is a recursive data structure. 
  
    Each value specifies the [Link properties](#link-properties) directives to be used when processing the specified field, a Link properties value of `{}` denotes inheritance of all (except `fields`) directives from the current level.

- **includeAllFields** (_optional_; type: `boolean`; default: `false`)

    If set to `true`, then process all document attributes. Otherwise, only consider attributes mentioned in `fields`. Attributes not explicitly specified in `fields` will be processed with default link properties, i.e. `{}`.

- **trackListPositions** (_optional_; type: `boolean`; default: `false`)

    If set to `true`, then for array values track the value position in arrays. 
  
    !!! example
        When querying for the input `{ attr: [ 'valueX', 'valueY', 'valueZ' ] }`, the user must specify: `doc.attr[1] == 'valueY'`. Otherwise, all values in an array are treated as equal alternatives. E.g., when querying for the input `{ attr: [ 'valueX', 'valueY', 'valueZ' ] }`, the user must specify: `doc.attr == 'valueY'`.

- **storeValues** (_optional_; type: `string`; default: `"none"`)

    This property controls how the view should keep track of the attribute values. Valid values are:
      - **none**: Do not store values with the view.
      - **id**: Store information about value presence to allow use of the `EXISTS()` function.

- **inBackground** (_optional_; type: `boolean`; default: `false`)

    If set to `true`, then no exclusive lock is used on the source collection during View index creation, so that it remains basically available.

#### View Properties

- **primarySort** (_optional_; type: `array`; default: `[]`; _immutable_)

    A primary sort order can be defined to enable an C8QL optimization. If a query iterates over all documents of a View, wants to sort them by attribute values and the (left-most) fields to sort by as well as their sorting direction match with the `primarySort` definition, then the `SORT` operation is optimized away. Also see [Primary Sort Order](#primary-sort-order)

#### Additional Notes

An `inverted index` is the heart of Search Views. The index consists of several independent segments and the index **segment** is treated as a standalone index. Some definitions:

*  **Commit**: Procedure of accumulating processed data creating new index segments. 
* **Consolidation** Procedure of joining multiple index segments into a bigger one and removing garbage documents (e.g. deleted from a collection). 
* **Cleanup** Procedure of removing unused segments after release of internal resources.

Search waits at least `2` commits between removing unused files in its data directory for the case where the consolidation policies merge segments often (i.e. a lot of commit+consolidate). 

!!! note
    With every **commit** or **consolidate** operation a new state of the `view internal data-structures` is created on disk. Old `states/snapshots` are released once there are no longer any users remaining. However, the files for the released states/snapshots are left on disk, and only removed by "cleanup" operation.

Search wait at least `1000` milliseconds between committing `view` data store changes and making documents visible to queries.

For data retrieval, Search Views follow the concept of `eventually-consistent`, i.e. eventually all the data in GDN will be matched by corresponding query expressions. 

!!! note
    The concept of Search View `commit` operation is introduced to control the upper-bound on the time until document addition/removals are actually reflected by corresponding query expressions. Once a `commit` operation is complete all documents added/removed prior to the start of the `commit` operation will be reflected by queries invoked in subsequent GDN transactions. GDN transactions in progress will still continue to return a repeatable-read state.

Search waits atleast `60000` milliseconds (i.e., 1 minute) between committing view data store changes and making documents visible to queries.

## Analyzers

Analyzers parse input values and transform them into sets of sub-values, for example by breaking up text into words. If they are used in Views then the documents' attribute values of the linked collections are used as input and additional metadata is produced internally. The data can then be used for searching and sorting to provide the most appropriate match for the specified conditions, similar to queries to web search engines.

Analyzers can be used on their own to tokenize and normalize strings in C8QL queries with the [`TOKENS()` function](../../c8ql/functions/search#tokens).

How analyzers process values depends on their type and configuration. The configuration is comprised of type-specific properties and list of features. The features control the additional metadata to be generated to augment View indexes, to be able to rank results for instance.

Analyzers can be managed via an HTTP API and through SDKs.

### Analyzer Value Handling

While most of the Analyzer functionality is geared towards text processing, there is no restriction to strings as input data type when using them through Views – your documents could have attributes of any data type after all.

Strings are processed according to the Analyzer, whereas other primitive data types (`null`, `true`, `false`, numbers) are added to the index unchanged.

The elements of arrays are unpacked, processed and indexed individually, regardless of the level of nesting. That is, strings are processed by the configured Analyzer(s) and other primitive values are indexed as-is.

Objects, including any nested objects, are indexed as sub-attributes. This applies to sub-objects as well as objects in arrays. Only primitive values are added to the index, arrays and objects can not be searched for.

Also see:

* [SEARCH operation](../../c8ql/operations/search) on how to query indexed values such as numbers and nested values.
* [Search Views](#views) for details about how compound data types (`arrays`, `objects`) get indexed.

### Analyzer Names

Each Analyzer has a name for identification with the following naming conventions, similar to collection names:

* The name must only consist of the letters `a` to `z` (both in lower and upper case), the numbers `0` to `9`, underscore (`_`) and dash (`-`) symbols. This also means that any non-ASCII names are not allowed.
* It must always start with a letter.
* The maximum allowed length of a name is `64` bytes.
* Analyzer names are case-sensitive.

Custom Analyzers are stored per database, in a system collection `_analyzers` for the tenant. The names get prefixed with the database name and two colons, e.g. `myDB::customAnalyzer`.This does not apply to the globally available [built-in Analyzers](#built-in-analyzers).

Custom Analyzers stored in the default `_system` database of the tenant and can be referenced in queries against other databases of the tenant by specifying the prefixed name, e.g.

* `_system::customGlobalAnalyzer`. Analyzers stored in databases other than
* `_system` can not be accessed from within another database however.

### Analyzer Types

The currently implemented Analyzer types are:

* `identity`: treat value as atom (no transformation)
* `delimiter`: split into tokens at user-defined character
* `stem`: apply stemming to the value as a whole
* `norm`: apply normalization to the value as a whole
* `ngram`: create n-grams from value with user-defined lengths
* `text`: tokenize into words, optionally with stemming, normalization and stop-word filtering

Available normalizations are case conversion and accent removal (conversion of characters with diacritical marks to the base characters).

|Feature / Analyzer | Identity | N-gram  | Delimiter | Stem | Norm | Text|
|:------------------|:---------|:--------|:----------|:-----|:-----|:----|
|**Tokenization**   | No       | No      | (Yes)     | No   | No   | Yes |
|**Stemming**       | No       | No      | No        | Yes  | No   | Yes |
|**Normalization**  | No       | No      | No        | No   | Yes  | Yes |

### Analyzer Properties

The valid attributes/values for the *properties* are dependant on what *type* is used. For example, the `delimiter` type needs to know the desired delimiting character(s), whereas the `text` type takes a locale, stop-words and more.

#### Identity

An Analyzer applying the `identity` transformation, i.e. returning the input unmodified.

It does not support any *properties* and will ignore them.

#### Delimiter

An Analyzer capable of breaking up delimited text into tokens as per [RFC 4180](https://tools.ietf.org/html/rfc4180) (without starting new records on newlines).

The `properties` allowed for this Analyzer are an object with the following attributes:

- `delimiter` (string): the delimiting character(s)

#### Stem

An Analyzer capable of stemming the text, treated as a single token, for supported languages.

The *properties* allowed for this Analyzer are an object with the following attributes:

- `locale` (string): a locale in the format `language[_COUNTRY][.encoding][@variant]` (square brackets denote optional parts), e.g. `"de.utf-8"` or `"en_US.utf-8"`. Only UTF-8 encoding is meaningful in GDN. Also see [Supported Languages](#supported-languages).

#### Norm

An Analyzer capable of normalizing the text, treated as a single token, i.e. case conversion and accent removal.

The *properties* allowed for this Analyzer are an object with the following attributes:

* `locale` (string): a locale in the format `language[_COUNTRY][.encoding][@variant]` (square brackets denote optional parts), e.g. `"de.utf-8"` or `"en_US.utf-8"`. Only UTF-8 encoding is meaningful in GDN. Also see [Supported Languages](#supported-languages).

* `accent` (boolean, _optional_):

  * `true` to preserve accented characters (default)
  * `false` to convert accented characters to their base characters

* `case` (string, _optional_):
  * `"lower"` to convert to all lower-case characters
  * `"upper"` to convert to all upper-case characters
  * `"none"` to not change character case (default)

#### N-gram

An Analyzer capable of producing n-grams from a specified input in a range of min..max (inclusive). Can optionally preserve the original input.

This Analyzer type can be used to implement substring matching. Note that it currently supports single-byte characters only. Multi-byte UTF-8 characters raise an *Invalid UTF-8 sequence* query error.

The *properties* allowed for this Analyzer are an object with the following attributes:

* `min` (number): unsigned integer for the minimum n-gram length
* `max` (number): unsigned integer for the maximum n-gram length - `preserveOriginal` (boolean):

  * `true` to include the original value as well
  * `false` to produce the n-grams based on *min* and *max* only

**Example**

With `min` = 4 and `max` = 5, the analyzer will produce the following n-grams for the input string `"foobar"`:

* `"foobar"` (if `preserveOriginal` is enabled)
* `"fooba"`
* `"foob"`
* `"oobar"`
* `"ooba"`
* `"obar"`

An input string `"foo"` will not produce any n-gram because it is shorter than the `min` length of 4.

#### Text

An Analyzer capable of breaking up strings into individual words while also optionally filtering out stop-words, extracting word stems, applying case conversion and accent removal.

The *properties* allowed for this Analyzer are an object with the following attributes:

* `locale` (string): a locale in the format `language[_COUNTRY][.encoding][@variant]` (square brackets denote optional parts), e.g. `"de.utf-8"` or `"en_US.utf-8"`. Only UTF-8 encoding is meaningful in GDN. Also see [Supported Languages](#supported-languages).

* `accent` (boolean, _optional_):

  * `true` to preserve accented characters
  * `false` to convert accented characters to their base characters (default)

* `case` (string, _optional_):

  * `"lower"` to convert to all lower-case characters (default)
  * `"upper"` to convert to all upper-case characters
  * `"none"` to not change character case

* `stemming` (boolean, _optional_):

  * `true` to apply stemming on returned words (default)
  * `false` to leave the tokenized words as-is

* `stopwords` (array, _optional_): an array of strings with words to omit from result. Default: load words from `stopwordsPath`. To disable stop-word filtering provide an empty array `[]`. If both *stopwords* and *stopwordsPath* are provided then both word sources are combined.

* `stopwordsPath` (string, _optional_): path with a *language* sub-directory (e.g. `en` for a locale `en_US.utf-8`) containing files with words to omit. Each word has to be on a separate line. Everything after the first whitespace character on a line will be ignored and can be used for comments. The files can be named arbitrarily and have any file extension (or none).

  Default: if no path is provided then the value of the environment variable `IRESEARCH_TEXT_STOPWORD_PATH` is used to determine the path, or if it is undefined then the current working directory is assumed. If the `stopwords` attribute is provided then no stop-words are loaded from files, unless an explicit *stopwordsPath* is also provided.

  Note that if the *stopwordsPath* can not be accessed, is missing language sub-directories or has no files for a language required by an Analyzer, then the creation of a new Analyzer is refused. If such an issue is discovered for an existing Analyzer during startup then the server will abort with a fatal error.

### Analyzer Features

The *features* of an Analyzer determine what term matching capabilities will be available and as such are only applicable in the context of Search Views.

The valid values for the features are dependant on both the capabilities of the underlying *type* and the query filtering and sorting functions that the result can be used with. For example the *text* type will produce `frequency` + `norm` + `position` and the `PHRASE()` C8QL function requires `frequency` + `position` to be available.

Currently the following *features* are supported:

* **frequency**: how often a term is seen, required for `PHRASE()`
* **norm**: the field normalization factor
* **position**: sequentially increasing term position, required for `PHRASE()`.

  If present then the `frequency` feature is also required

### Built-in Analyzers

There is a set of built-in analyzers which are available by default for convenience and backward compatibility. They can not be removed.

The `identity` analyzer has the features `frequency` and `norm`. The analyzers of type `text` all tokenize strings with stemming enabled, no stopwords configured, case conversion set to `lower`, accent removal turned on and the features `frequency`, `norm` and `position`:

Name       | Type       | Language
-----------|------------|-----------
`identity` | `identity` | none
`text_de`  | `text`     | German
`text_en`  | `text`     | English
`text_es`  | `text`     | Spanish
`text_fi`  | `text`     | Finnish
`text_fr`  | `text`     | French
`text_it`  | `text`     | Italian
`text_nl`  | `text`     | Dutch
`text_no`  | `text`     | Norwegian
`text_pt`  | `text`     | Portuguese
`text_ru`  | `text`     | Russian
`text_sv`  | `text`     | Swedish
`text_zh`  | `text`     | Chinese

### Supported Languages

Analyzers rely on [ICU](http://site.icu-project.org/){:target="_blank"} for language-dependent tokenization and normalization. The ICU data file `icudtl.dat` that GDN ships with contains information for a lot of languages, which are technically all supported.

!!! note
    The alphabetical order of characters is not taken into account by Search, i.e. range queries in SEARCH operations against Views will not follow the language rules as per the defined Analyzer locale.

Stemming support is provided by [Snowball](https://snowballstem.org/){:target="_blank"}, which supports the following languages:

Code  | Language
------|-----------
`de`  | German
`en`  | English
`es`  | Spanish
`fi`  | Finnish
`fr`  | French
`it`  | Italian
`nl`  | Dutch
`no`  | Norwegian
`pt`  | Portuguese
`ru`  | Russian
`sv`  | Swedish
`zh`  | Chinese