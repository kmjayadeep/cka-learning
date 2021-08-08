# Storage

Volume - an abstration to attach persistant data storage to containers

k8s supports a number of types of volumes by default
eg: Aws elastic block store, configMap, emptyDir, downwardAPI etc

configMap, secret -> mount these resources as files

emptyDir -> create an empty space to share between containers in a pod. The
medium can be empty string or `Memory`

projected -> map multiple volumes sources in the same volume (secret, configMap,
downwardAPI etc)

# PV and PVC

Persistent Volume is provisioned by admininistrator or dynamically created
through storageclasses. It stores details about the backing storage, size,
accessmode etc.

Persistent Volume Claim is the Pod's request to access a persistant volume.
Claims can request specific size and access mode. If the claim matches an
existing persistant volume, it will be allocated (on static provisioning)

## Static provisioning

A cluster admin creates a number of PVs in advance. It will have predefined size
and acces modes and it will carry the details of the original backing storage.
It will be available for users to consume using a PVC.

## Dynamic provisioning

Admins will create and configure storageclasses in advance. Users can specify
the storageclass in the PVC to dynamically provision PVs based on the
storageclass.

## Binding

PV and PVC has a one-to-one mapping. If PV was created dynamically for a PVC,
then that PV will always be bound to the PVC. On the other hand, pvc will be
bound to PV which offers the least of what was requested. If there is no such PV
available, then the PVC will be unbound indefinitely until a PV comes to
existence.

if storage class is set as "", then PVC won't be dynamically bound. if storage
class is nil, then default storage class is used (if defaultstorageclass
admission plugin is turned on)

## Reclamining

reclaim policy for the PV tells the cluster what to do with once the pv is
released from its claim

* Retain -> PV will become `released`. Admin can take manual action to clear the
  storage or delete the PV to reuse it in a new PV
* Delete -> underlying storage will be deleted along with PV automatically
* Recycle -> deprecated

## PV

Access modes:
* ReadWriteOnce ->  Read write in a single node
* ReadOnlyMany ->  Read only in many nodes
* ReadWriteMany ->  Read write in many nodes
* ReadWriteOncePod

Node Affinity can be set on PVs to restrict which nodes the PVs are accessible.
The pods using these PVs will be scheduled only to these nodes where PVs can
be accesed.

## PVC

pvc can have label selectors and matchexpressions to choose which PVs can be bound to the PVC.

## StorageClass

It has the specs of the underlying storage backend. The provisioner determines
what kind of storage it is backend from. There are some inbuilt provisioners
like AWSElasticBlockStore, Glusterfs etc

The parameters in the spc specifies the parameters required to provision the
underying storage mechanism.

volume binding mode
* Immediete -> Create pvc without waiting for pod to be created. This can make
  pods unschedulable
* WaitForFirstConsumer -> Wait for pod to be created.
