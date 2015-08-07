
# The Aerospike class
The Aerospike PHP client API may be described as follows:

## Introduction

The main Aerospike class

```php

final class Aerospike
{
    // The key policy can be determined by setting OPT_POLICY_KEY to one of
    const POLICY_KEY_DIGEST; // hashes (ns,set,key) data into a unique record ID (default)
    const POLICY_KEY_SEND;   // also send, store, and get the actual (ns,set,key) with each record

    // The generation policy can be set using OPT_POLICY_GEN to one of
    const POLICY_GEN_IGNORE; // write a record, regardless of generation
    const POLICY_GEN_EQ;     // write a record, ONLY if given value is equal to the current record generation
    const POLICY_GEN_GT;     // write a record, ONLY if given value is greater-than the current record generation

    // The retry policy can be determined by setting OPT_POLICY_RETRY to one of
    const POLICY_RETRY_NONE; // do not retry an operation (default)
    const POLICY_RETRY_ONCE; // allow for a single retry on an operation

    // By default writes will try to create or replace records and bins
    // behaving similar to an array in PHP. Setting
    // OPT_POLICY_EXISTS with one of these values will overwrite this.
    // POLICY_EXISTS_IGNORE (aka CREATE_OR_UPDATE) is the default value
    const POLICY_EXISTS_IGNORE;            // interleave bins of a record if it exists
    const POLICY_EXISTS_CREATE;            // create a record ONLY if it DOES NOT exist
    const POLICY_EXISTS_UPDATE;            // update a record ONLY if it exists
    const POLICY_EXISTS_REPLACE;           // replace a record ONLY if it exists
    const POLICY_EXISTS_CREATE_OR_REPLACE; // overwrite the bins if record exists

    // Replica and consistency guarantee options
    // See: http://www.aerospike.com/docs/client/c/usage/consistency.html
    const POLICY_REPLICA_MASTER;      // read from the partition master replica node (default)
    const POLICY_REPLICA_ANY;         // read from either the master or prole node
    const POLICY_CONSISTENCY_ONE;     // involve a single replica in the read operation (default)
    const POLICY_CONSISTENCY_ALL;     // involve all replicas in the read operation
    const POLICY_COMMIT_LEVEL_ALL;    // return success after committing all replicas (default)
    const POLICY_COMMIT_LEVEL_MASTER; // return success after committing the master replica

    // Determines a handler for writing values of unsupported type into bins
    // Set OPT_SERIALIZER to one of the following:
    const SERIALIZER_NONE;
    const SERIALIZER_PHP; // default handler
    const SERIALIZER_JSON;
    const SERIALIZER_USER;

    // OPT_SCAN_PRIORITY can be set to one of the following:
    const SCAN_PRIORITY_AUTO;   //The cluster will auto adjust the scan priority
    const SCAN_PRIORITY_LOW;    //Low priority scan.
    const SCAN_PRIORITY_MEDIUM; //Medium priority scan.
    const SCAN_PRIORITY_HIGH;   //High priority scan.

    // Options can be assigned values that modify default behavior
    const OPT_CONNECT_TIMEOUT;    // value in milliseconds, default: 1000
    const OPT_READ_TIMEOUT;       // value in milliseconds, default: 1000
    const OPT_WRITE_TIMEOUT;      // value in milliseconds, default: 1000
    const OPT_POLICY_RETRY;       // set to a Aerospike::POLICY_RETRY_* value
    const OPT_POLICY_EXISTS;      // set to a Aerospike::POLICY_EXISTS_* value
    const OPT_SERIALIZER;         // set the unsupported type handler
    const OPT_SCAN_PRIORITY;      // set to a Aerospike::SCAN_PRIORITY_* value
    const OPT_SCAN_PERCENTAGE;    // integer value 1-100, default: 100
    const OPT_SCAN_CONCURRENTLY;  // boolean value, default: false
    const OPT_SCAN_NOBINS;        // boolean value, default: false
    const OPT_POLICY_KEY;         // records store the digest unique ID, optionally also its (ns,set,key) inputs
    const OPT_POLICY_GEN;         // set to array( Aerospike::POLICY_GEN_* [, $gen_value ] )
    const OPT_POLICY_REPLICA;     // set to one of Aerospike::POLICY_REPLICA_*
    const OPT_POLICY_CONSISTENCY; // set to one of Aerospike::POLICY_CONSISTENCY_*
    const OPT_POLICY_COMMIT_LEVEL;// set to one of Aerospike::POLICY_COMMIT_LEVEL_*

    // Aerospike Status Codes:
    //
    // Each Aerospike API method invocation returns a status code
    //  depending upon the success or failure condition of the call.
    //
    // The error status codes map to the C client
    //  src/include/aerospike/as_status.h

    // Client status codes:
    //
    const ERR_PARAM              ; // Invalid client parameter
    const ERR_CLIENT             ; // Generic client error

    // Server status codes:
    //
    const OK                     ; // Success status
    const ERR_SERVER             ; // Generic server error
    const ERR_SERVER_FULL        ; // Node running out of memory/storage
    const ERR_DEVICE_OVERLOAD    ; // Node storage lagging write load
    const ERR_TIMEOUT            ; // Client or server side timeout error
    const ERR_CLUSTER            ; // Generic cluster discovery and connection error
    const ERR_CLUSTER_CHANGE     ; // Cluster state changed during the request
    const ERR_REQUEST_INVALID    ; // Invalid request protocol or protocol field
    const ERR_UNSUPPORTED_FEATURE;
    const ERR_NO_XDR             ; // XDR not available for the cluster
    // Record specific:
    const ERR_NAMESPACE_NOT_FOUND;
    const ERR_RECORD_NOT_FOUND   ;
    const ERR_RECORD_EXISTS      ; // Record already exists
    const ERR_RECORD_GENERATION  ; // Write policy regarding generation violated
    const ERR_RECORD_TOO_BIG     ; // Record written cannot fit in storage write block
    const ERR_RECORD_BUSY        ; // Hot key: too many concurrent requests for the record
    const ERR_RECORD_KEY_MISMATCH; // Digest incompatibility?
    // Bin specific:
    const ERR_BIN_NAME           ; // Name too long or exceeds the unique name quota for the namespace
    const ERR_BIN_NOT_FOUND      ;
    const ERR_BIN_EXISTS         ; // Bin already exists
    const ERR_BIN_INCOMPATIBLE_TYPE;
    // Query and Scan operations:
    const ERR_SCAN_ABORTED       ; // Scan aborted by the user
    const ERR_QUERY              ; // Generic query error
    const ERR_QUERY_END          ; // Out of records to query
    const ERR_QUERY_ABORTED      ; // Query aborted by the user
    const ERR_QUERY_QUEUE_FULL   ;
    // Index operations:
    const ERR_INDEX              ; // Generic secondary index error
    const ERR_INDEX_OOM          ; // Index out of memory
    const ERR_INDEX_NOT_FOUND    ;
    const ERR_INDEX_FOUND        ;
    const ERR_INDEX_NOT_READABLE ;
    const ERR_INDEX_NAME_MAXLEN  ;
    const ERR_INDEX_MAXCOUNT     ; // Max number of indexes reached
    // UDF operations:
    const ERR_UDF                ; // Generic UDF error
    const ERR_UDF_NOT_FOUND      ; // UDF does not exist
    const ERR_LUA_FILE_NOT_FOUND ; // Source file for the module not found

    // Status values returned by scanInfo()
    const SCAN_STATUS_UNDEF;      // Scan status is undefined.
    const SCAN_STATUS_INPROGRESS; // Scan is currently running.
    const SCAN_STATUS_ABORTED;    // Scan was aborted due to failure or the user.
    const SCAN_STATUS_COMPLETED;  // Scan completed successfully.

    // Logger
    const LOG_LEVEL_OFF  ;
    const LOG_LEVEL_ERROR;
    const LOG_LEVEL_WARN ;
    const LOG_LEVEL_INFO ;
    const LOG_LEVEL_DEBUG;
    const LOG_LEVEL_TRACE;

    // Query Predicate Operators
    const string OP_EQ = '=';
    const string OP_BETWEEN = 'BETWEEN';
    const string OP_CONTAINS = 'CONTAINS';
    const string OP_RANGE = 'RANGE';

    // Multi-operation operators map to the C client
    //  src/include/aerospike/as_operations.h
    const OPERATOR_WRITE;
    const OPERATOR_READ;
    const OPERATOR_INCR;
    const OPERATOR_PREPEND;
    const OPERATOR_APPEND;
    const OPERATOR_TOUCH;

    // UDF types
    const UDF_TYPE_LUA;

    // index types
    const INDEX_TYPE_DEFAULT;   // index records where the bin contains an atomic (string, integer) type
    const INDEX_TYPE_LIST;      // index records where the bin contains a list
    const INDEX_TYPE_MAPKEYS;   // index the keys of records whose specified bin is a map
    const INDEX_TYPE_MAPVALUES; // index the values of records whose specified bin is a map
    // data type
    const INDEX_STRING;  // if the index type is matched, regard values of type string
    const INDEX_NUMERIC; // if the index type is matched, regard values of type integer

    // lifecycle and connection methods
    public __construct ( array $config [,  boolean $persistent_connection = true [, array $options]] )
    public __destruct ( void )
    public boolean isConnected ( void )
    public close ( void )
    public reconnect ( void )

    // error handling methods
    public string error ( void )
    public int errorno ( void )
    public setLogLevel ( int $log_level )
    public setLogHandler ( callback $log_handler )

    // key-value methods
    public array initKey ( string $ns, string $set, int|string $pk [, boolean $is_digest = false ] )
    public string getKeyDigest ( string $ns, string $set, int|string $pk )
    public int put ( array $key, array $bins [, int $ttl = 0 [, array $options ]] )
    public int get ( array $key, array &$record [, array $filter [, array $options ]] )
    public int exists ( array $key, array &$metadata [, array $options ] )
    public int touch ( array $key, int $ttl = 0 [, array $options ] )
    public int remove ( array $key [, array $options ] )
    public int removeBin ( array $key, array $bins [, array $options ] )
    public int increment ( array $key, string $bin, int $offset [, array $options ] )
    public int append ( array $key, string $bin, string $value [, array $options ] )
    public int prepend ( array $key, string $bin, string $value [, array $options ] )
    public int operate ( array $key, array $operations [, array &$returned ] )

    // unsupported type handler methods
    public static setSerializer ( callback $serialize_cb )
    public static setDeserializer ( callback $unserialize_cb )

    // batch operation methods
    public int getMany ( array $keys, array &$records [, array $filter [, array $options]] )
    public int existsMany ( array $keys, array &$metadata [, array $options ] )

    // UDF methods
    public int register ( string $path, string $module [, int $language = Aerospike::UDF_TYPE_LUA] )
    public int deregister ( string $module )
    public int listRegistered ( array &$modules [, int $language ] )
    public int getRegistered ( string $module, string &$code )
    public int apply ( array $key, string $module, string $function[, array $args [, mixed &$returned [, array $options ]]] )
    public int aggregate ( string $ns, string $set, array $where, string $module, string $function, array $args, mixed &$returned [, array $options ] )
    public int scanApply ( string $ns, string $set, string $module, string $function, array $args, int &$scan_id [, array $options ] )
    public int scanInfo ( integer $scan_id, array &$info [, array $options ] )

    // query and scan methods
    public int query ( string $ns, string $set, array $where, callback $record_cb [, array $select [, array $options ]] )
    public int scan ( string $ns, string $set, callback $record_cb [, array $select [, array $options ]] )
    public array predicateEquals ( string $bin, int|string $val )
    public array predicateBetween ( string $bin, int $min, int $max )
    public array predicateContains ( string $bin, int $index_type, int|string $val )
    public array predicateRange ( string $bin, int $index_type, int $min, int $max )

    // admin methods
    public int addIndex ( string $ns, string $set, string $bin, string $name, int $index_type, int $data_type [, array $options ] )
    public int dropIndex ( string $ns, string $name [, array $options ] )

    // info methods
    public int info ( string $request, string &$response [, array $host [, array options ] ] )
    public array infoMany ( string $request [, array $config [, array options ]] )
    public array getNodes ( void )
}
```

### [Runtime Configuration](aerospike_config.md)
### [Session Handler](aerospike_sessions.md)
### [Lifecycle and Connection Methods](apiref_connection.md)
### [Error Handling and Logging Methods](apiref_error.md)
### [Key-Value Methods](apiref_kv.md)
### [Query and Scan Methods](apiref_streams.md)
### [User Defined Methods](apiref_udf.md)
### [Admin Methods](apiref_admin.md)
### [Info Methods](apiref_info.md)
### [Large Data Type Methods](aerospike_ldt.md)

An overview of the development of the client is at the top level
[README](README.md).
