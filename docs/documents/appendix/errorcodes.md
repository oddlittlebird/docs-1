# Error Codes

## General errors

* <a name="ERROR_NO_ERROR"></a>**0** - **ERROR_NO_ERROR**<br>
  No error has occurred.

* <a name="ERROR_FAILED"></a>**1** - **ERROR_FAILED**<br>
  Will be raised when a general error occurred.

* <a name="ERROR_SYS_ERROR"></a>**2** - **ERROR_SYS_ERROR**<br>
  Will be raised when operating system error occurred.

* <a name="ERROR_OUT_OF_MEMORY"></a>**3** - **ERROR_OUT_OF_MEMORY**<br>
  Will be raised when there is a memory shortage.

* <a name="ERROR_INTERNAL"></a>**4** - **ERROR_INTERNAL**<br>
  Will be raised when an internal error occurred.

* <a name="ERROR_ILLEGAL_NUMBER"></a>**5** - **ERROR_ILLEGAL_NUMBER**<br>
  Will be raised when an illegal representation of a number was given.

* <a name="ERROR_NUMERIC_OVERFLOW"></a>**6** - **ERROR_NUMERIC_OVERFLOW**<br>
  Will be raised when a numeric overflow occurred.

* <a name="ERROR_ILLEGAL_OPTION"></a>**7** - **ERROR_ILLEGAL_OPTION**<br>
  Will be raised when an unknown option was supplied by the user.

* <a name="ERROR_DEAD_PID"></a>**8** - **ERROR_DEAD_PID**<br>
  Will be raised when a PID without a living process was found.

* <a name="ERROR_NOT_IMPLEMENTED"></a>**9** - **ERROR_NOT_IMPLEMENTED**<br>
  Will be raised when hitting an unimplemented feature.

* <a name="ERROR_BAD_PARAMETER"></a>**10** - **ERROR_BAD_PARAMETER**<br>
  Will be raised when the parameter does not fulfill the requirements.

* <a name="ERROR_FORBIDDEN"></a>**11** - **ERROR_FORBIDDEN**<br>
  Will be raised when you are missing permission for the operation.

* <a name="ERROR_OUT_OF_MEMORY_MMAP"></a>**12** - **ERROR_OUT_OF_MEMORY_MMAP**<br>
  Will be raised when there is a memory shortage.

* <a name="ERROR_CORRUPTED_CSV"></a>**13** - **ERROR_CORRUPTED_CSV**<br>
  Will be raised when encountering a corrupt csv line.

* <a name="ERROR_FILE_NOT_FOUND"></a>**14** - **ERROR_FILE_NOT_FOUND**<br>
  Will be raised when a file is not found.

* <a name="ERROR_CANNOT_WRITE_FILE"></a>**15** - **ERROR_CANNOT_WRITE_FILE**<br>
  Will be raised when a file cannot be written.

* <a name="ERROR_CANNOT_OVERWRITE_FILE"></a>**16** - **ERROR_CANNOT_OVERWRITE_FILE**<br>
  Will be raised when an attempt is made to overwrite an existing file.

* <a name="ERROR_TYPE_ERROR"></a>**17** - **ERROR_TYPE_ERROR**<br>
  Will be raised when a type error is unencountered.

* <a name="ERROR_LOCK_TIMEOUT"></a>**18** - **ERROR_LOCK_TIMEOUT**<br>
  Will be raised when there's a timeout waiting for a lock.

* <a name="ERROR_CANNOT_CREATE_DIRECTORY"></a>**19** - **ERROR_CANNOT_CREATE_DIRECTORY**<br>
  Will be raised when an attempt to create a directory fails.

* <a name="ERROR_CANNOT_CREATE_TEMP_FILE"></a>**20** - **ERROR_CANNOT_CREATE_TEMP_FILE**<br>
  Will be raised when an attempt to create a temporary file fails.

* <a name="ERROR_REQUEST_CANCELED"></a>**21** - **ERROR_REQUEST_CANCELED**<br>
  Will be raised when a request is canceled by the user.

* <a name="ERROR_DEBUG"></a>**22** - **ERROR_DEBUG**<br>
  Will be raised intentionally during debugging.

* <a name="ERROR_IP_ADDRESS_INVALID"></a>**25** - **ERROR_IP_ADDRESS_INVALID**<br>
  Will be raised when the structure of an IP address is invalid.

* <a name="ERROR_FILE_EXISTS"></a>**27** - **ERROR_FILE_EXISTS**<br>
  Will be raised when a file already exists.

* <a name="ERROR_LOCKED"></a>**28** - **ERROR_LOCKED**<br>
  Will be raised when a resource or an operation is locked.

* <a name="ERROR_DEADLOCK"></a>**29** - **ERROR_DEADLOCK**<br>
  Will be raised when a deadlock is detected when accessing collections.

* <a name="ERROR_SHUTTING_DOWN"></a>**30** - **ERROR_SHUTTING_DOWN**<br>
  Will be raised when a call cannot succeed because a server shutdown is already in progress.

* <a name="ERROR_ONLY_ENTERPRISE"></a>**31** - **ERROR_ONLY_ENTERPRISE**<br>
  Will be raised when an enterprise-feature is requested from the Macrometa C8DB.

* <a name="ERROR_RESOURCE_LIMIT"></a>**32** - **ERROR_RESOURCE_LIMIT**<br>
  Will be raised when the resources used by an operation exceed the configured maximum value.

## HTTP error status codes

* <a name="ERROR_HTTP_BAD_PARAMETER"></a>**400** - **ERROR_HTTP_BAD_PARAMETER**<br>
  Will be raised when the HTTP request does not fulfill the requirements.

* <a name="ERROR_HTTP_UNAUTHORIZED"></a>**401** - **ERROR_HTTP_UNAUTHORIZED**<br>
  Will be raised when authorization is required but the user is not authorized.

* <a name="ERROR_HTTP_FORBIDDEN"></a>**403** - **ERROR_HTTP_FORBIDDEN**<br>
  Will be raised when the operation is forbidden.

* <a name="ERROR_HTTP_NOT_FOUND"></a>**404** - **ERROR_HTTP_NOT_FOUND**<br>
  Will be raised when an URI is unknown.

* <a name="ERROR_HTTP_METHOD_NOT_ALLOWED"></a>**405** - **ERROR_HTTP_METHOD_NOT_ALLOWED**<br>
  Will be raised when an unsupported HTTP method is used for an operation.

* <a name="ERROR_HTTP_NOT_ACCEPTABLE"></a>**406** - **ERROR_HTTP_NOT_ACCEPTABLE**<br>
  Will be raised when an unsupported HTTP content type is used for an operation, or if a request is not acceptable for a leader or follower.

* <a name="ERROR_HTTP_PRECONDITION_FAILED"></a>**412** - **ERROR_HTTP_PRECONDITION_FAILED**<br>
  Will be raised when a precondition for an HTTP request is not met.

* <a name="ERROR_HTTP_SERVER_ERROR"></a>**500** - **ERROR_HTTP_SERVER_ERROR**<br>
  Will be raised when an internal server is encountered.

* <a name="ERROR_HTTP_SERVICE_UNAVAILABLE"></a>**503** - **ERROR_HTTP_SERVICE_UNAVAILABLE**<br>
  Will be raised when a service is temporarily unavailable.

* <a name="ERROR_HTTP_GATEWAY_TIMEOUT"></a>**504** - **ERROR_HTTP_GATEWAY_TIMEOUT**<br>
  Will be raised when a service contacted by C8Db_Db does not respond in a timely manner.

## HTTP processing errors

* <a name="ERROR_HTTP_CORRUPTED_JSON"></a>**600** - **ERROR_HTTP_CORRUPTED_JSON**<br>
  Will be raised when a string representation of a JSON object is corrupt.

* <a name="ERROR_HTTP_SUPERFLUOUS_SUFFICES"></a>**601** - **ERROR_HTTP_SUPERFLUOUS_SUFFICES**<br>
  Will be raised when the URL contains superfluous suffices.

## Internal C8 errors

For errors that occur because of a programming error.

* <a name="ERROR_C8DB_ILLEGAL_STATE"></a>**1000** - **ERROR_C8DB_ILLEGAL_STATE**<br>
  Internal error that will be raised when the datafile is not in the required state.

