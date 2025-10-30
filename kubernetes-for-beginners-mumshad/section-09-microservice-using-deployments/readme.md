## Microservice Architecture using Deployments

Step 1: Ensure minikube does not have existing pods or services running

```
$ minikube stop

$ minikube delete --all=true --purge=true

$ minikube start

$ kubectl get all
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   18s

```

Step 2: Start deploying 

```
$ kubectl create -f 01-voting-app-deploy.yaml 
deployment.apps/voting-app-deploy created

$ kubectl create -f 02-voting-app-service.yaml 
service/voting-service created


$ kubectl create -f 03-redis-deploy.yaml 
deployment.apps/redis-deploy created

$ kubectl create -f 04-redis-service.yaml 
service/redis created

$ kubectl create -f 05-worker-app-deploy.yaml 
deployment.apps/worker-app-deploy created

$ kubectl create -f 06-postgres-deploy.yaml 
deployment.apps/postgres-deploy created

$ kubectl create -f 07-postgres-service.yaml 
service/db created

$ kubectl create -f 08-result-app-deploy.yaml 
deployment.apps/result-app-deploy created

$ kubectl create -f 09-result-app-service.yaml 
service/result-service created

$ kubectl get all
NAME                                     READY   STATUS              RESTARTS   AGE
pod/postgres-deploy-87d67bb5-wmtc5       0/1     ContainerCreating   0          28s
pod/redis-deploy-7bcb5c67c-8hvmh         1/1     Running             0          46s
pod/result-app-deploy-65cbdb859f-sfjsq   0/1     ContainerCreating   0          17s
pod/voting-app-deploy-7b5b46f448-f64bv   1/1     Running             0          66s
pod/worker-app-deploy-b47bf974d-lbns9    1/1     Running             0          35s

NAME                     TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
service/db               ClusterIP   10.109.7.30      <none>        5432/TCP       22s
service/kubernetes       ClusterIP   10.96.0.1        <none>        443/TCP        2m47s
service/redis            ClusterIP   10.102.101.223   <none>        6379/TCP       41s
service/result-service   NodePort    10.99.85.51      <none>        80:30005/TCP   14s
service/voting-service   NodePort    10.96.97.17      <none>        80:30004/TCP   54s

NAME                                READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/postgres-deploy     0/1     1            0           28s
deployment.apps/redis-deploy        1/1     1            1           46s
deployment.apps/result-app-deploy   0/1     1            0           17s
deployment.apps/voting-app-deploy   1/1     1            1           66s
deployment.apps/worker-app-deploy   1/1     1            1           35s

NAME                                           DESIRED   CURRENT   READY   AGE
replicaset.apps/postgres-deploy-87d67bb5       1         1         0       28s
replicaset.apps/redis-deploy-7bcb5c67c         1         1         1       46s
replicaset.apps/result-app-deploy-65cbdb859f   1         1         0       17s
replicaset.apps/voting-app-deploy-7b5b46f448   1         1         1       66s
replicaset.apps/worker-app-deploy-b47bf974d    1         1         1       35s
```

Step 3: Getting voting-app and result-app URLs

```
$ minikube service list
|-------------|----------------|--------------|-----|
|  NAMESPACE  |      NAME      | TARGET PORT  | URL |
|-------------|----------------|--------------|-----|
| default     | db             | No node port |     |
| default     | kubernetes     | No node port |     |
| default     | redis          | No node port |     |
| default     | result-service |           80 |     |
| default     | voting-service |           80 |     |
| kube-system | kube-dns       | No node port |     |
|-------------|----------------|--------------|-----|

$ minikube service voting-service --url
http://127.0.0.1:58977
❗  Because you are using a Docker driver on darwin, the terminal needs to be open to run it.

// In a separate shell window, do the next service command
$ minikube service result-service --url
http://127.0.0.1:59069
❗  Because you are using a Docker driver on darwin, the terminal needs to be open to run it.
```

Step 4: Scaing voting-app

```
$ kubectl scale deployment voting-app-deploy --replicas=3

$ kubectl get pods
NAME                                 READY   STATUS    RESTARTS   AGE
postgres-deploy-87d67bb5-wmtc5       1/1     Running   0          6m25s
redis-deploy-7bcb5c67c-8hvmh         1/1     Running   0          6m43s
result-app-deploy-65cbdb859f-sfjsq   1/1     Running   0          6m14s
voting-app-deploy-7b5b46f448-f64bv   1/1     Running   0          7m3s
voting-app-deploy-7b5b46f448-t68q2   1/1     Running   0          15s
voting-app-deploy-7b5b46f448-trrtw   1/1     Running   0          15s
worker-app-deploy-b47bf974d-lbns9    1/1     Running   0          6m32s
```