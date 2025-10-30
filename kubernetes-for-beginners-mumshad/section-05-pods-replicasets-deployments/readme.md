#### Creating POD using pod.xml file

```
$ kubectl apply -f pod.xml
```

####  Replication-Controller vs ReplicaSet
- Replication-controller is older way to create replications which is replaced by ReplicaSet.
- Replication-controller is supported in apiVersion `v1`. 
- ReplicaSet is supported in apiVersion `apps/v1`

####  Command for Replication-Controller: 

```
$ kubectl create -f replication-controller.yaml
replicationcontroller/myapp-rc created

$ kubectl get replicationcontroller
NAME       DESIRED   CURRENT   READY   AGE
myapp-rc   3         3         3       93s

$ kubectl get pods
NAME             READY   STATUS    RESTARTS   AGE
myapp-rc-n2xnz   1/1     Running   0          2m40s
myapp-rc-rhsj8   1/1     Running   0          2m40s
myapp-rc-zw9g8   1/1     Running   0          2m40s

$ kubectl delete replicationcontroller myapp-rc
replicationcontroller "myapp-rc" deleted
```

####  Command for ReplicaSet

```
$ kubectl create -f replicaset-definition.yaml 
replicaset.apps/myapp-replicaset created

$ kubectl get replicaset
NAME               DESIRED   CURRENT   READY   AGE
myapp-replicaset   3         3         3       12s

//short form for accessing replicaset
$ kubectl get rs

$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
myapp-replicaset-f9dd9   1/1     Running   0          16s
myapp-replicaset-nnks4   1/1     Running   0          16s
myapp-replicaset-zml6c   1/1     Running   0          16s

//command to scale-up or scale-down pods
$ kubectl scale replicaset myapp-replicaset --replicas=4

$ kubectl delete replicaset myapp-replicaset
replicaset.apps "myapp-replicaset" deleted
```

#### Deployments:

```
$ kubectl create -f deployment.yaml
deployment.apps/myapp-deployment created

# kubectl get all
NAME                                    READY   STATUS    RESTARTS   AGE
pod/myapp-deployment-597667964f-b9fxh   1/1     Running   0          5s
pod/myapp-deployment-597667964f-jg5rg   1/1     Running   0          5s
pod/myapp-deployment-597667964f-srtp8   1/1     Running   0          5s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   23h

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/myapp-deployment   3/3     3            3           5s

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/myapp-deployment-597667964f   3         3         3       5s


$ kubectl describe deployment myapp-deployment
Name:                   myapp-deployment
Namespace:              default
CreationTimestamp:      Thu, 05 Dec 2024 22:54:08 -0600
Labels:                 app=nginx
                        tier=frontend
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=myapp
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=myapp
  Containers:
   nginx:
    Image:         nginx
    Port:          <none>
    Host Port:     <none>
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   myapp-deployment-597667964f (3/3 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  86s   deployment-controller  Scaled up replica set myapp-deployment-597667964f to 3
```

#### Deployment Updates: 

```
$ kubectl create -f deployment.yaml
deployment.apps/myapp-deployment created

# this command needs to executed immediately after create command to see the replicas getting created. 
$ kubectl rollout status deployment.apps/myapp-deployment
Waiting for deployment "myapp-deployment" rollout to finish: 0 of 6 updated replicas are available...
Waiting for deployment "myapp-deployment" rollout to finish: 1 of 6 updated replicas are available...
Waiting for deployment "myapp-deployment" rollout to finish: 2 of 6 updated replicas are available...
Waiting for deployment "myapp-deployment" rollout to finish: 3 of 6 updated replicas are available...
Waiting for deployment "myapp-deployment" rollout to finish: 4 of 6 updated replicas are available...
Waiting for deployment "myapp-deployment" rollout to finish: 5 of 6 updated replicas are available...
deployment "myapp-deployment" successfully rolled out

// cause-of-change is not recorded. 
$ kubectl rollout history deployment.apps/myapp-deployment
deployment.apps/myapp-deployment 
REVISION  CHANGE-CAUSE
1         <none>

// re-create deployment with cause-of-change
$ kubectl delete deployment myapp-deployment

$ kubectl create -f deployment.yaml --record

$ kubectl rollout history deployment.apps/myapp-deployment
deployment.apps/myapp-deployment 
REVISION  CHANGE-CAUSE
1         kubectl create --filename=deployment.yaml --record=true

// edit deployment to see another cause-of-change
$ kubectl edit deployment myapp-deployment --record
// edit nginx version to 1.18 using vim

$ kubectl rollout history deployment.apps/myapp-deployment
deployment.apps/myapp-deployment 
REVISION  CHANGE-CAUSE
1         kubectl create --filename=deployment.yaml --record=true
2         kubectl edit deployment myapp-deployment --record=true

//another way to update the deployment
$ kubectl set image deployment myapp-deployment nginx=nginx:1.18-perl --record

$ kubectl rollout history deployment.apps/myapp-deployment
deployment.apps/myapp-deployment 
REVISION  CHANGE-CAUSE
1         kubectl create --filename=deployment.yaml --record=true
2         kubectl edit deployment myapp-deployment --record=true
3         kubectl set image deployment myapp-deployment nginx=nginx:1.18-perl --record=true
```

#### Deployment Rollbacks:

```
$ kubectl rollout undo deployment/myapp-deployment
deployment.apps/myapp-deployment rolled back

$ kubectl rollout history deployment.apps/myapp-deployment
deployment.apps/myapp-deployment 
REVISION  CHANGE-CAUSE
1         kubectl create --filename=deployment.yaml --record=true
3         kubectl set image deployment myapp-deployment nginx=nginx:1.18-perl --record=true
4         kubectl edit deployment myapp-deployment --record=true
```

