#### PODS

Assumptions:
- Application image has been created. 
- Application image has been uploaded to docker-hub (or any docker registry)

PODs overview: 

- Each docker `container` runs inside a `POD`. 
- There can be one or more PODs running on a `node` or `worker-node`. 
- The `node` can be a physical or virtual machine. 
- To handle increased traffic, additional `PODs` can be created in the same `node` or a different node. 
- Multiple `containers` of the same-type cannot exists inside a single `POD`. 
- A single POD can contain multiple containers of different image-types.
- If multiple containers are running inside the same POD then they can access each other using `localhost`. 


Install kubectl: 
- https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/


Install Minikube: 
- minikube is local Kubernetes, focusing on making it easy to learn and develop for Kubernetes.
- No permission to install VirtualBox on Expedia Mac, so I'll be using Dockers instead. 
- https://medium.com/@rajeshmamuddu/setup-minikube-with-docker-driver-539c151b1b7e
- Pre-requisite: Install Docker and Docker-Desktop

```
$ brew install minikube

//start local kubernetes cluster
$ minikube start --driver=docker

$ minikube status

$ kubectl get nodes

// As of version 1.18, kubectl run will create a pod instead of a deployment.
// create and run a particular image in a pod
$ kubectl run nginx --image=nginx

$ kubectl get pods
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          6m16s

$ kubectl describe pod nginx
Name:             nginx
Namespace:        default
Priority:         0
Service Account:  default
Node:             minikube/192.168.49.2
Start Time:       Wed, 04 Dec 2024 13:58:37 -0600
Labels:           run=nginx
Annotations:      <none>
Status:           Running
IP:               10.244.0.3
IPs:
  IP:  10.244.0.3
Containers:
  nginx:
    Container ID:   docker://fe9c543e1a5ed0f2bf56396fb3d47a499764758465a928b9aa24a1cfb19b57c3
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:fb197595ebe76b9c0c14ab68159fd3c08bd067ec62300583543f0ebda353b5be
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Wed, 04 Dec 2024 13:58:42 -0600
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-vvnh9 (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  kube-api-access-vvnh9:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  7m11s  default-scheduler  Successfully assigned default/nginx to minikube
  Normal  Pulling    7m11s  kubelet            Pulling image "nginx"
  Normal  Pulled     7m6s   kubelet            Successfully pulled image "nginx" in 4.649s (4.649s including waiting). Image size: 197054391 bytes.
  Normal  Created    7m6s   kubelet            Created container nginx
  Normal  Started    7m6s   kubelet            Started container nginx

$ kubectl get pods -o wide
NAME    READY   STATUS    RESTARTS   AGE     IP           NODE       NOMINATED NODE   READINESS GATES
nginx   1/1     Running   0          9m29s   10.244.0.3   minikube   <none>           <none>


// this will remove the lingering Pod that has been created without deployment. 
// minikube is the name of the NODE
$ kubectl drain minikube --force --ignore-daemonsets
```

Deploy Application: 

- https://minikube.sigs.k8s.io/docs/start/?arch=%2Fmacos%2Farm64%2Fstable%2Fbinary+download

```
$ kubectl create deployment hello-minikube --image=kicbase/echo-server:1.0
$ kubectl expose deployment hello-minikube --type=NodePort --port=8080
$ kubectl get services hello-minikube
$ minikube service hello-minikube

$ kubectl get deployments
```

Manage Cluster:

```
$ minikube pause
$ minikube unpause
$ minikube stop
$ minikube delete --all
```





