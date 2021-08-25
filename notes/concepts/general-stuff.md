# General Stuff

* In `k describe` command, if you enter a prefix of the resource name, it will describe all the resources which match the prefix
* Kubectl apply -k to apply with `kustomize`
* Field selectors lets you filter resources based on arbitary fields. eg:
  `k get po --field-selector status.phase=Running`
* kubectl rollout restart [name] -> To do a rolling restart of a workload
* `kind` uses `containerd` internally. Use `crictl ps` to list the pods.
* use grep -A to show more output lines after grep
* use `k replace` instead of delete and apply
* use `k auth can-i` to check if a rolebindig works
* `--no-headers` flag in k get commands to avoid headers
* `&>` redirects both stdout and stderr