* <a name="ERROR_C8DB_DATAFILE_SEALED"></a>**1002** - **ERROR_C8DB_DATAFILE_SEALED**<br>
  Internal error that will be raised when trying to write to a datafile.

* <a name="ERROR_C8DB_READ_ONLY"></a>**1004** - **ERROR_C8DB_READ_ONLY**<br>
  Internal error that will be raised when trying to write to a read-only datafile or collection.

* <a name="ERROR_C8DB_DUPLICATE_IDENTIFIER"></a>**1005** - **ERROR_C8DB_DUPLICATE_IDENTIFIER**<br>
  Internal error that will be raised when a identifier duplicate is detected.

* <a name="ERROR_C8DB_DATAFILE_UNREADABLE"></a>**1006** - **ERROR_C8DB_DATAFILE_UNREADABLE**<br>
  Internal error that will be raised when a datafile is unreadable.

* <a name="ERROR_C8DB_DATAFILE_EMPTY"></a>**1007** - **ERROR_C8DB_DATAFILE_EMPTY**<br>
  Internal error that will be raised when a datafile is empty.

* <a name="ERROR_C8DB_RECOVERY"></a>**1008** - **ERROR_C8DB_RECOVERY**<br>
  Will be raised when an error occurred during WAL log file recovery.

* <a name="ERROR_C8DB_DATAFILE_STATISTICS_NOT_FOUND"></a>**1009** - **ERROR_C8DB_DATAFILE_STATISTICS_NOT_FOUND**<br>
  Will be raised when a required datafile statistics object was not found.

## External C8 errors

For errors that occur because of an outside event.

* <a name="ERROR_C8DB_CORRUPTED_DATAFILE"></a>**1100** - **ERROR_C8DB_CORRUPTED_DATAFILE**<br>
  Will be raised when a corruption is detected in a datafile.

* <a name="ERROR_C8DB_ILLEGAL_PARAMETER_FILE"></a>**1101** - **ERROR_C8DB_ILLEGAL_PARAMETER_FILE**<br>
  Will be raised if a parameter file is corrupted or cannot be read.

* <a name="ERROR_C8DB_CORRUPTED_COLLECTION"></a>**1102** - **ERROR_C8DB_CORRUPTED_COLLECTION**<br>
  Will be raised when a collection contains one or more corrupted data files.

* <a name="ERROR_C8DB_MMAP_FAILED"></a>**1103** - **ERROR_C8DB_MMAP_FAILED**<br>
  Will be raised when the system call mmap failed.

* <a name="ERROR_C8DB_FILESYSTEM_FULL"></a>**1104** - **ERROR_C8DB_FILESYSTEM_FULL**<br>
  Will be raised when the filesystem is full.

* <a name="ERROR_C8DB_NO_JOURNAL"></a>**1105** - **ERROR_C8DB_NO_JOURNAL**<br>
  Will be raised when a journal cannot be created.

* <a name="ERROR_C8DB_DATAFILE_ALREADY_EXISTS"></a>**1106** - **ERROR_C8DB_DATAFILE_ALREADY_EXISTS**<br>
  Will be raised when the datafile cannot be created or renamed because a file of the same name already exists.

* <a name="ERROR_C8DB_DATADIR_LOCKED"></a>**1107** - **ERROR_C8DB_DATADIR_LOCKED**<br>
  Will be raised when the database directory is locked by a different process.

* <a name="ERROR_C8DB_COLLECTION_DIRECTORY_ALREADY_EXISTS"></a>**1108** - **ERROR_C8DB_COLLECTION_DIRECTORY_ALREADY_EXISTS**<br>
  Will be raised when the collection cannot be created because a directory of the same name already exists.

* <a name="ERROR_C8DB_MSYNC_FAILED"></a>**1109** - **ERROR_C8DB_MSYNC_FAILED**<br>
  Will be raised when the system call msync failed.

* <a name="ERROR_C8DB_DATADIR_UNLOCKABLE"></a>**1110** - **ERROR_C8DB_DATADIR_UNLOCKABLE**<br>
  Will be raised when the server cannot lock the database directory on startup.

* <a name="ERROR_C8DB_SYNC_TIMEOUT"></a>**1111** - **ERROR_C8DB_SYNC_TIMEOUT**<br>
  Will be raised when the server waited too long for a datafile to be synced to disk.

## General C8 errors

For errors that occur when fulfilling a user request.

* <a name="ERROR_C8DB_CONFLICT"></a>**1200** - **ERROR_C8DB_CONFLICT**<br>
  Will be raised when updating or deleting a document and a conflict has been detected.

* <a name="ERROR_C8DB_DATADIR_INVALID"></a>**1201** - **ERROR_C8DB_DATADIR_INVALID**<br>
  Will be raised when a non-existing database directory was specified when starting the database.

* <a name="ERROR_C8DB_DOCUMENT_NOT_FOUND"></a>**1202** - **ERROR_C8DB_DOCUMENT_NOT_FOUND**<br>
  Will be raised when a document with a given identifier or handle is unknown.

* <a name="ERROR_C8DB_COLLECTION_NOT_FOUND"></a>**1203** - **ERROR_C8DB_COLLECTION_NOT_FOUND**<br>
  Will be raised when a collection with the given identifier or name is unknown.

* <a name="ERROR_C8DB_COLLECTION_PARAMETER_MISSING"></a>**1204** - **ERROR_C8DB_COLLECTION_PARAMETER_MISSING**<br>
  Will be raised when the collection parameter is missing.

* <a name="ERROR_C8DB_DOCUMENT_HANDLE_BAD"></a>**1205** - **ERROR_C8DB_DOCUMENT_HANDLE_BAD**<br>
  Will be raised when a document handle is corrupt.

* <a name="ERROR_C8DB_MAXIMAL_SIZE_TOO_SMALL"></a>**1206** - **ERROR_C8DB_MAXIMAL_SIZE_TOO_SMALL**<br>
  Will be raised when the maximal size of the journal is too small.

* <a name="ERROR_C8DB_DUPLICATE_NAME"></a>**1207** - **ERROR_C8DB_DUPLICATE_NAME**<br>
  Will be raised when a name duplicate is detected.

* <a name="ERROR_C8DB_ILLEGAL_NAME"></a>**1208** - **ERROR_C8DB_ILLEGAL_NAME**<br>
  Will be raised when an illegal name is detected.

* <a name="ERROR_C8DB_NO_INDEX"></a>**1209** - **ERROR_C8DB_NO_INDEX**<br>
  Will be raised when no suitable index for the query is known.

* <a name="ERROR_C8DB_UNIQUE_CONSTRAINT_VIOLATED"></a>**1210** - **ERROR_C8DB_UNIQUE_CONSTRAINT_VIOLATED**<br>
  Will be raised when there is a unique constraint violation.

* <a name="ERROR_C8DB_VIEW_NOT_FOUND"></a>**1211** - **ERROR_C8DB_VIEW_NOT_FOUND**<br>
  Will be raised when a view with the given identifier or name is unknown.

* <a name="ERROR_C8DB_INDEX_NOT_FOUND"></a>**1212** - **ERROR_C8DB_INDEX_NOT_FOUND**<br>
  Will be raised when an index with a given identifier is unknown.

* <a name="ERROR_C8DB_CROSS_COLLECTION_REQUEST"></a>**1213** - **ERROR_C8DB_CROSS_COLLECTION_REQUEST**<br>
  Will be raised when a cross-collection is requested.

* <a name="ERROR_C8DB_INDEX_HANDLE_BAD"></a>**1214** - **ERROR_C8DB_INDEX_HANDLE_BAD**<br>
  Will be raised when a index handle is corrupt.

* <a name="ERROR_C8DB_DOCUMENT_TOO_LARGE"></a>**1216** - **ERROR_C8DB_DOCUMENT_TOO_LARGE**<br>
  Will be raised when the document cannot fit into any datafile because of it is too large.

* <a name="ERROR_C8DB_COLLECTION_NOT_UNLOADED"></a>**1217** - **ERROR_C8DB_COLLECTION_NOT_UNLOADED**<br>
  Will be raised when a collection should be unloaded, but has a different status.

* <a name="ERROR_C8DB_COLLECTION_TYPE_INVALID"></a>**1218** - **ERROR_C8DB_COLLECTION_TYPE_INVALID**<br>
  Will be raised when an invalid collection type is used in a request.

