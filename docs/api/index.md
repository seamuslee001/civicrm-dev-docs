# The CiviCRM API

CiviCRM has a stable comprehensive **API** (Application Programming
Interface) that can be used to access and manage data in CiviCRM. The
API is the recommended way for any CiviCRM extension, CMS module, or
external program to interact with CiviCRM.

Utilizing the API is superior to accessing core functions directly (e.g.
calling raw SQL, or calling functions within the BAO files)
because the API offers a consistent interface to CiviCRM's features. It is
designed to function predictably with every new release so as to preserve
backwards compatibility of the API for several versions of CiviCRM. If
you decide to use other ways to collect data (like your own SQL statements),
you risk running into future problems when changes to the schema and
BAO arguments inevitably occur.

The best place to begin working with the API is your own ***test*** install of
CiviCRM, using the API explorer and the API parameter list.

For help creating your own API custom calls, see [civix generate:api](/extensions/civix.md#generate-api)

## Versions

CiviCRM has two different versions of the API. Version 3 is considered the current stable version and Version 4 is the currently worked on version.

## API explorer

The API explorer is a powerful GUI tool for building and executing API calls.

To access the API explorer:

For API Version 3:

1. Go to any CiviCRM site
    * This can even be the [demo site](http://dmaster.demo.civicrm.org/).
1. Within the CivCRM menu, go to **Support > Developer > API Explorer v3** or go to the URL `/civicrm/api`.

For API Version 4:

1. Go to any CiviCRM Site
    * This can even be the [demo site](http://dmaster.demo.civicrm.org/).
1. Within the CivCRM menu, go to **Support > Developer > API Explorer v4** or go to the URL `/civicrm/api4#explorer`

!!! warning
    The API explorer actually executes real API calls. It can modify data! So if you execute a `Contact` `delete` call, it will really delete the contact. As such, any experimenting is best done within a test site.

You can select the entity you want to
use, for example `Contact` and the action you want to perform, for
example `Get`. The API explorer
will show you the specific code necessary to execute the API call you
have been testing.

## API parameter documentation

For API version 3 from the API explorer, you can click on the **Code Docs** tab to find documentation for each API entity/action. You will first get a list of all the API entities. If you click on an entity you will get a list of parameters that are available for that specific entity, with the type of the parameter. This can be very useful if you want to check what you can retrieve with the API and what parameters you can use to refine your get
action or complete your create or update action.

## API examples

For API version 3 from the API explorer, you can click on the **Examples** tab to find examples of API calls which are based on automated tests within the source code. You can also [explore these examples on GitHub](https://github.com/civicrm/civicrm-core/tree/master/api/v3/examples).

For API version 4 the explorer doesn't explose any examples but does show off code options.

### API Examples in your extensions

From CiviCRM v5.8 the APIv3 explorer will now be able to show examples that are stored in your extension. The only requirement is that they are found in the same sort of directory structure as core e.g. in `<yourextension>/api/v3/examples/<entity>/<file>`

## Changelog

All important changes made to the API are recorded in [API changes](/api/changes.md).

## Version 4 Changes from version 3

There are a number of significant changes from version 3 to version 4 of the API.

Most notably by default check permissions are turned on by default no matter the end point that is accessed by the code. Whereas in APIv3, if you called from PHP code the check_permissions flag was set to false by default but for javascript and rest API it was turned on by default. Other changes are that the default limit on get actions have been removed from the API.

Other notable changes are

* **Api wrapper**:
    * In addition to the familiar style of civicrm_api4('Entity', 'action', $params) there is now an OO style in php `\Civi\Api4\Entity::action()`.
    * When chaining api calls together, backreferences to values from the main api call must be explicitly given (discoverable in the api explorer).
    * A 4th param index controls how results are returned: Passing a string will index all results by that key e.g. `civicrm_api4('Contact', 'get', $params, 'id')` will index by id. Passing a number will return the result at that index e.g. `civicrm_api4('Contact', 'get', $params, 0)` will return the first result and is the same as `\Civi\Api4\Contact::get()->execute()->first()`. -1 is the equivalent of `$result->last()`.
* **Actions**:
    * Use the Update action to update an entity rather than Create with an id.
    * Update and Delete can be performed on multiple items at once by specifying a where clause, vs a single item by id in api3.
    * getsingle is gone, use $result->first() or index 0.
    * getoptions is no longer a standalone action, but part of getFields.
* **Input**
    * Instead of a single `$params` array containing a mishmash of fields, options and parameters, each api4 parameters is distinct. e.g. for the Get action: select, where, orderBy and limit are different params.
    * Custom fields are refered to by name rather than id. E.g. use `constituent_information.Most_Important_Issue` instead of `custom_4`.
* **Output**
    * Output is an array with object properties rather than a nested array.
    * In PHP, you can foreach the results arrayObject directly, or you can call methods on it like `$result->first()` or `$result->indexBy('foo')`.
    * By default, results are indexed sequentially like api3 `sequential => 1`, but the index param or `indexBy()` method let you change that. e.g. `civicrm_api4('Contact', 'get', $params, 'id')` or `\Civi\Api4\Contact::get()->execute()->indexBy('id')` will index results by id.


