# Configmap and Secrets to configure applications

# Configmaps

Stores data as key value pairs

It will have `data` and `binaryData` fields. Both are optional
data is utf-8, binaryData is base64 encoded string. Both are key value pairs map[string]string


The field `immutable`, when set to true, will make sure that cm cannot be edited or updated. The only option 
is to delete and recreate it. We cannot even set this flag to false afterwards

## using configmap inside a pod

* inside container command and args
* pass as env variables
* mount as read-only volume
* k8s api to read cm directly

Mounted configmaps are updated automatically. But environment variables are not updated. It needs pod restart

## creating configmap

This command explains about creating cm a great deal
```
k create cm -h
```

From file or folder
```
k create cm test --from-file=[file] --dry-run=client -o yaml
```
In the configmap, each file will be added as a key value pair, where key is the file name and value is the file contents.
If the file is not utf-8 (binary), then it will be encoded into base64 and added to `binaryData` field

You can also specify keys to each file individually rather than automatically taking the filename as key

From literal
```
k create cm test --from-literal abc=def --from-literal koo=kaa --dry-run=client -o yaml
```

From env file
```
k create cm test --from-env-file env --dry-run -o yaml
```

## Using configmap

As environment variables
```
env:
- name: doo
  valueFrom:
    configMapKeyRef:
      name: busyconfig
      key: works
```

use entire cm as env vars
```
envFrom:
- configMapRef:
  name: special-config
```

As volume mounts

Add this to pod spec. Items are optional. If you skip that, it will mount all keys as files
```
volumes:
- name: fromconfig
  configMap:
    name: test
    items:
    - key: jd.csr
      path: jd
```
Add mount to container spec

```
volumeMounts:
- name: fromconfig
  mountPath: /cm
  readOnly: true
```
Mounted volume is always readOnly even if you don't specify so.

# Secrets

Secrets are kind of similar to configMaps, but are encrypted at rest and are more secure.

It can contain `data` or `stringData` fields. `data` is base64 encoded and `stringData` is plain string.
Both are optional

The `type` can be `Opaque` (default) or any built-in types or it can be user defined.
It is used to differenciate the type of secret data.

We can create secret the same way as we did for confgmap

```
k create secret generic test --from-literal foo=bar
```

We can also create secrets for `tls` certs and `docker` secret using kubectl

Run the following to view more details about the args
`k create secret tls -h`
`k create secret docker-registry -h`

Secrets can be mounted to pods as volumes the same way as we did for configmap.
Secrets can also be passsed as environment variables the same way as configmap,
using `secretKeyRef` in `valueFrom` or `secretRef` in `envFrom`.

Secrets are mounted into a `tmpfs` in the node and mounted into the pod. This
avoids writing the secret into disk.