* <a name="ERROR_C8DB_VALIDATION_FAILED"></a>**1219** - **ERROR_C8DB_VALIDATION_FAILED**<br>
  Will be raised when the validation of an attribute of a structure failed.

* <a name="ERROR_C8DB_ATTRIBUTE_PARSER_FAILED"></a>**1220** - **ERROR_C8DB_ATTRIBUTE_PARSER_FAILED**<br>
  Will be raised when parsing an attribute name definition failed.

* <a name="ERROR_C8DB_DOCUMENT_KEY_BAD"></a>**1221** - **ERROR_C8DB_DOCUMENT_KEY_BAD**<br>
  Will be raised when a document key is corrupt.

* <a name="ERROR_C8DB_DOCUMENT_KEY_UNEXPECTED"></a>**1222** - **ERROR_C8DB_DOCUMENT_KEY_UNEXPECTED**<br>
  Will be raised when a user-defined document key is supplied for collections with auto key generation.

* <a name="ERROR_C8DB_DATADIR_NOT_WRITABLE"></a>**1224** - **ERROR_C8DB_DATADIR_NOT_WRITABLE**<br>
  Will be raised when the server's database directory is not writable for the current user.

* <a name="ERROR_C8DB_OUT_OF_KEYS"></a>**1225** - **ERROR_C8DB_OUT_OF_KEYS**<br>
  Will be raised when a key generator runs out of keys.

* <a name="ERROR_C8DB_DOCUMENT_KEY_MISSING"></a>**1226** - **ERROR_C8DB_DOCUMENT_KEY_MISSING**<br>
  Will be raised when a document key is missing.

* <a name="ERROR_C8DB_DOCUMENT_TYPE_INVALID"></a>**1227** - **ERROR_C8DB_DOCUMENT_TYPE_INVALID**<br>
  Will be raised when there is an attempt to create a document with an invalid type.

* <a name="ERROR_C8DB_DATABASE_NOT_FOUND"></a>**1228** - **ERROR_C8DB_DATABASE_NOT_FOUND**<br>
  Will be raised when a non-existing database is accessed.

* <a name="ERROR_C8DB_DATABASE_NAME_INVALID"></a>**1229** - **ERROR_C8DB_DATABASE_NAME_INVALID**<br>
  Will be raised when an invalid database name is used.

* <a name="ERROR_C8DB_USE_SYSTEM_DATABASE"></a>**1230** - **ERROR_C8DB_USE_SYSTEM_DATABASE**<br>
  Will be raised when an operation is requested in a database other than the system database.

* <a name="ERROR_C8DB_ENDPOINT_NOT_FOUND"></a>**1231** - **ERROR_C8DB_ENDPOINT_NOT_FOUND**<br>
  Will be raised when there is an attempt to delete a non-existing endpoint.

* <a name="ERROR_C8DB_INVALID_KEY_GENERATOR"></a>**1232** - **ERROR_C8DB_INVALID_KEY_GENERATOR**<br>
  Will be raised when an invalid key generator description is used.

* <a name="ERROR_C8DB_INVALID_EDGE_ATTRIBUTE"></a>**1233** - **ERROR_C8DB_INVALID_EDGE_ATTRIBUTE**<br>
  will be raised when the _from or _to values of an edge are undefined or contain an invalid value.

* <a name="ERROR_C8DB_INDEX_DOCUMENT_ATTRIBUTE_MISSING"></a>**1234** - **ERROR_C8DB_INDEX_DOCUMENT_ATTRIBUTE_MISSING**<br>
  Will be raised when an attempt to insert a document into an index is caused by in the document not having one or more attributes which the index is built on.

* <a name="ERROR_C8DB_INDEX_CREATION_FAILED"></a>**1235** - **ERROR_C8DB_INDEX_CREATION_FAILED**<br>
  Will be raised when an attempt to create an index has failed.

* <a name="ERROR_C8DB_WRITE_THROTTLE_TIMEOUT"></a>**1236** - **ERROR_C8DB_WRITE_THROTTLE_TIMEOUT**<br>
  Will be raised when the server is write-throttled and a write operation has waited too long for the server to process queued operations.

* <a name="ERROR_C8DB_COLLECTION_TYPE_MISMATCH"></a>**1237** - **ERROR_C8DB_COLLECTION_TYPE_MISMATCH**<br>
  Will be raised when a collection has a different type from what has been expected.

* <a name="ERROR_C8DB_COLLECTION_NOT_LOADED"></a>**1238** - **ERROR_C8DB_COLLECTION_NOT_LOADED**<br>
  Will be raised when a collection is accessed that is not yet loaded.

* <a name="ERROR_C8DB_DOCUMENT_REV_BAD"></a>**1239** - **ERROR_C8DB_DOCUMENT_REV_BAD**<br>
  Will be raised when a document revision is corrupt or is missing where needed.

## Checked C8 errors

For errors that occur but are anticipated.

* <a name="ERROR_C8DB_DATAFILE_FULL"></a>**1300** - **ERROR_C8DB_DATAFILE_FULL**<br>
  Will be raised when the datafile reaches its limit.

* <a name="ERROR_C8DB_EMPTY_DATADIR"></a>**1301** - **ERROR_C8DB_EMPTY_DATADIR**<br>
  Will be raised when encountering an empty server database directory.

* <a name="ERROR_C8DB_TRY_AGAIN"></a>**1302** - **ERROR_C8DB_TRY_AGAIN**<br>
  Will be raised when an operation should be retried.

* <a name="ERROR_C8DB_BUSY"></a>**1303** - **ERROR_C8DB_BUSY**<br>
  Will be raised when storage engine is busy.

* <a name="ERROR_C8DB_MERGE_IN_PROGRESS"></a>**1304** - **ERROR_C8DB_MERGE_IN_PROGRESS**<br>
  Will be raised when storage engine has a datafile merge in progress and cannot complete the operation.

* <a name="ERROR_C8DB_IO_ERROR"></a>**1305** - **ERROR_C8DB_IO_ERROR**<br>
  Will be raised when storage engine encounters an I/O error.

## C8 replication errors

* <a name="ERROR_REPLICATION_NO_RESPONSE"></a>**1400** - **ERROR_REPLICATION_NO_RESPONSE**<br>
  Will be raised when the replication applier does not receive any or an incomplete response from the master.

* <a name="ERROR_REPLICATION_INVALID_RESPONSE"></a>**1401** - **ERROR_REPLICATION_INVALID_RESPONSE**<br>
  Will be raised when the replication applier receives an invalid response from the master.

* <a name="ERROR_REPLICATION_MASTER_ERROR"></a>**1402** - **ERROR_REPLICATION_MASTER_ERROR**<br>
  Will be raised when the replication applier receives a server error from the master.

* <a name="ERROR_REPLICATION_MASTER_INCOMPATIBLE"></a>**1403** - **ERROR_REPLICATION_MASTER_INCOMPATIBLE**<br>
  Will be raised when the replication applier connects to a master that has an incompatible version.

* <a name="ERROR_REPLICATION_MASTER_CHANGE"></a>**1404** - **ERROR_REPLICATION_MASTER_CHANGE**<br>
  Will be raised when the replication applier connects to a different master than before.

* <a name="ERROR_REPLICATION_LOOP"></a>**1405** - **ERROR_REPLICATION_LOOP**<br>
  Will be raised when the replication applier is asked to connect to itself for replication.

* <a name="ERROR_REPLICATION_UNEXPECTED_MARKER"></a>**1406** - **ERROR_REPLICATION_UNEXPECTED_MARKER**<br>
  Will be raised when an unexpected marker is found in the replication log stream.

* <a name="ERROR_REPLICATION_INVALID_APPLIER_STATE"></a>**1407** - **ERROR_REPLICATION_INVALID_APPLIER_STATE**<br>
  Will be raised when an invalid replication applier state file is found.

* <a name="ERROR_REPLICATION_UNEXPECTED_TRANSACTION"></a>**1408** - **ERROR_REPLICATION_UNEXPECTED_TRANSACTION**<br>
  Will be raised when an unexpected transaction id is found.

* <a name="ERROR_REPLICATION_INVALID_APPLIER_CONFIGURATION"></a>**1410** - **ERROR_REPLICATION_INVALID_APPLIER_CONFIGURATION**<br>
  Will be raised when the configuration for the replication applier is invalid.

* <a name="ERROR_REPLICATION_RUNNING"></a>**1411** - **ERROR_REPLICATION_RUNNING**<br>
  Will be raised when there is an attempt to perform an operation while the replication applier is running.

