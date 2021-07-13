#### Deployment MongoDB and MongoDB-Express using kubernetes

- The order in which the configuration files are applied, does matter. 

- Mongo database secrete inside mongodb-secret.yaml. This should be applied first, so that it's username/password can be referenced from other configuration files.

- Mongo database configuration inside mongodb-deployment.yaml

Kubernets Secret:

    $ echo -n 'username' | base64
    dXNlcm5hbWU=
    $ echo -n 'password' | base64
    cGFzc3dvcmQ=

    $ kubectl apply -f mongodb-secret.yaml
    secret/mongodb-secret created

    $ kubectl get secret 
    NAME                  TYPE                                  DATA   AGE
    default-token-99cps   kubernetes.io/service-account-token   3      2d1h
    mongodb-secret        Opaque                                2      3m16s

Mongo Deployment Commands:

    $ kubectl apply -f mongodb-deployment.yaml
    deployment.apps/mongodb-deployment created

    $ kubectl get pods
    NAME                                 READY   STATUS              RESTARTS   AGE
    mongodb-deployment-8f6675bc5-4b745   0/1     ContainerCreating   0          96s

    //command to get the info on pod
    $ kubectl describe pod mongodb-deployment-8f6675bc5-4b745

    $ kubectl get all
    NAME                                     READY   STATUS    RESTARTS   AGE
    pod/mongodb-deployment-8f6675bc5-4b745   1/1     Running   0          5m43s
    NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
    service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   2d1h
    NAME                                 READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/mongodb-deployment   1/1     1            1           5m43s
    NAME                                           DESIRED   CURRENT   READY   AGE
    replicaset.apps/mongodb-deployment-8f6675bc5   1         1         1       5m43s

MongoDB Service Commands:

- Service and Database can be defined in the same deployment file i.e. mongodb-deployment.yaml

    $ kubectl apply -f mongodb-deployment.yaml

    $ kubectl get service
    NAME              TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)     AGE
    kubernetes        ClusterIP   10.96.0.1        <none>        443/TCP     2d1h
    mongodb-service   ClusterIP   10.105.196.133   <none>        27017/TCP   32s

    $ kubectl describe service mongodb-service
    Name:              mongodb-service  
    Namespace:         default  
    Labels:            <none>  
    Annotations:       kubectl.kubernetes.io/last-applied-configuration:
                        {"apiVersion":"v1","kind":"Service","metadata":{"annotations":{},"name":"mongodb-service","namespace":"default"},"spec":{"ports":[{"port":...  
    Selector:          app=mongodb  
    Type:              ClusterIP  
    IP:                10.105.196.133  
    Port:              <unset>  27017/TCP  
    TargetPort:        27017/TCP  
    Endpoints:         172.17.0.2:27017  
    Session Affinity:  None  
    Events:            <none>  

    #Note that the end of the above service points to mongodb-pod  
    $ kubectl get pod -o wide  
    NAME                                 READY   STATUS    RESTARTS   AGE   IP           NODE       NOMINATED NODE   READINESS GATES
    mongodb-deployment-8f6675bc5-4b745   1/1     Running   0          16m   172.17.0.2   minikube   <none>           <none>  

Deploying Service and ConfigMap

    $ kubectl apply -f mongo-configmap.yaml
    configmap/mongodb-configmap created

    $ kubectl apply -f mongo-express-deployment.yaml 
    deployment.apps/mongo-express created

    $ kubectl get pod
    NAME                                 READY   STATUS              RESTARTS   AGE
    mongo-express-58457f6b75-47m8d       0/1     ContainerCreating   0          30s
    mongodb-deployment-8f6675bc5-4b745   1/1     Running             0          47h

    //creating external service in the same deployment file
    $ kubectl apply -f mongo-express-deployment.yaml 
    deployment.apps/mongo-express configured
    service/mongo-express-service created

    //ClusterIP is internal-service and LoadBalancer is external service
    $ kubectl get service
    NAME                    TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
    kubernetes              ClusterIP      10.96.0.1        <none>        443/TCP          4d1h
    mongo-express-service   LoadBalancer   10.107.250.124   <pending>     8081:30000/TCP   63s
    mongodb-service         ClusterIP      10.105.196.133   <none>        27017/TCP        47h

    //accessing mongo-express app.
    $ minikube service mongo-express-service
    |-----------|-----------------------|-------------|---------------------------|
    | NAMESPACE |         NAME          | TARGET PORT |            URL            |
    |-----------|-----------------------|-------------|---------------------------|
    | default   | mongo-express-service |        8081 | http://192.168.49.2:30000 |
    |-----------|-----------------------|-------------|---------------------------|
    🏃  Starting tunnel for service mongo-express-service.
    |-----------|-----------------------|-------------|------------------------|
    | NAMESPACE |         NAME          | TARGET PORT |          URL           |
    |-----------|-----------------------|-------------|------------------------|
    | default   | mongo-express-service |             | http://127.0.0.1:56640 |
    |-----------|-----------------------|-------------|------------------------|
    🎉  Opening service default/mongo-express-service in default browser...
    ❗  Because you are using a Docker driver on darwin, the terminal needs to be open to run it.
