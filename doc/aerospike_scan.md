
# Aerospike::scan

Aerospike::scan - scans a set in the Aerospike database

## Description

```
public int Aerospike::scan ( string $ns, string $set, callback $record_cb [, array $select [, array $options ]] )
```

**Aerospike::scan()** will scan a *set* and invoke a callback function 
*record_cb* on each record in the result stream.
A selection of bins returned can be determined by passing an array in *select*,
otherwise all bins in the record are returned.
.
Non-existent bins will appear in the *record* with a NULL value.

## Parameters

**ns** the namespace

**set** the set to be scanned

**record_cb** a callback function invoked for each [record](aerospike_get.md#parameters) streaming back from the server.

**select** an array of bin names which are the subset to be returned.

**[options](aerospike.md)** including
- **Aerospike::OPT_READ_TIMEOUT**
- **Aerospike::OPT_SCAN_PRIORITY**
- **Aerospike::OPT_SCAN_PERCENTAGE** of the records in the set to return
- **Aerospike::OPT_SCAN_CONCURRENTLY** whether to run the scan in parallel
- **Aerospike::OPT_SCAN_NOBINS** whether to not retrieve bins for the records

## Return Values

Returns an integer status code.  Compare to the Aerospike class status
constants.  When non-zero the **Aerospike::error()** and
**Aerospike::errorno()** methods can be used.

## Examples

### Get 20 records from a scan

```php
<?php

$config = array("hosts"=>array(array("addr"=>"localhost", "port"=>3000)));
$db = new Aerospike($config);
if (!$db->isConnected()) {
   echo "Aerospike failed to connect[{$db->errorno()}]: {$db->error()}\n";
   exit(1);
}

$options = array(Aerospike::OPT_SCAN_PRIORITY => Aerospike::SCAN_PRIORITY_MEDIUM);
$processed = 0;
$status = $db->scan("test", "users", function ($record) use (&$processed) {
    if (!is_null($record['bins']['email'])) echo $record['bins']['email']."\n";
    if ($processed++ > 19) return false; // halt the stream by returning a false
}, array("email"), $options);
// check the status of the last operation
if ($status == Aerospike::ERR_SCAN_ABORTED) {
    echo "I think a sample of $processed records is enough\n";
} else if ($status !== Aerospike::OK) {
    echo "An error occured while scanning[{$db->errorno()}] {$db->error()}\n";
}

?>
```

We expect to see:

```
foo@example.com
:
bar@example.com
I think a sample of 20 records is enough
```

### Buffer all the records returned from the scan

```php
<?php

$result = array();
$status = $db->scan("test", "users", function ($record) use (&$result) {
    $result[] = $record['bins'];
});
if ($status !== Aerospike::OK) {
    echo "An error occured while scanning[{$db->errorno()}] {$db->error()}\n";
} else {
    echo "The scan returned ".count($result)." records\n";
}

?>
```

## See Also

- [Aerospike Data Model](http://www.aerospike.com/docs/architecture/data-model.html)
- [Scans](http://www.aerospike.com/docs/guide/scan.html)
- [Managing Scans](http://www.aerospike.com/docs/operations/manage/scans/)