* <a name="ERROR_REPLICATION_APPLIER_STOPPED"></a>**1412** - **ERROR_REPLICATION_APPLIER_STOPPED**<br>
  Special error code used to indicate the replication applier was stopped by a user.

* <a name="ERROR_REPLICATION_NO_START_TICK"></a>**1413** - **ERROR_REPLICATION_NO_START_TICK**<br>
  Will be raised when the replication applier is started without a known start tick value.

* <a name="ERROR_REPLICATION_START_TICK_NOT_PRESENT"></a>**1414** - **ERROR_REPLICATION_START_TICK_NOT_PRESENT**<br>
  Will be raised when the replication applier fetches data using a start tick, but that start tick is not present on the logger server anymore.

* <a name="ERROR_REPLICATION_WRONG_CHECKSUM"></a>**1416** - **ERROR_REPLICATION_WRONG_CHECKSUM**<br>
  Will be raised when a new born follower submits a wrong checksum

* <a name="ERROR_REPLICATION_SHARD_NONEMPTY"></a>**1417** - **ERROR_REPLICATION_SHARD_NONEMPTY**<br>
  Will be raised when a shard is not empty and the follower tries a shortcut

## C8 cluster errors

* <a name="ERROR_CLUSTER_NO_AGENCY"></a>**1450** - **ERROR_CLUSTER_NO_AGENCY**<br>
  Will be raised when none of the agency servers can be connected to.

* <a name="ERROR_CLUSTER_NO_COORDINATOR_HEADER"></a>**1451** - **ERROR_CLUSTER_NO_COORDINATOR_HEADER**<br>
  Will be raised when a DB server in a cluster receives a HTTP request without a coordinator header.

* <a name="ERROR_CLUSTER_COULD_NOT_LOCK_PLAN"></a>**1452** - **ERROR_CLUSTER_COULD_NOT_LOCK_PLAN**<br>
  Will be raised when a coordinator in a cluster cannot lock the Plan hierarchy in the agency.

* <a name="ERROR_CLUSTER_COLLECTION_ID_EXISTS"></a>**1453** - **ERROR_CLUSTER_COLLECTION_ID_EXISTS**<br>
  Will be raised when a coordinator in a cluster tries to create a collection and the collection ID already exists.

* <a name="ERROR_CLUSTER_COULD_NOT_CREATE_COLLECTION_IN_PLAN"></a>**1454** - **ERROR_CLUSTER_COULD_NOT_CREATE_COLLECTION_IN_PLAN**<br>
  Will be raised when a coordinator in a cluster cannot create an entry for a new collection in the Plan hierarchy in the agency.

* <a name="ERROR_CLUSTER_COULD_NOT_READ_CURRENT_VERSION"></a>**1455** - **ERROR_CLUSTER_COULD_NOT_READ_CURRENT_VERSION**<br>
  Will be raised when a coordinator in a cluster cannot read the Version entry in the Current hierarchy in the agency.

* <a name="ERROR_CLUSTER_COULD_NOT_CREATE_COLLECTION"></a>**1456** - **ERROR_CLUSTER_COULD_NOT_CREATE_COLLECTION**<br>
  Will be raised when a coordinator in a cluster notices that some DBServers report problems when creating shards for a new collection.

* <a name="ERROR_CLUSTER_TIMEOUT"></a>**1457** - **ERROR_CLUSTER_TIMEOUT**<br>
  Will be raised when a coordinator in a cluster runs into a timeout for some cluster wide operation.

* <a name="ERROR_CLUSTER_COULD_NOT_REMOVE_COLLECTION_IN_PLAN"></a>**1458** - **ERROR_CLUSTER_COULD_NOT_REMOVE_COLLECTION_IN_PLAN**<br>
  Will be raised when a coordinator in a cluster cannot remove an entry for a collection in the Plan hierarchy in the agency.

* <a name="ERROR_CLUSTER_COULD_NOT_REMOVE_COLLECTION_IN_CURRENT"></a>**1459** - **ERROR_CLUSTER_COULD_NOT_REMOVE_COLLECTION_IN_CURRENT**<br>
  Will be raised when a coordinator in a cluster cannot remove an entry for a collection in the Current hierarchy in the agency.

* <a name="ERROR_CLUSTER_COULD_NOT_CREATE_DATABASE_IN_PLAN"></a>**1460** - **ERROR_CLUSTER_COULD_NOT_CREATE_DATABASE_IN_PLAN**<br>
  Will be raised when a coordinator in a cluster cannot create an entry for a new database in the Plan hierarchy in the agency.

* <a name="ERROR_CLUSTER_COULD_NOT_CREATE_DATABASE"></a>**1461** - **ERROR_CLUSTER_COULD_NOT_CREATE_DATABASE**<br>
  Will be raised when a coordinator in a cluster notices that some DBServers report problems when creating databases for a new cluster wide database.

* <a name="ERROR_CLUSTER_COULD_NOT_REMOVE_DATABASE_IN_PLAN"></a>**1462** - **ERROR_CLUSTER_COULD_NOT_REMOVE_DATABASE_IN_PLAN**<br>
  Will be raised when a coordinator in a cluster cannot remove an entry for a database in the Plan hierarchy in the agency.

* <a name="ERROR_CLUSTER_COULD_NOT_REMOVE_DATABASE_IN_CURRENT"></a>**1463** - **ERROR_CLUSTER_COULD_NOT_REMOVE_DATABASE_IN_CURRENT**<br>
  Will be raised when a coordinator in a cluster cannot remove an entry for a database in the Current hierarchy in the agency.

* <a name="ERROR_CLUSTER_SHARD_GONE"></a>**1464** - **ERROR_CLUSTER_SHARD_GONE**<br>
  Will be raised when a coordinator in a cluster cannot determine the shard that is responsible for a given document.

* <a name="ERROR_CLUSTER_CONNECTION_LOST"></a>**1465** - **ERROR_CLUSTER_CONNECTION_LOST**<br>
  Will be raised when a coordinator in a cluster loses an HTTP connection to a DBserver in the cluster whilst transferring data.

* <a name="ERROR_CLUSTER_MUST_NOT_SPECIFY_KEY"></a>**1466** - **ERROR_CLUSTER_MUST_NOT_SPECIFY_KEY**<br>
  Will be raised when a coordinator in a cluster finds that the _key attribute was specified in a sharded collection the uses not only _key as sharding attribute.

* <a name="ERROR_CLUSTER_GOT_CONTRADICTING_ANSWERS"></a>**1467** - **ERROR_CLUSTER_GOT_CONTRADICTING_ANSWERS**<br>
  Will be raised if a coordinator in a cluster gets conflicting results from different shards, which should never happen.

* <a name="ERROR_CLUSTER_NOT_ALL_SHARDING_ATTRIBUTES_GIVEN"></a>**1468** - **ERROR_CLUSTER_NOT_ALL_SHARDING_ATTRIBUTES_GIVEN**<br>
  Will be raised if a coordinator tries to find out which shard is responsible for a partial document, but cannot do this because not all sharding attributes are specified.

* <a name="ERROR_CLUSTER_MUST_NOT_CHANGE_SHARDING_ATTRIBUTES"></a>**1469** - **ERROR_CLUSTER_MUST_NOT_CHANGE_SHARDING_ATTRIBUTES**<br>
  Will be raised if there is an attempt to update the value of a shard attribute.

* <a name="ERROR_CLUSTER_UNSUPPORTED"></a>**1470** - **ERROR_CLUSTER_UNSUPPORTED**<br>
  Will be raised when there is an attempt to carry out an operation that is not supported in the context of a sharded collection.

* <a name="ERROR_CLUSTER_ONLY_ON_COORDINATOR"></a>**1471** - **ERROR_CLUSTER_ONLY_ON_COORDINATOR**<br>
  Will be raised if there is an attempt to run a coordinator-only operation on a different type of node.

* <a name="ERROR_CLUSTER_READING_PLAN_AGENCY"></a>**1472** - **ERROR_CLUSTER_READING_PLAN_AGENCY**<br>
  Will be raised if a coordinator or DBserver cannot read the Plan in the agency.

* <a name="ERROR_CLUSTER_COULD_NOT_TRUNCATE_COLLECTION"></a>**1473** - **ERROR_CLUSTER_COULD_NOT_TRUNCATE_COLLECTION**<br>
  Will be raised if a coordinator cannot truncate all shards of a cluster collection.

