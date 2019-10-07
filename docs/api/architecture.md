# API Architecture

## Version 4

* An [**Entity**](https://github.com/civicrm/civicrm-core/blob/master/Civi/Api4/Generic/AbstractEntity.php) is a class implementing one or more static methods (`get()`, `create()`, `delete()`, etc).
* Each static method constructs and returns an [**Action object**](https://github.com/civicrm/civicrm-core/blob/master/Civi/Api4/Generic/AbstractAction.php).
* All actions extend the [AbstractAction class](https://github.com/civicrm/civicrm-core/blob/master/Civi/Api4/Generic/AbstractAction.php). A number of other abstract action classes build on this, e.g. [AbstractBatchAction](https://github.com/civicrm/civicrm-core/blob/master/Civi/Api4/Generic/AbstractBatchAction.php) is the base class for batch-process actions (`delete`, `update`, `replace`).
* Most entity classes correspond to a `CRM_Core_DAO` subclass. E.g. `Civi\Api4\Contact` corresponds to `CRM_Contact_DAO_Contact`.
* A set of **`DAO` action classes** (e.g. [DAOGetAction](https://github.com/civicrm/civicrm-core/blob/master/Civi/Api4/Generic/DAOGetAction.php), [DAODeleteAction](https://github.com/civicrm/civicrm-core/blob/master/Civi/Api4/Generic/DAODeleteAction.php)) exists to support DAO-based entities. [DAOGetAction](https://github.com/civicrm/civicrm-core/blob/master/Civi/Api4/Generic/DAOGetAction.php) uses [`Api4SelectQuery`](https://github.com/civicrm/civicrm-core/blob/master/Civi/API/Api4SelectQuery.php) to query the database.
* A set of **`Basic` action classes** (e.g. [BasicGetAction](https://github.com/civicrm/civicrm-core/blob/master/Civi/Api4/Generic/BasicGetAction.php), [BasicBatchAction](https://github.com/civicrm/civicrm-core/blob/master/Civi/Api4/Generic/BasicBatchAction.php)) exists to support many other use-cases, e.g. file-based entities.
* The base action `execute()` method calls the core [`civi_api_kernel`](https://github.com/civicrm/civicrm-core/blob/master/Civi/API/Kernel.php)
service `runRequest()` method which invokes hooks and then calls the `_run` method for that action.
* Each action object has a `_run()` method that accepts and updates a [`Result`](https://github.com/civicrm/civicrm-core/blob/master/Civi/Api4/Generic/Result.php) object (which is an extended [ArrayObject](http://php.net/manual/en/class.arrayobject.php)).
* For DAO based entities the stanard way is to extend [`Generic\DAOEntity`](https://github.com/civicrm/civicrm-core/blob/master/Civi/Api4/Generic/DAOEntity.php) which ensures a standard way of interacting with the DAO objects.

