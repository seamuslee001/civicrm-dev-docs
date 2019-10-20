# APIv4 Options

There are many API Options accepted by the CiviCRM API. These options allow the developer to add in more parameters to the resulting Query that is run against the database. E.g. Limit, Sort. You can explore these options using the the [API Explorer](/api/index.md#api-explorer) In APIv3 options were set as keys of an array called options within the parameters array. In APIv4 tthey are just passed in as another parameter not in a nested array.


## OrderBy

-   **Action**: get
-   **Type**: string
-   **Default** id ASC

Determine what the order of the records should be. This was either done with `options.sort` or `options.sequential` in APIv3

```php
$results = \Civi\Api4\UFMatch::get()
  ->addOrderBy('uf_id', 'ASC')
  ->execute();
```

```php
$result = civicrm_api4('UFMatch', 'get', [
  'where' => [
    ['uf_id', '=', $user->uid],
  ],
  'orderBy' => [
    'uf_id' => 'asc',
  ],
]);
```

## Limit

-   **Action**: get
-   **Type**: int
-   **Default**: 0

The maximum number of records to return

Example:

```php
	
\Civi\Api4\UFMatch::get()
  ->adWhere('uf_id' ,'=', $user->uid)
  ->setLimit(25)
  ->execute();
```

```php
civicrm_api4('UFMatch', 'get', [
  'where' => [
    ['uf_id', '=', $user->uid],
  ],
  'limit' => 25,
]);
```


## Offset

-   **Action**: get
-   **Type**: int
-   **Default**: 0
-   **Description**:

The numerical offset of the first result record

Example:

```php
\Civi\Api4\UFMatch::get()
  ->addWhere('uf_id', '=', $user->uid)
  ->setLimit(25)
  ->setOffset(15)
  ->execute();
```

```php
civicrm_api4('UFMatch', 'get', [
  'where' => [
    ['uf_id', '=', $user->uid],
  ],
  'limit' => 25,
  'offset' => 50,
]);
```
