
# Aerospike::__construct

Aerospike::__construct - constructs a new Aerospike object

## Description

```
public Aerospike::__construct ( array $config [, boolean $persistent_connection = true [, array $options]] )
```

**Aerospike::__construct()** constructs an Aerospike object and connects to the
cluster defined in *config*.  The **Aerospike::isConnected()** method can be used
to test whether the connection succeeded. If a config or connection error
occured the **Aerospike::error()** and **Aerospike::errorno()** methods can be used
to inspect it.

The Aerospike class instance should use persistent connections.  This allows for
reduced overhead on initializing the cluster and keeping track of the state of
its nodes.  Subsequent instantiation calls will attempt to reuse the connection.

## Parameters

**config** an associative array holding the cluster connection information. One
node or more (for failover) may be defined. Once a connection is established to
a node of the Aerospike DB the client will retrieve the full list of nodes in the
cluster and manage its connections to them.

- *hosts* an array of host data
  - *addr* hostname or IP of the node
  - *port*
- *user*
- *pass*

**persistent_connection** whether the C-client will persist between requests.

**[options](aerospike.md)** including
- **Aerospike::OPT_CONNECT_TIMEOUT**
- **Aerospike::OPT_READ_TIMEOUT**
- **Aerospike::OPT_WRITE_TIMEOUT**
- **[Aerospike::OPT_SERIALIZER](aerospike.md)**
- **[Aerospike::OPT_POLICY_KEY](http://www.aerospike.com/apidocs/c/db/d65/group__client__policies.html#gaa9c8a79b2ab9d3812876c3ec5d1d50ec)**
- **[Aerospike::OPT_POLICY_EXISTS](http://www.aerospike.com/apidocs/c/db/d65/group__client__policies.html#ga50b94613bcf416c9c2691c9831b89238)**
- **[Aerospike::OPT_POLICY_RETRY](http://www.aerospike.com/apidocs/c/db/d65/group__client__policies.html#gaa9730980a8b0eda8ab936a48009a6718)**
- **[Aerospike::OPT_POLICY_COMMIT_LEVEL](http://www.aerospike.com/apidocs/c/db/d65/group__client__policies.html#ga17faf52aeb845998e14ba0f3745e8f23)**
- **[Aerospike::OPT_POLICY_CONSISTENCY](http://www.aerospike.com/apidocs/c/db/d65/group__client__policies.html#ga34dbe8d01c941be845145af643f9b5ab)**
- **[Aerospike::OPT_POLICY_REPLICA](http://www.aerospike.com/apidocs/c/db/d65/group__client__policies.html#gabce1fb468ee9cbfe54b7ab834cec79ab)**

## See Also

- [Aerospike::isConnected()](aerospike_isconnected.md)

The following only apply to instances created with non-persistent connections:

- [Aerospike::close()](aerospike_close.md)
- [Aerospike::reconnect()](aerospike_reconnect.md)

## Examples

```php
<?php

$config = array("hosts"=>array(array("addr"=>"localhost", "port"=>3000)));
$opts = array(Aerospike::OPT_CONNECT_TIMEOUT => 1250, Aerospike::OPT_WRITE_TIMEOUT => 1500);
$db = new Aerospike($config, true, $opts);
if (!$db->isConnected()) {
   echo "Aerospike failed to connect[{$db->errorno()}]: {$db->error()}\n";
   exit(1);
}

?>
```

On error we expect to see:

```
Aerospike failed to connect[-1]: Unable to connect to server
```

