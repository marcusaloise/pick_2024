# Deployment

### O que é um deployment:

O ***deployment*** e um recurso que gerencia o processo de implantação de aplicações em contêineres no cluster. Ele proporciona atualizações declarativas para aplicações e permite que você descreva o estado desejado de uma aplicação, deixando que o Kubernetes execute as mudanças necessárias para alcançar esse estado

Durante o treinamento e mostrado como e realizado o deployment de novos pods dentro do node usando o arquivo de deployment.yml, abaixo segue alguns modelos usado em aula para pratica!


para realizar o rollout de uma versão especifica utilize o seguinte comando:

```
kubectl rollout undo deployment -n giropops nginx-deployment --to-revision 2
```

para realizar o scale de pods da sua aplicação utilize o seguinte comando:

```
Kubectl scale deployment -n namespace --replicas n(9) deployment-name
```



Durante uma mudança de pods no node e possivel acompanhar utilizando o seguinte comando:
```
kubectl rollout status deployment -n namespace deployment-name
```

Podemos realizar um rollout caso de algum problema no nosso cluster usando o seguinte comando: 

```
kubectl rollout undo deployment -n namespace deployment-name
```

kubectl rollout history deployment -n giropops nginx-deployment
```
deployment.apps/nginx-deployment 
REVISION  CHANGE-CAUSE
4         <none>
6         <none>
8         <none>
9         <none>

```
kubectl rollout history deployment -n giropops nginx-deployment --revision 6

```
deployment.apps/nginx-deployment with revision #6
Pod Template:
  Labels:	app=nginx-deployment
	pod-template-hash=9dd64d678
  Containers:
   nginx-s:
    Image:	nginx:1.13.0
    Port:	<none>
    Host Port:	<none>
    Limits:
      cpu:	900m
      memory:	256Mi
    Requests:
      cpu:	400m
      memory:	64Mi
    Environment:	<none>
    Mounts:	<none>
   nginx-server:
    Image:	nginx:1.15.0
    Port:	<none>
    Host Port:	<none>
    Limits:
      cpu:	500m
      memory:	256Mi
    Requests:
      cpu:	300m
      memory:	64Mi
    Environment:	<none>
    Mounts:	<none>
  Volumes:	<none>

```


> ***Nosso primeiro deployment:***

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx-deployment
  name: nginx-deployment
spec:
  replicas: 3
  selector:
     matchLabels:
       app: nginx-deployment  

  strategy: {} 
  template:
    metadata:
      labels:
        app: nginx-deployment  
    spec:
      containers:  
      - image: nginx
        name: nginx-server
        resources: 
          limits:
            cpu: "0.5"
            memory: "256Mi"
          requests:
            cpu: "0.3"
            memory: "64Mi"      

