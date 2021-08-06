# General Stuff

* In `k describe` command, if you enter a prefix of the resource name, it will describe all the resources which match the prefix
* Kubectl apply -k to apply with `kustomize`
* Field selectors lets you filter resources based on arbitary fields. eg:
  `k get po --field-selector status.phase=Running`
