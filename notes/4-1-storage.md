# Storage

We can create PV with a storageclassname which doesn't have a
storageclass defined. Then create a PVC with the same storageclassname.
It will make sure that the PVC is always bound to this PV even if there
are other PVs with matching spec. This is handy when doing manual PV
provisioning

To use with storageclass, we only need to create pvc. storage class
provisioner will create the pv for us with the exact requirements.

When we use a storageclass and the `volumebindingmode` is
waitforfirstconsumer, the pv will be created only when the pod requests
the pvc. pvc will be in pending state till then

