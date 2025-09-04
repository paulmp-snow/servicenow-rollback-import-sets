# ServiceNow: Rollback Import Sets

### Scope:

Provide the functionality to rollback inserts and updates after transforming an Import Set.

### Components:

- Table: Rollback record (u_rollback_record)
- Script Include: RollbackImportsUtil
- UI Actions: Rollback Record, Rollback Records
- UI Policy: [ROLL] Make fields readonly

Additions: added related list for rollback records on the Import Set form default view

### How to use:

The way that the functionality works is that for each import set transform and for each import set row in that transform a rollback record is created that holds the initial values of the target record and the updated ones. The rollback record is then used to revert the target value to the previous values and also easily check for changes after transforms.

For this to work a snapshot of the target record is needed before the transform starts and another snapshot after the transform ends. This can be done by adding to the transform map two scripts: onBefore and onComplete.

The onBefore script runs before any operation is done, for each row and can be used to determine the target. To create the first snapshot just use the following line:

new RollbackImportsUtil().createRollbackRecord(source, target, action);

The onComplete script runs only once when the transform is complete. At this stage the import rows should have the target set and we can get the updated values. To do that use the following line:

new RollbackImportsUtil().updateRollbackRecordsAfterTranform(source);

For a simple transform map this is all that is required to create the rollback records. For more complex ones with dynamic targets, call the createRollbackRecord method when required.

In order to roll back the records you can use the UI Action from each individual rollback record or use the UI Action from the Transform History and rollback all records associated with that transformation.
