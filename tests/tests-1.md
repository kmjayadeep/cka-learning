# Practice tests 1 - Cluster Architecture, Installation and Configuration

## RBAC


* Create a role that will allow users to get, list, watch pods and view container logs

```
k create role pod-reader --resource=pod --resource=pod/log --verb=list --verb=get --verb=watch --dry-run -o yaml > pod-reader.yaml
k apply -f pod-reader.yaml
```

* Create a rolebinding that binds the role pod-reader to a user named `dev`

```
k create rolebinding podreader-dev --role=podreader --user=dev --dry-run=client -o yaml > podreader-dev.yaml
k apply -f podreader-dev.yaml
```

* Create a new service account `secretguy` and give it permissions to list and get secrets in the namespace

```
k create serviceaccount secretguy
k create role secretreader --resource=secret --verb=list --verb=get --dry-run=client -o yaml > secretreader.yaml
k apply -f secretreader.yaml
k create rolebinding secretreader-binding --role=secretreader --serviceaccount="default:secretguy" --dry-run=client -o yaml > secretguy-binding.yaml
k apply -f secretguy-binding.yaml
```


## Learnings

* Create the role using `k create role` command. Use `k create role -h` help if get stuck
* API Group need not be provided. It will be filled automatically with create role command
* Write to yaml, verify and apply it
* use `--as=username` and `--as-group=groupname` to run kubectl commands as a particular user and group
