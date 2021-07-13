- Next Chapter: [mongodb-deployment.md](mongodb-deployment.md)
- https://www.youtube.com/watch?v=bhBSlnQcq2k&t=10922s

Minikube:
- It is a 1 node k8s cluster for testing purpose
- https://minikube.sigs.k8s.io/docs/start/
- Minikube has dependency on kubectl i.e when minikube is installed, the kubectl also gets installed automatically.
- Minikube is used for starting and deleting the cluster but rest of the work is actually done by kubectl. 

Setup:

    $ curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-amd64
    $ sudo install minikube-darwin-amd64 /usr/local/bin/minikube

    $ minikube version
    minikube version: v1.22.0
    commit: a03fbcf166e6f74ef224d4a63be4277d017bb62e

    $ kubectl version
    Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.5", GitCommit:"20c265fef0741dd71a66480e35bd69f18351daea", GitTreeState:"clean", BuildDate:"2019-10-15T19:16:51Z", GoVersion:"go1.12.10", Compiler:"gc", Platform:"darwin/amd64"}
    The connection to the server localhost:8080 was refused - did you specify the right host or port?

Starting Cluster:

    $ minikube start
    😄  minikube v1.22.0 on Darwin 10.14.6
    ✨  Automatically selected the docker driver. Other choices: hyperkit, parallels, ssh
    👍  Starting control plane node minikube in cluster minikube
    🚜  Pulling base image ...
    💾  Downloading Kubernetes v1.21.2 preload ...
        > gcr.io/k8s-minikube/kicbase...: 361.09 MiB / 361.09 MiB  100.00% 363.41 K
        > preloaded-images-k8s-v11-v1...: 502.14 MiB / 502.14 MiB  100.00% 447.18 K
    🔥  Creating docker container (CPUs=2, Memory=1989MB) ...
    🐳  Preparing Kubernetes v1.21.2 on Docker 20.10.7 ...
        ▪ Generating certificates and keys ...
        ▪ Booting up control plane ...
        ▪ Configuring RBAC rules ...
    🔎  Verifying Kubernetes components...
        ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
    🌟  Enabled addons: storage-provisioner, default-storageclass

    ❗  /usr/local/bin/kubectl is version 1.15.5, which may have incompatibilites with Kubernetes 1.21.2.
        ▪ Want kubectl v1.21.2? Try 'minikube kubectl -- get pods -A'
    🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
    
    $ minikube status
    minikube
    type: Control Plane
    host: Running
    kubelet: Running
    apiserver: Running
    kubeconfig: Configured

    $ minikube stop
    ✋  Stopping node "minikube"  ...
    🛑  Powering off "minikube" via SSH ...
    🛑  1 nodes stopped

Kubernetes: Before creating any resource:

    $ kubectl version
    Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.5", GitCommit:"20c265fef0741dd71a66480e35bd69f18351daea", GitTreeState:"clean", BuildDate:"2019-10-15T19:16:51Z", GoVersion:"go1.12.10", Compiler:"gc", Platform:"darwin/amd64"}
    Server Version: version.Info{Major:"1", Minor:"21", GitVersion:"v1.21.2", GitCommit:"092fbfbf53427de67cac1e9fa54aaa09a28371d7", GitTreeState:"clean", BuildDate:"2021-06-16T12:53:14Z", GoVersion:"go1.16.5", Compiler:"gc", Platform:"linux/amd64"}

    $ kubectl get nodes
    NAME       STATUS   ROLES                  AGE     VERSION
    minikube   Ready    control-plane,master   3m20s   v1.21.2

    $ kubectl get pods
    No resources found.

    $ kubectl get services
    NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
    kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   16m

Kubernetes: Create first deployemnt(aka pod) using command line:

    $ kubectl create deployment nginx-depl --image=nginx
    deployment.apps/nginx-depl created

    $ kubectl get deployment
    NAME         READY   UP-TO-DATE   AVAILABLE   AGE
    nginx-depl   1/1     1            1           86s

    $ kubectl get pods
    NAME                          READY   STATUS    RESTARTS   AGE
    nginx-depl-5c8bf76b5b-5xkj6   1/1     Running   0          107s

    $ kubectl logs nginx-depl-5c8bf76b5b-5xkj6
    //nginx logs displayed here

    //log into the container
    $ kubectl exec -it nginx-depl-5c8bf76b5b-5xkj6 -- bin/bash
    root@nginx-depl-5c8bf76b5b-5xkj6:/# 

    $ kubectl delete deployment nginx-depl
    deployment.apps "nginx-depl" deleted

    $ kubectl get pods
    NAME                                READY   STATUS        RESTARTS   AGE
    nginx-depl-5c8bf76b5b-gg6nf         0/1     Terminating   0          51s

Kubernetes: Create deployment using yaml file.
- See nginx-deployment.yaml in this directory.

    $ kubectl apply -f nginx-deployment.yaml
    deployment.apps/nginx-deployment created

    $ kubectl get pods
    NAME                                READY   STATUS    RESTARTS   AGE
    nginx-deployment-644599b9c9-bzrvm   1/1     Running   0          5m32s