* <a name="ERROR_CLUSTER_C8QL_COMMUNICATION"></a>**1474** - **ERROR_CLUSTER_C8QL_COMMUNICATION**<br>
  Will be raised if the internal communication of the cluster for C8QL produces an error.

* <a name="ERROR_C8DB_DOCUMENT_NOT_FOUND_OR_SHARDING_ATTRIBUTES_CHANGED"></a>**1475** - **ERROR_C8DB_DOCUMENT_NOT_FOUND_OR_SHARDING_ATTRIBUTES_CHANGED**<br>
  Will be raised when a document with a given identifier or handle is unknown, or if the sharding attributes have been changed in a REPLACE operation in the cluster.

* <a name="ERROR_CLUSTER_COULD_NOT_DETERMINE_ID"></a>**1476** - **ERROR_CLUSTER_COULD_NOT_DETERMINE_ID**<br>
  Will be raised if a cluster server at startup could not determine its own ID from the local info provided.

* <a name="ERROR_CLUSTER_ONLY_ON_DBSERVER"></a>**1477** - **ERROR_CLUSTER_ONLY_ON_DBSERVER**<br>
  Will be raised if there is an attempt to run a DBserver-only operation on a different type of node.

* <a name="ERROR_CLUSTER_BACKEND_UNAVAILABLE"></a>**1478** - **ERROR_CLUSTER_BACKEND_UNAVAILABLE**<br>
  Will be raised if a required db server can't be reached.

* <a name="ERROR_CLUSTER_UNKNOWN_CALLBACK_ENDPOINT"></a>**1479** - **ERROR_CLUSTER_UNKNOWN_CALLBACK_ENDPOINT**<br>
  An endpoint couldn't be found

* <a name="ERROR_CLUSTER_AGENCY_STRUCTURE_INVALID"></a>**1480** - **ERROR_CLUSTER_AGENCY_STRUCTURE_INVALID**<br>
  The structure in the agency is invalid

* <a name="ERROR_CLUSTER_C8QL_COLLECTION_OUT_OF_SYNC"></a>**1481** - **ERROR_CLUSTER_C8QL_COLLECTION_OUT_OF_SYNC**<br>
  Will be raised if a collection needed during query execution is out of sync. This currently can only happen when using satellite collections

* <a name="ERROR_CLUSTER_COULD_NOT_CREATE_INDEX_IN_PLAN"></a>**1482** - **ERROR_CLUSTER_COULD_NOT_CREATE_INDEX_IN_PLAN**<br>
  Will be raised when a coordinator in a cluster cannot create an entry for a new index in the Plan hierarchy in the agency.

* <a name="ERROR_CLUSTER_COULD_NOT_DROP_INDEX_IN_PLAN"></a>**1483** - **ERROR_CLUSTER_COULD_NOT_DROP_INDEX_IN_PLAN**<br>
  Will be raised when a coordinator in a cluster cannot remove an index from the Plan hierarchy in the agency.

* <a name="ERROR_CLUSTER_CHAIN_OF_DISTRIBUTESHARDSLIKE"></a>**1484** - **ERROR_CLUSTER_CHAIN_OF_DISTRIBUTESHARDSLIKE**<br>
  Will be raised if one tries to create a collection with a distributeShardsLike attribute which points to another collection that also has one.

* <a name="ERROR_CLUSTER_MUST_NOT_DROP_COLL_OTHER_DISTRIBUTESHARDSLIKE"></a>**1485** - **ERROR_CLUSTER_MUST_NOT_DROP_COLL_OTHER_DISTRIBUTESHARDSLIKE**<br>
  Will be raised if one tries to drop a collection to which another collection points with its distributeShardsLike attribute.

* <a name="ERROR_CLUSTER_UNKNOWN_DISTRIBUTESHARDSLIKE"></a>**1486** - **ERROR_CLUSTER_UNKNOWN_DISTRIBUTESHARDSLIKE**<br>
  Will be raised if one tries to create a collection which points to an unknown collection in its distributeShardsLike attribute.

* <a name="ERROR_CLUSTER_INSUFFICIENT_DBSERVERS"></a>**1487** - **ERROR_CLUSTER_INSUFFICIENT_DBSERVERS**<br>
  Will be raised if one tries to create a collection with a replicationFactor greater than the available number of DBServers.

* <a name="ERROR_CLUSTER_COULD_NOT_DROP_FOLLOWER"></a>**1488** - **ERROR_CLUSTER_COULD_NOT_DROP_FOLLOWER**<br>
  Will be raised if a follower that ought to be dropped could not be dropped in the agency (under Current).

* <a name="ERROR_CLUSTER_SHARD_LEADER_REFUSES_REPLICATION"></a>**1489** - **ERROR_CLUSTER_SHARD_LEADER_REFUSES_REPLICATION**<br>
  Will be raised if a replication operation is refused by a shard leader.

* <a name="ERROR_CLUSTER_SHARD_FOLLOWER_REFUSES_OPERATION"></a>**1490** - **ERROR_CLUSTER_SHARD_FOLLOWER_REFUSES_OPERATION**<br>
  Will be raised if a non-replication operation is refused by a shard follower.

* <a name="ERROR_CLUSTER_SHARD_LEADER_RESIGNED"></a>**1491** - **ERROR_CLUSTER_SHARD_LEADER_RESIGNED**<br>
  Will be raised if a non-replication operation is refused by a former shard leader that has found out that it is no longer the leader.

* <a name="ERROR_CLUSTER_AGENCY_COMMUNICATION_FAILED"></a>**1492** - **ERROR_CLUSTER_AGENCY_COMMUNICATION_FAILED**<br>
  Will be raised if after various retries an agency operation could not be performed successfully.

* <a name="ERROR_CLUSTER_DISTRIBUTE_SHARDS_LIKE_REPLICATION_FACTOR"></a>**1493** - **ERROR_CLUSTER_DISTRIBUTE_SHARDS_LIKE_REPLICATION_FACTOR**<br>
  Will be raised if intended replication factor does not match that of the prototype shard given in distributeShardsLike parameter.

* <a name="ERROR_CLUSTER_DISTRIBUTE_SHARDS_LIKE_NUMBER_OF_SHARDS"></a>**1494** - **ERROR_CLUSTER_DISTRIBUTE_SHARDS_LIKE_NUMBER_OF_SHARDS**<br>
  Will be raised if intended number of shards does not match that of the prototype shard given in distributeShardsLike parameter.

* <a name="ERROR_CLUSTER_LEADERSHIP_CHALLENGE_ONGOING"></a>**1495** - **ERROR_CLUSTER_LEADERSHIP_CHALLENGE_ONGOING**<br>
  Will be raised when servers are currently competing for leadership, and the result is still unknown.

* <a name="ERROR_CLUSTER_NOT_LEADER"></a>**1496** - **ERROR_CLUSTER_NOT_LEADER**<br>
  Will be raised when an operation is sent to a non-leading server.

## C8 query errors

* <a name="ERROR_QUERY_KILLED"></a>**1500** - **ERROR_QUERY_KILLED**<br>
  Will be raised when a running query is killed by an explicit admin command.

* <a name="ERROR_QUERY_PARSE"></a>**1501** - **ERROR_QUERY_PARSE**<br>
  Will be raised when query is parsed and is found to be syntactically invalid.

* <a name="ERROR_QUERY_EMPTY"></a>**1502** - **ERROR_QUERY_EMPTY**<br>
  Will be raised when an empty query is specified.

* <a name="ERROR_QUERY_SCRIPT"></a>**1503** - **ERROR_QUERY_SCRIPT**<br>
  Will be raised when a runtime error is caused by the query.

* <a name="ERROR_QUERY_NUMBER_OUT_OF_RANGE"></a>**1504** - **ERROR_QUERY_NUMBER_OUT_OF_RANGE**<br>
  Will be raised when a number is outside the expected range.

* <a name="ERROR_QUERY_VARIABLE_NAME_INVALID"></a>**1510** - **ERROR_QUERY_VARIABLE_NAME_INVALID**<br>
  Will be raised when an invalid variable name is used.

* <a name="ERROR_QUERY_VARIABLE_REDECLARED"></a>**1511** - **ERROR_QUERY_VARIABLE_REDECLARED**<br>
  Will be raised when a variable gets re-assigned in a query.

