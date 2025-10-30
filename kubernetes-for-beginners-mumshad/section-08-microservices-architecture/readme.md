## Microservices Architecture

- We will use example of voting application to demonstrate microservices architecture. 
- Note: The worker node kept crashing so we were not able to make this example work. 

Step 1: Deploying voting-app Pod and Service

```
$ kubectl create -f 01-voting-app-pod.yaml 
pod/voting-app-pod created

$ kubectl create -f 08-voting-app-service.yaml 
service/voting-service created

$ kubectl get pods,svc 
NAME                 READY   STATUS    RESTARTS   AGE
pod/voting-app-pod   1/1     Running   0          33s

NAME                     TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
service/kubernetes       ClusterIP   10.96.0.1      <none>        443/TCP        8d
service/voting-service   NodePort    10.108.10.69   <none>        80:30004/TCP   22s

$ minikube service voting-service --url
http://127.0.0.1:50768
❗  Because you are using a Docker driver on darwin, the terminal needs to be open to run it.

```

Step 2: Deploying Redis In-Memory DB and it's service

```
$ kubectl create -f 03-redis-pod.yaml 
pod/redis-pod created

$ kubectl create -f 06-redis-service.yaml 
service/redis created

$ kubectl get pods,svc
NAME                 READY   STATUS    RESTARTS   AGE
pod/redis-pod        1/1     Running   0          27s
pod/voting-app-pod   1/1     Running   0          4m34s

NAME                     TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/kubernetes       ClusterIP   10.96.0.1       <none>        443/TCP        8d
service/redis            ClusterIP   10.97.192.161   <none>        6379/TCP       14s
service/voting-service   NodePort    10.108.10.69    <none>        80:30004/TCP   4m23s
```

Step 3: Deploying Postgress Database and it's service

```
$ kubectl create -f 04-postgres-pod.yaml 
pod/postgres-pod created

$ kubectl create -f 07-postgres-service.yaml 
service/db created

$ kubectl get pods,svc                      
NAME                 READY   STATUS              RESTARTS   AGE
pod/postgres-pod     0/1     ContainerCreating   0          9s
pod/redis-pod        1/1     Running             0          2m32s
pod/voting-app-pod   1/1     Running             0          6m39s

NAME                     TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/db               ClusterIP   10.97.150.27    <none>        5432/TCP       2s
service/kubernetes       ClusterIP   10.96.0.1       <none>        443/TCP        8d
service/redis            ClusterIP   10.97.192.161   <none>        6379/TCP       2m19s
service/voting-service   NodePort    10.108.10.69    <none>        80:30004/TCP   6m28s
```

Step 4: Deploying Worker Pod

```
$ kubectl create -f 05-worker-app-pod.yaml 
pod/worker-app-pod created

$ kubectl get pods,svc                    
NAME                 READY   STATUS              RESTARTS   AGE
pod/postgres-pod     1/1     Running             0          2m31s
pod/redis-pod        1/1     Running             0          4m54s
pod/voting-app-pod   1/1     Running             0          9m1s
pod/worker-app-pod   0/1     ContainerCreating   0          7s

NAME                     TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/db               ClusterIP   10.97.150.27    <none>        5432/TCP       2m24s
service/kubernetes       ClusterIP   10.96.0.1       <none>        443/TCP        8d
service/redis            ClusterIP   10.97.192.161   <none>        6379/TCP       4m41s
service/voting-service   NodePort    10.108.10.69    <none>        80:30004/TCP   8m50s
```

Step 5: Deploying Result App Pod and Service

```
$ kubectl create -f 02-result-app-pod.yaml 
pod/result-app-pod created

$ kubectl create -f 09-result-app-service.yaml 
service/result-service created

$ kubectl get pods,svc                        
NAME                 READY   STATUS             RESTARTS      AGE
pod/postgres-pod     1/1     Running            0             4m6s
pod/redis-pod        1/1     Running            0             6m29s
pod/result-app-pod   1/1     Running            0             11s
pod/voting-app-pod   1/1     Running            0             10m
pod/worker-app-pod   0/1     CrashLoopBackOff   3 (39s ago)   102s

NAME                     TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/db               ClusterIP   10.97.150.27    <none>        5432/TCP       3m59s
service/kubernetes       ClusterIP   10.96.0.1       <none>        443/TCP        8d
service/redis            ClusterIP   10.97.192.161   <none>        6379/TCP       6m16s
service/result-service   NodePort    10.96.131.219   <none>        80:30005/TCP   3s
service/voting-service   NodePort    10.108.10.69    <none>        80:30004/TCP   10m
```
