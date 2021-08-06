# Finalizers

Finalizers lets you do some action right before deleting an object

Add a name to list of finalizers in the spec. Then kubernetes will add a metadata.deletionTimestamp to the resource.
This can be tracked as an indication that the resource is going to get deleted. Then the finalizer can be removed
after doing the necessary action and then delete the object itself.
