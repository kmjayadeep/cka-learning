# Practice tests 5 - Troubleshooting

* Add a sidecar to the following pod to print the logs to stdout

```
apiVersion: v1
kind: Pod
metadata:
  name: counter
spec:
  containers:
  - name: count
    image: busybox
    args:
    - /bin/sh
    - -c
    - >
      i=0;
      while true;
      do
        echo "$i: $(date)" >> /var/log/1.log;
        echo "$(date) INFO $i" >> /var/log/2.log;
        i=$((i+1));
        sleep 1;
      done      
```

ans:
```
apiVersion: v1
kind: Pod
metadata:
  name: counter
spec:
  volumes:
  - name: log
    emptyDir: {}
  containers:
  - name: logger
    image: busybox
    volumeMounts:
    - name: log
      mountPath: /var/log
    args:
    - /bin/sh
    - -c
    - "tail -f /var/log/1.log"
  - name: count
    image: busybox
    volumeMounts:
    - name: log
      mountPath: /var/log
    args:
    - /bin/sh
    - -c
    - >
      i=0;
      while true;
      do
        echo "$i: $(date)" >> /var/log/1.log;
        echo "$(date) INFO $i" >> /var/log/2.log;
        i=$((i+1));
        sleep 1;
      done      
```

# Create a deployment, a service and dump the iptable rules corresponding to that service

```
k create deploy nginx --image nginx --replicas=3
k expose deploy nginx --port=80
```

ssh node

```
iptables-save  | grep nginx > nginx-rules
```
