# Primitives for Robust self healing deployments 

## Pod Disruption Budget

PDB can limit the number of concurrent disruption your application can afford.
It can protect critical workloads from various voluntary disruptions like,
draining a node. It doesn't seem to affect when we delete pods manually.

kubernetes will fail the voluntary disruption unless the PDB is met.
So For example, If you try to drain a node, it will gradually move the pods to a healthy node

PDB spec can have either `maxUnavailable` or `minAvailable` specified as a number of pods or a percentage of total count.
It takes a label selector to match the pods rather than directly specifying a deployment or replicaset.

eg:

```
k create pdb web-pdb --selector app=web --min-available 50%
```

## Livenes, Readiness and Startup Probes

* liveness probe -> when to restart container
* readiness probe -> when it is ready to accept traffic
* startup probe -> when app has started and ready to run liveness and readiness probes

example liveness with a command

```
livenessProbe:
  exec:
    command:
    - cat
    - /tmp/healthy
  initialDelaySeconds: 5
  periodSeconds: 5
```

These probes are configured at the container level, not pod level

## Lifecycle hooks

We can add lifecycle hooks to pod events like postStart and preStop. 
They follow similar syntax as the probes.

eg:

```
lifecycle:
  postStart:
    exec:
      command: ["/bin/sh", "-c", "echo Hello from the postStart handler > /usr/share/message"]
  preStop:
    exec:
      command: ["/bin/sh","-c","nginx -s quit; while killall -0 nginx; do sleep 1; done"]
```

These hooks are also configured at the container level.
