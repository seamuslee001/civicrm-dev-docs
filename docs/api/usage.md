# API Usage

Every API call consists of three elements: the *entity*, *action*, and
*parameters*.  For example, consider a few commonly-used entities along
with the supported actions and parameters:


| Entity                   | Description              | Actions |  Parameters       |
|--------------------------|--------------------------|---------|-------------------|
| <code>Contact</code>     | An individual, <br /> organization, or <br />household.         |<code>create</code><br/><code>get</code><br/><code>delete</code><br/>| <code>contact\_type</code><br /> <code>nick\_name</code>  <br /><code>preferred\_language</code>       |
| <code>Activity</code>    | A phone call, meeting,<br /> or email message. that <br /> has occurred (or will <br /> occur) at a specific <br /> date and time|<code>create</code><br/><code>get</code><br/><code>delete</code><br/>| <code>activity\_type\_id</code> <br /> <code>source\_contact\_id</code> <br /> <code>assignee\_contact\_id</code>    |
| <code>Address</code>     | A street-address related <br /> to a contact. |<code>create</code><br/><code>get</code><br/><code>delete</code><br/>| <code>contact\_id</code>,  <br /> <code>street\_address</code> <br /> <code>city</code>  <br /> <code>state\_province\_id</code> <br /> <code>country\_id</code>     |

