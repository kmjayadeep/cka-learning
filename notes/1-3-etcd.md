# Etcd management

etcd is the storage layer for kubernetes. It is a highly available distributed key-value storage system.

## Taking etcd snapshots and restoring them. Refer <https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/>

Create a configmap for testing
```
kubectl create configmap test --from-literal=key1=config1 --from-literal=key2=config2
```

## Create etcd snapshot

Install etcd client first

```
apt install etcd-client
```

Check members

```
ETCDCTL_API=3 etcdctl --endpoints localhost:2379 \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  member list
```

Take in built snapshot

```
ETCDCTL_API=3 etcdctl --endpoints localhost:2379 \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  snapshot save backup
```

## Test snapshot restore

Delete the configmap created above

```
k delete cm test
```

Restore etcd from snapshot

```
ETCDCTL_API=3 etcdctl --endpoints localhost:2379 \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --data-dir /var/lib/etcd \
  snapshot restore backup
```

Note: To test restore on a running system,
1. Delete etcd pod from kube-system namespace
2. Delete /var/lib/etcd directory
3. Run the above restore command
4. Wait for some time and verify by checking the configmap