* <a name="ERROR_QUERY_VARIABLE_NAME_UNKNOWN"></a>**1512** - **ERROR_QUERY_VARIABLE_NAME_UNKNOWN**<br>
  Will be raised when an unknown variable is used or the variable is undefined the context it is used.

* <a name="ERROR_QUERY_COLLECTION_LOCK_FAILED"></a>**1521** - **ERROR_QUERY_COLLECTION_LOCK_FAILED**<br>
  Will be raised when a read lock on the collection cannot be acquired.

* <a name="ERROR_QUERY_TOO_MANY_COLLECTIONS"></a>**1522** - **ERROR_QUERY_TOO_MANY_COLLECTIONS**<br>
  Will be raised when the number of collections in a query is beyond the allowed value.

* <a name="ERROR_QUERY_DOCUMENT_ATTRIBUTE_REDECLARED"></a>**1530** - **ERROR_QUERY_DOCUMENT_ATTRIBUTE_REDECLARED**<br>
  Will be raised when a document attribute is re-assigned.

* <a name="ERROR_QUERY_FUNCTION_NAME_UNKNOWN"></a>**1540** - **ERROR_QUERY_FUNCTION_NAME_UNKNOWN**<br>
  Will be raised when an undefined function is called.

* <a name="ERROR_QUERY_FUNCTION_ARGUMENT_NUMBER_MISMATCH"></a>**1541** - **ERROR_QUERY_FUNCTION_ARGUMENT_NUMBER_MISMATCH**<br>
  Will be raised when the number of arguments used in a function call does not match the expected number of arguments for the function.

* <a name="ERROR_QUERY_FUNCTION_ARGUMENT_TYPE_MISMATCH"></a>**1542** - **ERROR_QUERY_FUNCTION_ARGUMENT_TYPE_MISMATCH**<br>
  Will be raised when the type of an argument used in a function call does not match the expected argument type.

* <a name="ERROR_QUERY_INVALID_REGEX"></a>**1543** - **ERROR_QUERY_INVALID_REGEX**<br>
  Will be raised when an invalid regex argument value is used in a call to a function that expects a regex.

* <a name="ERROR_QUERY_BIND_PARAMETERS_INVALID"></a>**1550** - **ERROR_QUERY_BIND_PARAMETERS_INVALID**<br>
  Will be raised when the structure of bind parameters passed has an unexpected format.

* <a name="ERROR_QUERY_BIND_PARAMETER_MISSING"></a>**1551** - **ERROR_QUERY_BIND_PARAMETER_MISSING**<br>
  Will be raised when a bind parameter was declared in the query but the query is being executed with no value for that parameter.

* <a name="ERROR_QUERY_BIND_PARAMETER_UNDECLARED"></a>**1552** - **ERROR_QUERY_BIND_PARAMETER_UNDECLARED**<br>
  Will be raised when a value gets specified for an undeclared bind parameter.

* <a name="ERROR_QUERY_BIND_PARAMETER_TYPE"></a>**1553** - **ERROR_QUERY_BIND_PARAMETER_TYPE**<br>
  Will be raised when a bind parameter has an invalid value or type.

* <a name="ERROR_QUERY_INVALID_LOGICAL_VALUE"></a>**1560** - **ERROR_QUERY_INVALID_LOGICAL_VALUE**<br>
  Will be raised when a non-boolean value is used in a logical operation.

* <a name="ERROR_QUERY_INVALID_ARITHMETIC_VALUE"></a>**1561** - **ERROR_QUERY_INVALID_ARITHMETIC_VALUE**<br>
  Will be raised when a non-numeric value is used in an arithmetic operation.

* <a name="ERROR_QUERY_DIVISION_BY_ZERO"></a>**1562** - **ERROR_QUERY_DIVISION_BY_ZERO**<br>
  Will be raised when there is an attempt to divide by zero.

* <a name="ERROR_QUERY_ARRAY_EXPECTED"></a>**1563** - **ERROR_QUERY_ARRAY_EXPECTED**<br>
  Will be raised when a non-array operand is used for an operation that expects an array argument operand.

* <a name="ERROR_QUERY_FAIL_CALLED"></a>**1569** - **ERROR_QUERY_FAIL_CALLED**<br>
  Will be raised when the function FAIL() is called from inside a query.

* <a name="ERROR_QUERY_GEO_INDEX_MISSING"></a>**1570** - **ERROR_QUERY_GEO_INDEX_MISSING**<br>
  Will be raised when a geo restriction was specified but no suitable geo index is found to resolve it.

* <a name="ERROR_QUERY_FULLTEXT_INDEX_MISSING"></a>**1571** - **ERROR_QUERY_FULLTEXT_INDEX_MISSING**<br>
  Will be raised when a fulltext query is performed on a collection without a suitable fulltext index.

* <a name="ERROR_QUERY_INVALID_DATE_VALUE"></a>**1572** - **ERROR_QUERY_INVALID_DATE_VALUE**<br>
  Will be raised when a value cannot be converted to a date.

* <a name="ERROR_QUERY_MULTI_MODIFY"></a>**1573** - **ERROR_QUERY_MULTI_MODIFY**<br>
  Will be raised when an C8QL query contains more than one data-modifying operation.

* <a name="ERROR_QUERY_INVALID_AGGREGATE_EXPRESSION"></a>**1574** - **ERROR_QUERY_INVALID_AGGREGATE_EXPRESSION**<br>
  Will be raised when an C8QL query contains an invalid aggregate expression.

* <a name="ERROR_QUERY_COMPILE_TIME_OPTIONS"></a>**1575** - **ERROR_QUERY_COMPILE_TIME_OPTIONS**<br>
  Will be raised when an C8QL data-modification query contains options that cannot be figured out at query compile time.

* <a name="ERROR_QUERY_EXCEPTION_OPTIONS"></a>**1576** - **ERROR_QUERY_EXCEPTION_OPTIONS**<br>
  Will be raised when an C8QL data-modification query contains an invalid options specification.

* <a name="ERROR_QUERY_COLLECTION_USED_IN_EXPRESSION"></a>**1577** - **ERROR_QUERY_COLLECTION_USED_IN_EXPRESSION**<br>
  Will be raised when a collection is used as an operand in an C8QL expression.

* <a name="ERROR_QUERY_DISALLOWED_DYNAMIC_CALL"></a>**1578** - **ERROR_QUERY_DISALLOWED_DYNAMIC_CALL**<br>
  Will be raised when a dynamic function call is made to a function that cannot be called dynamically.

* <a name="ERROR_QUERY_ACCESS_AFTER_MODIFICATION"></a>**1579** - **ERROR_QUERY_ACCESS_AFTER_MODIFICATION**<br>
  Will be raised when collection data are accessed after a data-modification operation.

## C8QL user function errors

* <a name="ERROR_QUERY_FUNCTION_INVALID_NAME"></a>**1580** - **ERROR_QUERY_FUNCTION_INVALID_NAME**<br>
  Will be raised when a user function with an invalid name is registered.

* <a name="ERROR_QUERY_FUNCTION_INVALID_CODE"></a>**1581** - **ERROR_QUERY_FUNCTION_INVALID_CODE**<br>
  Will be raised when a user function is registered with invalid code.

* <a name="ERROR_QUERY_FUNCTION_NOT_FOUND"></a>**1582** - **ERROR_QUERY_FUNCTION_NOT_FOUND**<br>
  Will be raised when a user function is accessed but not found.

* <a name="ERROR_QUERY_FUNCTION_RUNTIME_ERROR"></a>**1583** - **ERROR_QUERY_FUNCTION_RUNTIME_ERROR**<br>
  Will be raised when a user function throws a runtime exception.

## C8QL query registry errors

* <a name="ERROR_QUERY_BAD_JSON_PLAN"></a>**1590** - **ERROR_QUERY_BAD_JSON_PLAN**<br>
  Will be raised when an HTTP API for a query got an invalid JSON object.

* <a name="ERROR_QUERY_NOT_FOUND"></a>**1591** - **ERROR_QUERY_NOT_FOUND**<br>
  Will be raised when an Id of a query is not found by the HTTP API.

* <a name="ERROR_QUERY_IN_USE"></a>**1592** - **ERROR_QUERY_IN_USE**<br>
  Will be raised when an Id of a query is found by the HTTP API but the query is in use.

## C8 cursor errors