(*For full, up-to-date details about specific entities and parameters, use the
[API Explorer](/api/index.md#api-explorer).*)

The API is available in many different environments (such as PHP, REST, and
Javascript), and the notation differs slightly in each environment.
However, if you understand the canonical notation, then other environments
will appear as small adaptations.

Canonically, an API call is processed by the API kernel.  The `$entity`,
`$action`, and `$params` are passed as inputs, and an associative-array is
returned as output.

```php
$result = Civi::service('civi_api_kernel')->run('Contact', 'get', array(
  'version' => 3,
  'first_name' => 'Alice',
  'last_name' => 'Roberts'
));
```

The result of a successful API call typically looks like this:

```php
array(
  'is_error' => 0,
  'version' => 3,
  'count' => /* number of records */,
  'values' => /* array of records */,
)
```

The result of a failed API call typically looks like this:

```php
array(
  'is_error' => 1,
  'error_message' => /* a descriptive error message */,
)
```

!!! note
    A few specialized actions like `getsingle` or `getvalue` may return success in a different format.


## PHP (civicrm_api3)

This is the most common way to call the API.

```php
try {
  $contacts = civicrm_api3('Contact', 'get', array(
    'first_name' => 'Alice',
    'last_name' => 'Roberts',
  ));
}
catch (CiviCRM_API3_Exception $e) {
  $error = $e->getMessage();
}
printf("Found %d item(s)\n", $contacts['count']);
```

This format matches canonical format almost exactly, with a few improvements
for usability:

-   The function `civicrm_api3()` is easier to remember.
-   The `version => 3` parameter is not required.
-   Errors are reported as PHP exceptions. You may catch the exceptions or
    (by default) allow them to bubble up.

*Note*: If you're writing a Drupal module, a Joomla extension, a WordPress
plugin, or a standalone script, then you may need to **bootstrap** CiviCRM
before using the API.  See the examples in [Bootstrap Reference].

[Bootstrap Reference](/framework/bootstrap.md)

## PHP (class.api.php)

CiviCRM v3.4 introduced an object-oriented API client, `class.api.php`.
This class can be used locally or remotely to invoke APIs, as in:

```php
require_once 'your/civicrm/folder/api/class.api.php';
$api = new civicrm_api3(array(
  // Specify location of "civicrm.settings.php".
  'conf_path' => 'your/sites/default',
));
$apiParams = array(
  'first_name' => 'Alice',
  'last_name' => 'Roberts',
);
if ($api->Contact->Get($apiParams)) {
  //each key of the result array is an attribute of the api
  echo "\n contacts found ".$api->count;
}
else {
  echo $api->errorMsg();
}
```

If you call the API in the object oriented fashion, you do not have to
specify 'version' as a parameter.

The object-oriented client can connect to a local or remote CiviCRM
instance. For details about connection parameters, see the docblock in
[class.api.php](https://github.com/civicrm/civicrm-core/blob/master/api/class.api.php).

## PHP API version 4

In API version 4 there are 2 distinct formats for querying the API, firstly an Object Oriented format

```php
$aCLs = \Civi\Api4\ACL::get()
  ->setSelect([
    'id',
  ])
  ->addWhere('name', '=', 'hello')
  ->setLimit(25)
  ->execute();
foreach ($aCLs as $aCL) {
  // do something
}
```

There is a traditional format which also is supported by the API version 4

```php
$aCLs = civicrm_api4('ACL', 'get', [
  'select' => [
    'id',
  ],
  'where' => [
    ['name', '=', 'hello'],
  ],
  'limit' => 25,
]);
```

Both formats will return an [arrayObject](http://php.net/manual/en/class.arrayobject.php). This means that you can loop through the results, and use functions like first() and the like to access reults.

```php
$result = \Civi\Api4\Contact::get()->execute();

// you can loop through the results directly
foreach ($result as $contact) {}

// you can just grab the first one
$contact1 = $result->first();

// reindex results on-the-fly (replacement for sequential=1 in v3)
$result->indexBy('id');

// or fetch some metadata about the call
$entity = $result->entity; // "Contact"
```

## REST

For external services:

V3

```text
http://www.example.com/sites/all/modules/civicrm/extern/rest.php
  ?api_key=t0ps3cr3t
  &key=an07h3rs3cr3t
  &json=1
  &debug=1
  &version=3
  &entity=Contact
  &action=get
  &first_name=Alice
  &last_name=Roberts
```

V4
```text
http://www.example.com/sites/all/modules/civicrm/extern/rest.php
  ?api_key=t0ps3cr3t
  &key=an07h3rs3cr3t
  &json=1
  &debug=1
  &version=4
  &entity=Contact
  &action=get
  &first_name=Alice
  &last_name=Roberts
```

For sessions already authenticated by the CMS (e.g. AJAX)

V3
```text
http://www.example.com/civicrm/ajax/rest
  ?json=1
  &debug=1
  &version=3
  &entity=Contact
  &action=get
  &first_name=Alice
  &last_name=Roberts
```

V4
```text
http://www.example.com/civicrm/ajax/rest
  ?json=1
  &debug=1
  &version=3
  &entity=Contact
  &action=get
  &first_name=Alice
  &last_name=Roberts
```

Obviously you should substitute your site in! You can explore the syntax
and options available using the [API Explorer](/api/index.md#api-explorer).

Please note that the REST interface is subject to [API Security](/security/permissions.md#api-permissions).

For more details, see [REST interface](/api/interfaces.md#rest). 


## AJAX

V3
```javascript
CRM.api3('entity', 'action', [params], [statusMessage]);
```

V4
```javascript
CRM.api4('Contact', 'get', params).then(function(result) {
  // you can loop through the results
  result.forEach(function(contact, n) {});

  // you can just grab the first one
  var contact1 = result[0];

  // or fetch some metadata about the call
  var entity = result.entity; // "Contact"
});
```

For more details, see [AJAX Interface](/api/interfaces.md#ajax).

The AJAX interface is automatically available for web-pages generated through
CiviCRM (such as standard CiviCRM web-pages, CiviCRM extensions,
and custom CiviCRM templates).

The AJAX interface could be made available to other parts of the same website
(e.g. a drupal module or wordpress widget) by calling
`CRM_Core_Resources::singleton()->addCoreResources()`
from php. Please note that the AJAX interface is subject to
[API Security](/security/permissions.md#api-permissions)
and
[Same Origin Policy](http://en.wikipedia.org/wiki/Same_origin_policy).
To use it from an external site or application, see REST interface documentation.

## Smarty

```smarty
{crmAPI var="myContactList" entity="Contact" action="get" version="3" first_name="Alice" last_name="Roberts" }
Found {$myContactList.count} item(s).
```

The smarty call is to add extra information, therefore *create* or *delete*
actions don't make sense in this case.

For more details, see
[Smarty API interface](/api/interfaces.md#smarty-api-interface).

## Scheduled jobs
Any API call can be configured to be run as a scheduled job. These can be configured in the UI under Administer->System Settings->Scheduled jobs. Usually API calls run this way are written with the intent that they be run as scheduled jobs - e.g those with the Job entity or provided by payment processors to run recurring payments.



## Command line

### drush

To run on the default Drupal site:

```bash
drush civicrm-api contact.get first_name=Alice last_name=Roberts
```

To run on Drupal multisite, specify the site name:

```bash
drush -l www.example.com civicrm-api contact.get first_name=Alice last_name=Roberts
```

### wp-cli

```bash
wp civicrm-api contact.get first_name=Alice last_name=Roberts
```

### cv

```bash
cv api contact.get first_name=Alice last_name=Roberts
```

## API Security

API has security measures built in depending on the way the API is called that can also be turned off or on. API Permissions are also able to be altered via hook. More information on API Security can be found in the [Security Documentation](/security/permissions.md).

## API Lookups by Username

You can use a CMS username in lieu of a contact ID by prefacing it with `@user:`.  For instance, if the user "james" has a CiviCRM contact ID of 123, these statements are identical:

```bash
cv api contact.get id=123
cv api contact.get id=@user:james
```
