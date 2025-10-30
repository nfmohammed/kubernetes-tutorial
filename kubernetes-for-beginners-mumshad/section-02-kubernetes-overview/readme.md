- Overview of Containers
    - Difference between Images and Containers

- Learn more about Dockers in separate courses
    - Docker for the Absolute Begineers - Hands on
    - Docker SWARM - SERVICES - STACKS

- Container Orchestration tools:
    - Docker swarm - from Dockers
    - Kubernetes - from Google
    - Mesos - from Apache

#### Kubernetes Architecture

Kubernetes Components: 
- API server: FE for kubernetes
- Scheduler: Distributing the work (or containers) across multiple nodes
- Controller: Brain behind orchestration. They detected if a node goes down and make decision to spin up new nodes. 
- Container Runtime:  - like Docker
- kubelet: Agent that runs on each node in the cluster
- etcd: distributed key-value store to manage the cluster, contains multiple master and worker node info. 

Master vs Workder Nodes:
- Master contains the followig k8s components
    - kube-apiserver
    - etcd
    - controller
    - scheduler
- Worker contains the following k8s components
    - kubelet
    - Container Runtime

#### Kubectl

#### Docker vs ContainerD