```

```Name:                   nginx-deployment
Namespace:              default
CreationTimestamp:      Mon, 15 Apr 2024 01:28:38 -0300
Labels:                 app=nginx-deployment
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=nginx-deployment
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx-deployment
  Containers:
   nginx-server:
    Image:      nginx
    Port:       <none>
    Host Port:  <none>
    Limits:
      cpu:     500m
      memory:  256Mi
    Requests:
      cpu:        300m
      memory:     64Mi
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   nginx-deployment-765c9f7d9d (3/3 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  46m   deployment-controller  Scaled up replica set nginx-deployment-765c9f7d9d to 3

```

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"labels":{"app":"nginx-deployment"},"name":"nginx-deployment","namespace":"default"},"spec":{"replicas":3,"selector":{"matchLabels":{"app":"nginx-deployment"}},"strategy":{},"template":{"metadata":{"labels":{"app":"nginx-deployment"}},"spec":{"containers":[{"image":"nginx","name":"nginx-server","resources":{"limits":{"cpu":"0.5","memory":"256Mi"},"requests":{"cpu":"0.3","memory":"64Mi"}}}]}}}}
  creationTimestamp: "2024-04-15T04:28:38Z"
  generation: 1
  labels:
    app: nginx-deployment
  name: nginx-deployment
  namespace: default
  resourceVersion: "90324"
  uid: c0981fca-4743-498e-a481-a016bb49e6c7
spec:
  progressDeadlineSeconds: 600
  replicas: 3
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: nginx-deployment
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: nginx-deployment
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx-server
        resources:
          limits:
            cpu: 500m
            memory: 256Mi
          requests:
            cpu: 300m
            memory: 64Mi
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status:
  availableReplicas: 3
  conditions:
  - lastTransitionTime: "2024-04-15T04:29:04Z"
    lastUpdateTime: "2024-04-15T04:29:04Z"
    message: Deployment has minimum availability.
    reason: MinimumReplicasAvailable
    status: "True"
    type: Available
  - lastTransitionTime: "2024-04-15T04:28:39Z"
    lastUpdateTime: "2024-04-15T04:29:04Z"
    message: ReplicaSet "nginx-deployment-765c9f7d9d" has successfully progressed.
    reason: NewReplicaSetAvailable
    status: "True"
    type: Progressing
  observedGeneration: 1
  readyReplicas: 3
  replicas: 3
  updatedReplicas: 3

```

> ***Criando o nosso namespace***

```yml
apiVersion: v1
kind: Namespace
metadata:
  name: giropops
spec: {}
```

> ***Atualizando o nosso ns e deployando varios nginx***

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx-deployment
  name: nginx-deployment
  namespace: giropops
spec:
  replicas: 10
  selector:
     matchLabels:
       app: nginx-deployment  

  strategy: {} 
  template:
    metadata:
      labels:
        app: nginx-deployment  
    spec:
      containers:  
      - image: nginx:1.16.0
        name: nginx-server
        resources: 
          limits:
            cpu: "0.5"
            memory: "256Mi"
          requests:
            cpu: "0.3"
            memory: "64Mi"      


```
```
root@elliot:/home/elliot/k8s# k get deployment -n giropops 
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   10/10   10           10          12m
root@elliot:/home/elliot/k8s# k describe deployment -n giropops 
Name:                   nginx-deployment
Namespace:              giropops
CreationTimestamp:      Mon, 15 Apr 2024 22:44:34 -0300
Labels:                 app=nginx-deployment
Annotations:            deployment.kubernetes.io/revision: 2
Selector:               app=nginx-deployment
Replicas:               10 desired | 10 updated | 10 total | 10 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx-deployment
  Containers:
   nginx-server:
    Image:      nginx:1.16.0
    Port:       <none>
    Host Port:  <none>
    Limits:
      cpu:     500m
      memory:  256Mi
    Requests:
      cpu:        300m
      memory:     64Mi
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  nginx-deployment-879847b77 (0/0 replicas created)
NewReplicaSet:   nginx-deployment-5cd69b976d (10/10 replicas created)
Events:
  Type    Reason             Age                   From                   Message
  ----    ------             ----                  ----                   -------
  Normal  ScalingReplicaSet  13m                   deployment-controller  Scaled up replica set nginx-deployment-879847b77 to 10
  Normal  ScalingReplicaSet  3m41s                 deployment-controller  Scaled up replica set nginx-deployment-5cd69b976d to 3
  Normal  ScalingReplicaSet  3m40s                 deployment-controller  Scaled down replica set nginx-deployment-879847b77 to 8 from 10
  Normal  ScalingReplicaSet  3m39s                 deployment-controller  Scaled up replica set nginx-deployment-5cd69b976d to 5 from 3
  Normal  ScalingReplicaSet  2m55s                 deployment-controller  Scaled down replica set nginx-deployment-879847b77 to 7 from 8
  Normal  ScalingReplicaSet  2m53s                 deployment-controller  Scaled up replica set nginx-deployment-5cd69b976d to 6 from 5
  Normal  ScalingReplicaSet  2m47s                 deployment-controller  Scaled down replica set nginx-deployment-879847b77 to 6 from 7
  Normal  ScalingReplicaSet  2m41s                 deployment-controller  Scaled up replica set nginx-deployment-5cd69b976d to 7 from 6
  Normal  ScalingReplicaSet  2m39s                 deployment-controller  Scaled down replica set nginx-deployment-879847b77 to 5 from 6
  Normal  ScalingReplicaSet  2m9s (x8 over 2m37s)  deployment-controller  (combined from similar events): Scaled down replica set nginx-deployment-879847b77 to 0 from 1

```

> ***Utilizando RollingUpdate no nosso deployment (pode ser usado o recriate)***

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx-deployment
  name: nginx-deployment
  namespace: giropops
spec:
  replicas: 10
  selector:
     matchLabels:
       app: nginx-deployment  

  strategy: 
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1      
  template:
    metadata:
      labels:
        app: nginx-deployment  
    spec:
      containers:  
      - image: nginx:1.15.0
        name: nginx-server
        resources: 
          limits:
            cpu: "0.5"
            memory: "256Mi"
          requests:
            cpu: "0.3"
            memory: "64Mi"      


```

```
RollingUpdateStrategy:  1 max unavailable, 1 max surge
```