# Finalizers

Finalizers lets you do some action right before deleting an object

Add a name to list of finalizers in the spec. Then kubernetes will add a metadata.deletionTimestamp to the resource.
This can be tracked as an indication that the resource is going to get deleted. Then the finalizer can be removed
after doing the necessary action and then delete the object itself.

# Owner reference

Owner reference can be set in metadata of each kubernetes object.
It is used to identify which object is dependent on other. When the owner
object is deleted, the child objects are deleted automatically
through cascade deletion.

There are two types of cascading deletion, foreground and background.
In Background deletion, the owner will be deleted immedietly and the 
dependents are deleted in the background. In foreground deletion, 
the deletion happens through finalizers and makes sure the owner
is deleted only after deleting the dependents.

You can also decide to orphan the dependents instead of deleting them.
These options can ba passed as deleteOptions propogationPolicy
by making a DELETE api call to the api server.

# Images 

Images are automatically cleaned up by the kubelets based
on it's configuration by checking various factors like disk usage, age etc.
