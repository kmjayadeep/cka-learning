# Containers

Kubernetes supports several container runtimes like Docker,
CRIO, Containerd etc.

`ImagePullBackOff` -> kubelet is not able to pull the image from registry

container hostname will be the name of the pod. We can pass more details
as environment variable through DownwardAPI

List of services available in the same namespace is passed on as 
environment variables to the container.

## Using private registry


To use private registry, there are several ways

1. Pre-pulled images
1. ImagePullSecrets defined for the pod (passed as secret)
1. Authenticate nodes to private registry
1. Vendor specific extensions

We can login to each node and authenticate the container runtime
manually to our private registry.
For example, in docker we can use `docker login` command in each node.
Or copy docker config json to each nodes.


To use ImagePullSecrets,

Create secret with creds

```
kubectl create secret docker-registry <name>\
--docker-server=DOCKER_REGISTRY_SERVER\
--docker-username=DOCKER_USER\
--docker-password=DOCKER_PASSWORD\
--docker-email=DOCKER_EMAIL
```

Add this to pod spec

```
imagePullSecrets:
- name: docker-registry
```

We can also set the imagePullSecrets to a serviceaccount, thus
adding the secret to all the pods which use this serviceaccount.

Just edit the serviceaccount and add the above snippet. Then all
the pods which use this serviceaccount will have imagePullSecrets
automatically populated.
