# APIv3 changes

This page lists changes to CiviCRM core and API Extension Code (prior to CiviCRM version 5.19) which affect the ways in which developers use the APIv4.

## APIv4 Extension version 4.5.0 - default Location Type and default where values

From extension version 4.5.0 onwards. When creating Entities such as Address, IMs, Emails etc the default Location Type will now be set. When running get actions on Entities with an `is_deleted` field, by default the where clause will exclude `is_deleted` = 1 rows.

## APIv4 Extension version 4.3.0 - DAOEntity Base Class

From 4.3.0 onwards there is now a generic DAOEntity base class which contains basic methods for creating updating replacing entities that are based on DAO Objects.

For developers creating their own entities if the entity is a basic DAO entity developers can create a basic Entity API file such as 

```php

<?php

namespace Civi\Api4
use Civi\Api4\Generic\DAOEntity;

class <YourEntity> extends DAOEntity {

}
```
