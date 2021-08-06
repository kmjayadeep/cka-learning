# Awareness of manifest management and common templating tools

# Kustomize

Kustomize is used to manage and customize kubernetes manifests.
One of the useful feature kustomize provides is the ability to
generate ConfigMaps and Secrets and append the hash of the contents
to the name of the resource.

This forces the dependent resources to be recreated if the ConfigMap/Secret
contents are changed.

It add common annotations/labels to the resources. Specify namespace to all the resources etc.

# Helm

Helm is a package manager for kubernetes manifests. It lets you bundle kubernetes manifests
as a single package and let you customize the manifests through templates and values file.

Additionally it supports versioning, rollback, updates, dependency management etc.

To install a chart

```
helm repo add name url
helm install releasename repo/chartname
```
