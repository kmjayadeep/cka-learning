# CKA Learning Repo

My notes and preparation materials for Certified Kubernetes Administrator Exam. This repo may also contain additional resources and links which are not related to CKA, but generally useful for Kubernetes administration


# Folder structure

* README.md - outline, plan, structure etc.
* notes/ - Notes I prepared while learning, based on curriculm. They are organized on the order of curriculm and have a number prefix. There are some additional notes I added randomly, they won't have this number prefix
* tests/ - Practice tests I went through and their solution. In the order of curriculm
* yamls/ - Yaml files, certs etc. I created as part of the practice tests
* lxc.md - instructions to setup lxc containers
* kubernetes-hard-way-\*.md - Instructions I followed to setup kubernetes based on the famous `kubernetes the hard way` guide

# Things to learn and research

* LXC containers
* Kubernetes the hard way <https://github.com/kelseyhightower/kubernetes-the-hard-way>
* [killer.sh] Possibly a good way to experience the exam environment. Need to check if it provides free access
* <https://github.com/alijahnas/CKA-practice-exercises>
* [https://medium.com/@pmvk/tips-to-crack-certified-kubernetes-administrator-cka-exam-c949c7a9bea1]
* play with kind
* RBAC and certificates and stuff
* PriorityClass, ResourceQuotas, NetworkPolicy
* Generators for yaml

# Plan

- [x] Complete `Kubernetes the hard way` using LXC containers and document the steps (Or maybe using digitalocean droplets) - try multiple times
- [x] Go through handbook and curriculm
- [ ] Learn RBAC from official doc
- [ ] Go through a CKA excercises repo and try out all the excercises
- [x] Register for the Exam
- [ ] Master the kubernetes official documentation
- [ ] Take some sample test from somewhere
- [ ] Install kubernetes in a single node using binaries, without tls and all


# Links

* kubernetes-the-hard-way steps using LXC containers : [https://github.com/kmjayadeep/cka-learning/blob/main/kubernetes-hard-way.md]
* CKA curriculm [https://github.com/cncf/curriculum/blob/master/CKA_Curriculum_v1.21.pdf]

# Environment

## Kind cluster

Kubernetes in docker is the easiest way to get started with kubernetes. It has most of the things needed to practice in-cluster scenarios.
Also, it is 1.21 Kubernetes, which is what we'll be having in the exam this quarter. It uses etcd too. So  we can try out etcd commands as well - like backup and restore


```
kind create cluster
❯ kind create cluster
Creating cluster "kind" ...
 ✓ Ensuring node image (kindest/node:v1.21.1) 🖼
 ✓ Preparing nodes 📦  
 ✓ Writing configuration 📜 
 ✓ Starting control-plane 🕹️ 
 ✓ Installing CNI 🔌 
 ✓ Installing StorageClass 💾 
Set kubectl context to "kind-kind"
You can now use your cluster with:

kubectl cluster-info --context kind-kind
```

## Setting context and namespace

```
k config set-context kind-kind --namespace=kube-system
k config use-context kind-kind
```

## Learning Plan

* Day - 1 (23/07/2021): Kubernetes the hard way
* Day - 2 (24/07/2021): Kubernetes the hard way
* Day - 3 (25/07/2021): RBAC basics
* Day - 4 (26/07/2021): RBAC yamls and excercises - role, clusterole, serviceaccount, clusterrolebinding, rolebinding
* Day - 5 (27/07/2021): certificates, creating new user, kubeadm
