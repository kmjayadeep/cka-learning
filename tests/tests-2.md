# Practice tests 2 - Workloads and Scheduling

## Deployments and Rollouts

* Create a deployment `web` using the image `nginx` which has 3 replicas. Watch it's rollout status

```
k create deploy web --image=nginx --replicas=3
k rollout status deploy/web
```

* Update the container image for the above deployment to `nginx:1.16.3` and show rollout history

```
k set image deploy/web nginx=nginx:1.16.1
k rollout history deploy web
```

* Undo the rollout to a previous revision

```
k rollout undo deploy/web --to-revision=9
```

* Scale up web to 10 pods

```
k scale deploy web --replicas=10
```

* Pause the rollout, update the cpu,memory limits and requests, update image and then resume rollout

```
k rollout pause deploy/web
```

```
k set resources deploy web -c nginx --limits=cpu=10m,memory=20Mi
k set resources deploy web -c nginx --requests=cpu=5m,memory=5Mi
```

* Create a deployment web with nginx image of replica count 3. Set maxsurge to 0 and max unavailable to 50%.
Then set the image to nginx:1.16.1 and watch pods getting recreated

```
k create deploy web --replicas=3 --image=nginx --dry-run=client -o yaml > web.yaml
```

Then add the strategy as below

```
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 50%
    maxSurge: 0%
```

## Configmaps

* Create a configmap called `myconfig` with one key `hello`  and value `world`

```
k create cm myconfig --from-literal=hello=world
```

* Create a busybox deployment, pass the above configmap as an environment variable.
Open a shell into the pod and print env variables

```
k create deploy busybox --image=busybox -o yaml --dry-run=client > busybox.yaml
```

Add the following to container spec

```
env:
- name: HELLO
  valueFrom: 
    configMapKeyRef:
      name: myconfig
      key: hello
```


* Modify the same busybox deployment to mount the secret as a file and
print the output that file using `cat` command

```
volumes:
- name: config
  configMap:
    name: myconfig
containers:
- image: busybox
  name: busybox
  command: [ "cat", "/cm/hello" ]
  volumeMounts:
  - name: config
    mountPath: /cm
    readOnly: true
```