* <a name="ERROR_CURSOR_NOT_FOUND"></a>**1600** - **ERROR_CURSOR_NOT_FOUND**<br>
  Will be raised when a cursor is requested via its id but a cursor with that id cannot be found.

* <a name="ERROR_CURSOR_BUSY"></a>**1601** - **ERROR_CURSOR_BUSY**<br>
  Will be raised when a cursor is requested via its id but a concurrent request is still using the cursor.

## User management errors

* <a name="ERROR_USER_INVALID_NAME"></a>**1700** - **ERROR_USER_INVALID_NAME**<br>
  Will be raised when an invalid user name is used.

* <a name="ERROR_USER_INVALID_PASSWORD"></a>**1701** - **ERROR_USER_INVALID_PASSWORD**<br>
  Will be raised when an invalid password is used.

* <a name="ERROR_USER_DUPLICATE"></a>**1702** - **ERROR_USER_DUPLICATE**<br>
  Will be raised when a user name already exists.

* <a name="ERROR_USER_NOT_FOUND"></a>**1703** - **ERROR_USER_NOT_FOUND**<br>
  Will be raised when a user name is updated that does not exist.

* <a name="ERROR_USER_CHANGE_PASSWORD"></a>**1704** - **ERROR_USER_CHANGE_PASSWORD**<br>
  Will be raised when the user must change his password.

* <a name="ERROR_USER_EXTERNAL"></a>**1705** - **ERROR_USER_EXTERNAL**<br>
  Will be raised when the user is authenicated by an external server.

## Task errors

* <a name="ERROR_TASK_INVALID_ID"></a>**1850** - **ERROR_TASK_INVALID_ID**<br>
  Will be raised when a task is created with an invalid id.

* <a name="ERROR_TASK_DUPLICATE_ID"></a>**1851** - **ERROR_TASK_DUPLICATE_ID**<br>
  Will be raised when a task id is created with a duplicate id.

* <a name="ERROR_TASK_NOT_FOUND"></a>**1852** - **ERROR_TASK_NOT_FOUND**<br>
  Will be raised when a task with the specified id could not be found.

## Graph / traversal errors

* <a name="ERROR_GRAPH_INVALID_GRAPH"></a>**1901** - **ERROR_GRAPH_INVALID_GRAPH**<br>
  Will be raised when an invalid name is passed to the server.

* <a name="ERROR_GRAPH_COULD_NOT_CREATE_GRAPH"></a>**1902** - **ERROR_GRAPH_COULD_NOT_CREATE_GRAPH**<br>
  Will be raised when an invalid name, vertices or edges is passed to the server.

* <a name="ERROR_GRAPH_INVALID_VERTEX"></a>**1903** - **ERROR_GRAPH_INVALID_VERTEX**<br>
  Will be raised when an invalid vertex id is passed to the server.

* <a name="ERROR_GRAPH_COULD_NOT_CREATE_VERTEX"></a>**1904** - **ERROR_GRAPH_COULD_NOT_CREATE_VERTEX**<br>
  Will be raised when the vertex could not be created.

* <a name="ERROR_GRAPH_COULD_NOT_CHANGE_VERTEX"></a>**1905** - **ERROR_GRAPH_COULD_NOT_CHANGE_VERTEX**<br>
  Will be raised when the vertex could not be changed.

* <a name="ERROR_GRAPH_INVALID_EDGE"></a>**1906** - **ERROR_GRAPH_INVALID_EDGE**<br>
  Will be raised when an invalid edge id is passed to the server.

* <a name="ERROR_GRAPH_COULD_NOT_CREATE_EDGE"></a>**1907** - **ERROR_GRAPH_COULD_NOT_CREATE_EDGE**<br>
  Will be raised when the edge could not be created.

* <a name="ERROR_GRAPH_COULD_NOT_CHANGE_EDGE"></a>**1908** - **ERROR_GRAPH_COULD_NOT_CHANGE_EDGE**<br>
  Will be raised when the edge could not be changed.

* <a name="ERROR_GRAPH_TOO_MANY_ITERATIONS"></a>**1909** - **ERROR_GRAPH_TOO_MANY_ITERATIONS**<br>
  Will be raised when too many iterations are done in a graph traversal.

* <a name="ERROR_GRAPH_INVALID_FILTER_RESULT"></a>**1910** - **ERROR_GRAPH_INVALID_FILTER_RESULT**<br>
  Will be raised when an invalid filter result is returned in a graph traversal.

* <a name="ERROR_GRAPH_COLLECTION_MULTI_USE"></a>**1920** - **ERROR_GRAPH_COLLECTION_MULTI_USE**<br>
  an edge collection may only be used once in one edge definition of a graph.,

* <a name="ERROR_GRAPH_COLLECTION_USE_IN_MULTI_GRAPHS"></a>**1921** - **ERROR_GRAPH_COLLECTION_USE_IN_MULTI_GRAPHS**<br>
   is already used by another graph in a different edge definition.,

* <a name="ERROR_GRAPH_CREATE_MISSING_NAME"></a>**1922** - **ERROR_GRAPH_CREATE_MISSING_NAME**<br>
  a graph name is required to create a graph.,

* <a name="ERROR_GRAPH_CREATE_MALFORMED_EDGE_DEFINITION"></a>**1923** - **ERROR_GRAPH_CREATE_MALFORMED_EDGE_DEFINITION**<br>
  the edge definition is malformed. It has to be an array of objects.,

* <a name="ERROR_GRAPH_NOT_FOUND"></a>**1924** - **ERROR_GRAPH_NOT_FOUND**<br>
  a graph with this name could not be found.,

* <a name="ERROR_GRAPH_DUPLICATE"></a>**1925** - **ERROR_GRAPH_DUPLICATE**<br>
  a graph with this name already exists.,

* <a name="ERROR_GRAPH_VERTEX_COL_DOES_NOT_EXIST"></a>**1926** - **ERROR_GRAPH_VERTEX_COL_DOES_NOT_EXIST**<br>
  the specified vertex collection does not exist or is not part of the graph.,

* <a name="ERROR_GRAPH_WRONG_COLLECTION_TYPE_VERTEX"></a>**1927** - **ERROR_GRAPH_WRONG_COLLECTION_TYPE_VERTEX**<br>
  the collection is not a vertex collection.,

* <a name="ERROR_GRAPH_NOT_IN_ORPHAN_COLLECTION"></a>**1928** - **ERROR_GRAPH_NOT_IN_ORPHAN_COLLECTION**<br>
  Vertex collection not in orphan collection of the graph.,

* <a name="ERROR_GRAPH_COLLECTION_USED_IN_EDGE_DEF"></a>**1929** - **ERROR_GRAPH_COLLECTION_USED_IN_EDGE_DEF**<br>
  The collection is already used in an edge definition of the graph.,

* <a name="ERROR_GRAPH_EDGE_COLLECTION_NOT_USED"></a>**1930** - **ERROR_GRAPH_EDGE_COLLECTION_NOT_USED**<br>
  The edge collection is not used in any edge definition of the graph.,

* <a name="ERROR_GRAPH_NO_GRAPH_COLLECTION"></a>**1932** - **ERROR_GRAPH_NO_GRAPH_COLLECTION**<br>
  collection _graphs does not exist.,

* <a name="ERROR_GRAPH_INVALID_EXAMPLE_ARRAY_OBJECT_STRING"></a>**1933** - **ERROR_GRAPH_INVALID_EXAMPLE_ARRAY_OBJECT_STRING**<br>
  Invalid example type. Has to be String, Array or Object.,

* <a name="ERROR_GRAPH_INVALID_EXAMPLE_ARRAY_OBJECT"></a>**1934** - **ERROR_GRAPH_INVALID_EXAMPLE_ARRAY_OBJECT**<br>
  Invalid example type. Has to be Array or Object.,

* <a name="ERROR_GRAPH_INVALID_NUMBER_OF_ARGUMENTS"></a>**1935** - **ERROR_GRAPH_INVALID_NUMBER_OF_ARGUMENTS**<br>
  Invalid number of arguments. Expected: ,

* <a name="ERROR_GRAPH_INVALID_PARAMETER"></a>**1936** - **ERROR_GRAPH_INVALID_PARAMETER**<br>
  Invalid parameter type.,

* <a name="ERROR_GRAPH_INVALID_ID"></a>**1937** - **ERROR_GRAPH_INVALID_ID**<br>
  Invalid id,

