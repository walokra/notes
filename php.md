# PHP stuff

## Monolog

<https://stackoverflow.com/questions/24450180/monolog-how-to-log-php-array-into-console>

```php
$array = var_dump($groups);
$this->container->logger->addWarning("# groups: " . print_r($groups, true));
$this->container->logger->addWarning("Foo Bar");
$this->container->logger->addWarning("" . print_r($currentUser, true));
$this->container->logger->addWarning("user", $user);
 
```
## Log SQL Queries 

With Eloquent and Monolog:

```php
// Listen for Query Events for Debug
$this->container->db->getConnection()->setEventDispatcher(new \Illuminate\Events\Dispatcher);
$this->container->db->getConnection()->listen(function ($query) {

    // Format binding data for sql insertion
    foreach ($query->bindings as $i => $binding) {
        if ($binding instanceof \DateTime) {
            $query->bindings[$i] = $binding->format('\'Y-m-d H:i:s\'');
        } else {
            if (is_string($binding)) {
                $query->bindings[$i] = "'$binding'";
            }
        }
    }

    // Insert bindings into query
    $boundSql = str_replace(['%', '?'], ['%%', '%s'], $query->sql);
    $boundSql = vsprintf($boundSql, $query->bindings);
    // UNBOUND QUERY = " . $query->sql

    $this->container->logger->debug("### SQL: time=" . $query->time . "ms; " . $boundSql . "###");
});
```

## Strip special characters

```php
$imageUrl = $params['image_url'];
// Minio doesn't accept filenames with special characters
$imageUrl = str_replace(' ', '-', $imageUrl); // Replaces all spaces with hyphens.
$imageUrl = preg_replace('/[^A-Za-z0-9\-:]/', '', $imageUrl); // Removes special chars.
$imageUrl = preg_replace('/-+/', '-', $imageUrl); // Replaces multiple hyphens with single one.
```
