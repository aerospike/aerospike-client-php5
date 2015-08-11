
# Aerospike::getMany

Aerospike::getMany - gets a batch of record from the Aerospike database

## Description

```
public int Aerospike::getMany ( array $keys, array &$records [, array $filter [, array $options]] )
```

**Aerospike::getMany()** will read a batch of *records* from a list of given *keys*
Each of the *records* is filled with an array of bins and values.
Non-existent bins will appear in the *record* with a NULL value. Non-existent
records will return as NULL.
The bins returned can be filtered by passing an array of the bins needed.

## Parameters

**keys** an array of initialized keys, each an array with keys ['ns','set','key'] or ['ns','set','digest'].

**record** filled by an array of bins and values.

**filter** an array of bin names

**[options](aerospike.md)** including
- **Aerospike::OPT_READ_TIMEOUT**

## Return Values

Returns an integer status code.  Compare to the Aerospike class status
constants.  When non-zero the **Aerospike::error()** and
**Aerospike::errorno()** methods can be used.

## Examples

### Example #1 Aerospike::getMany() default behavior example

```php
<?php

$config = array("hosts"=>array(array("addr"=>"localhost", "port"=>3000)));
$db = new Aerospike($config);
if (!$db->isConnected()) {
   echo "Aerospike failed to connect[{$db->errorno()}]: {$db->error()}\n";
   exit(1);
}

$key1 = $db->initKey("test", "users", 1234);
$key2 = $db->initKey("test", "users", 1235); // this key does not exist
$key3 = $db->initKey("test", "users", 1236);
$keys = array($key1, $key2, $key3);
$status = $db->getMany($keys, $records);
if ($status == Aerospike::OK) {
    var_dump($records);
} else {
    echo "[{$db->errorno()}] ".$db->error();
}

?>
```

We expect to see:

```
array(3) {
  [1234]=>
  array(3) {
    ["email"]=>
    string(15) "hey@example.com"
    ["name"]=>
    string(9) "You There"
    ["age"]=>
    int(33)
  }
  [12345]=>
  NULL
  [1236]=>
  array(3) {
    ["email"]=>
    string(19) "thisguy@example.com"
    ["name"]=>
    string(8) "This Guy"
    ["age"]=>
    int(42)
  }
}
```

### Example #2 getMany the record with filtered bins

```php
<?php

// assuming this follows Example #1

// Getting a filtered record
$filter = array("email", "manager");
unset($record);
$status = $db->getMany($keys, $records, $filter);
if ($status == Aerospike::OK) {
    var_dump($record);
} else {
    echo "[{$db->errorno()}] ".$db->error();
}

?>
```

We expect to see:

```
array(3) {
  [1234]=>
  array(3) {
    ["email"]=>
    string(15) "hey@example.com"
    ["manager"]=>
    NULL
  }
  [12345]=>
  NULL
  [1236]=>
  array(3) {
    ["email"]=>
    string(19) "thisguy@example.com"
    ["manager"]=>
    string(8) "The Boss"
  }
}
```