* <a name="ERROR_GRAPH_COLLECTION_USED_IN_ORPHANS"></a>**1938** - **ERROR_GRAPH_COLLECTION_USED_IN_ORPHANS**<br>
  The collection is already used in the orphans of the graph.,

* <a name="ERROR_GRAPH_EDGE_COL_DOES_NOT_EXIST"></a>**1939** - **ERROR_GRAPH_EDGE_COL_DOES_NOT_EXIST**<br>
  the specified edge collection does not exist or is not part of the graph.,

* <a name="ERROR_GRAPH_EMPTY"></a>**1940** - **ERROR_GRAPH_EMPTY**<br>
  The requested graph has no edge collections.

## Session errors

* <a name="ERROR_SESSION_UNKNOWN"></a>**1950** - **ERROR_SESSION_UNKNOWN**<br>
  Will be raised when an invalid/unknown session id is passed to the server.

* <a name="ERROR_SESSION_EXPIRED"></a>**1951** - **ERROR_SESSION_EXPIRED**<br>
  Will be raised when a session is expired.

## Communicator errors

* <a name="COMMUNICATOR_REQUEST_ABORTED"></a>**2100** - **COMMUNICATOR_REQUEST_ABORTED**<br>
  Request was aborted.

* <a name="COMMUNICATOR_DISABLED"></a>**2101** - **COMMUNICATOR_DISABLED**<br>
  Communication was disabled.


## Cluster repair errors

* <a name="ERROR_CLUSTER_REPAIRS_FAILED"></a>**5000** - **ERROR_CLUSTER_REPAIRS_FAILED**<br>
  General error during cluster repairs

* <a name="ERROR_CLUSTER_REPAIRS_NOT_ENOUGH_HEALTHY"></a>**5001** - **ERROR_CLUSTER_REPAIRS_NOT_ENOUGH_HEALTHY**<br>
  Will be raised when, during repairDistributeShardsLike, there must be a free db server to move a shard, but there is no candidate or none is healthy.

* <a name="ERROR_CLUSTER_REPAIRS_REPLICATION_FACTOR_VIOLATED"></a>**5002** - **ERROR_CLUSTER_REPAIRS_REPLICATION_FACTOR_VIOLATED**<br>
  Will be raised on various inconsistencies regarding the replication factor

* <a name="ERROR_CLUSTER_REPAIRS_NO_DBSERVERS"></a>**5003** - **ERROR_CLUSTER_REPAIRS_NO_DBSERVERS**<br>
  Will be raised if a collection that is fixed has some shard without DB Servers

* <a name="ERROR_CLUSTER_REPAIRS_MISMATCHING_LEADERS"></a>**5004** - **ERROR_CLUSTER_REPAIRS_MISMATCHING_LEADERS**<br>
  Will be raised if a shard in collection and its prototype in the corresponding distributeShardsLike collection have mismatching leaders (when they should already have been fixed)

* <a name="ERROR_CLUSTER_REPAIRS_MISMATCHING_FOLLOWERS"></a>**5005** - **ERROR_CLUSTER_REPAIRS_MISMATCHING_FOLLOWERS**<br>
  Will be raised if a shard in collection and its prototype in the corresponding distributeShardsLike collection don't have the same followers (when they should already have been adjusted)

* <a name="ERROR_CLUSTER_REPAIRS_INCONSISTENT_ATTRIBUTES"></a>**5006** - **ERROR_CLUSTER_REPAIRS_INCONSISTENT_ATTRIBUTES**<br>
  Will be raised if a collection that is fixed does (not) have distributeShardsLike when it is expected, or does (not) have repairingDistributeShardsLike when it is expected

* <a name="ERROR_CLUSTER_REPAIRS_MISMATCHING_SHARDS"></a>**5007** - **ERROR_CLUSTER_REPAIRS_MISMATCHING_SHARDS**<br>
  Will be raised if in a collection and its distributeShardsLike prototype collection some shard and its prototype have an unequal number of DB Servers

* <a name="ERROR_CLUSTER_REPAIRS_JOB_FAILED"></a>**5008** - **ERROR_CLUSTER_REPAIRS_JOB_FAILED**<br>
  Will be raised if a move shard job in the agency failed during cluster repairs

* <a name="ERROR_CLUSTER_REPAIRS_JOB_DISAPPEARED"></a>**5009** - **ERROR_CLUSTER_REPAIRS_JOB_DISAPPEARED**<br>
  Will be raised if a move shard job in the agency cannot be found anymore before it finished

* <a name="ERROR_CLUSTER_REPAIRS_OPERATION_FAILED"></a>**5010** - **ERROR_CLUSTER_REPAIRS_OPERATION_FAILED**<br>
  Will be raised if an agency transaction failed during either sending or executing it.

## Agency errors

* <a name="ERROR_AGENCY_INQUIRY_SYNTAX"></a>**20001** - **ERROR_AGENCY_INQUIRY_SYNTAX**<br>
  Inquiry handles a list of string clientIds: [<clientId>,...].

* <a name="ERROR_AGENCY_INFORM_MUST_BE_OBJECT"></a>**20011** - **ERROR_AGENCY_INFORM_MUST_BE_OBJECT**<br>
  The inform message in the agency must be an object.

* <a name="ERROR_AGENCY_INFORM_MUST_CONTAIN_TERM"></a>**20012** - **ERROR_AGENCY_INFORM_MUST_CONTAIN_TERM**<br>
  The inform message in the agency must contain a uint parameter 'term'.

* <a name="ERROR_AGENCY_INFORM_MUST_CONTAIN_ID"></a>**20013** - **ERROR_AGENCY_INFORM_MUST_CONTAIN_ID**<br>
  The inform message in the agency must contain a string parameter 'id'.

* <a name="ERROR_AGENCY_INFORM_MUST_CONTAIN_ACTIVE"></a>**20014** - **ERROR_AGENCY_INFORM_MUST_CONTAIN_ACTIVE**<br>
  The inform message in the agency must contain an array 'active'.

* <a name="ERROR_AGENCY_INFORM_MUST_CONTAIN_POOL"></a>**20015** - **ERROR_AGENCY_INFORM_MUST_CONTAIN_POOL**<br>
  The inform message in the agency must contain an object 'pool'.

* <a name="ERROR_AGENCY_INFORM_MUST_CONTAIN_MIN_PING"></a>**20016** - **ERROR_AGENCY_INFORM_MUST_CONTAIN_MIN_PING**<br>
  The inform message in the agency must contain an object 'min ping'.

* <a name="ERROR_AGENCY_INFORM_MUST_CONTAIN_MAX_PING"></a>**20017** - **ERROR_AGENCY_INFORM_MUST_CONTAIN_MAX_PING**<br>
  The inform message in the agency must contain an object 'max ping'.

* <a name="ERROR_AGENCY_INFORM_MUST_CONTAIN_TIMEOUT_MULT"></a>**20018** - **ERROR_AGENCY_INFORM_MUST_CONTAIN_TIMEOUT_MULT**<br>
  The inform message in the agency must contain an object 'timeoutMult'.

* <a name="ERROR_AGENCY_INQUIRE_CLIENT_ID_MUST_BE_STRING"></a>**20020** - **ERROR_AGENCY_INQUIRE_CLIENT_ID_MUST_BE_STRING**<br>
  Inquiry by clientId failed

* <a name="ERROR_AGENCY_CANNOT_REBUILD_DBS"></a>**20021** - **ERROR_AGENCY_CANNOT_REBUILD_DBS**<br>
  Will be raised if the readDB or the spearHead cannot be rebuilt from the replicated log.

## Supervision errors

* <a name="ERROR_SUPERVISION_GENERAL_FAILURE"></a>**20501** - **ERROR_SUPERVISION_GENERAL_FAILURE**<br>
  General supervision failure.

## Dispatcher errors

* <a name="ERROR_DISPATCHER_IS_STOPPING"></a>**21001** - **ERROR_DISPATCHER_IS_STOPPING**<br>
  Will be returned if a shutdown is in progress.

* <a name="ERROR_QUEUE_UNKNOWN"></a>**21002** - **ERROR_QUEUE_UNKNOWN**<br>
  Will be returned if a queue with this name does not exist.

* <a name="ERROR_QUEUE_FULL"></a>**21003** - **ERROR_QUEUE_FULL**<br>
  Will be returned if a queue with this name is full.


