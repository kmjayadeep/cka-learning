# Practice tests 2 - Workloads and Scheduling

## Deployments and Rollouts

* Create a busybox pod which prints the date in a loop every second


```
k run busy --image busybox --dry-run=client -o yaml -- sh -c "while true;do date;sleep 1;done;"
```

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

## Secrets

* Create a secret `web` from a file named `web.yaml`

```
k create secret generic web --from-file=web.yaml
```

* Print the value of a key `web.yaml` in the secret `web`

```
k get secret web -o json | jq -r '.data["web.yaml"]' | base64 -d
```

* Create a deployment which mounts this secret and prints the contents using `cat`

```
volumes:
- name: sec
  secret:
    secretName: web
containers:
- image: busybox
  name: busybox
  command: ["cat", "/se/web.yaml"]
  volumeMounts:
  - name: sec
    readOnly: true
    mountPath: /se
```

## Scale

* Create a deployement `web` using `nginx` image. Then scale it to 3 pods afterwards

```
k create deploy web --image nginx
```

```
k scale deploy web --replicas=3
```

* Create a deployment of a custom apache server below
```
kubectl apply -f https://k8s.io/examples/application/php-apache.yaml
```

* Create an autoscaler for the apache with pods 2-10 and target cpu 70%

```
k autoscale deploy php-apache --min 2 --max 10 --cpu-percent 70
```

## Pod disruption budget

* Create a deployement web with nginx image and replicas 4.
Create a disruption budget of 50% min available

```
k create deploy web --image nginx --replicas 4
k create pdb web-pdb --selector app=web --min-available 50%
```

## Probes

* Create a deployement with httpget livenesspobe

```
containers:
- image: nginx
  name: nginx
  resources: {}
  livenessProbe:
    httpGet:
      path: /
      port: 80
```

* Create a deployment with exec livenessProbe

```
livenessProbe:
  exec:
    command:
    - cat
    - /tmp/healthy
  initialDelaySeconds: 5
  periodSeconds: 5
```

## Resource limits

* Create a deployment `web` with image `nginx` and a cpu limit & request of 100m

add this to container spec after creating deployement.
```
limits:
  cpu: 100m
requests:
  cpu: 100m
```

## Jobs

* Create a job `date` using busybox image to print the current date

```
k create job date --image busybox -- date
```
