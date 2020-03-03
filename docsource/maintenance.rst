Maintenance
===========



ELabInventory schema changes
----------------------------

Elab's behavior for schema changes is as follows:

1. create new field
    - does not affect existing records
    - once new field is created, existing records can be edited to add values to this new field.
    - once new field is created, new records will contain the new field.

2. update existing field
    - does not update field names of existing records or affect their existing values
    - however, editing existing fields will require adherence to the new field type and validation rules that have been changed
    - new records added after the update will get the new field name if field name is changed.
    - delete existing field - fields in existing records get deleted

**Best practice to follow**:

Add field or controlled vocabulary but never update or delete a field.

If there is a strong need to update a field, we should follow the 3-phase approach -
    1. add new field with update
    2. copy values from old field to new field
    3. delete old field.

We simply never delete a field. We only tag it as being deprecated in our documentation.