# Practice tests 2 - Workloads and Scheduling

## Deployments

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
