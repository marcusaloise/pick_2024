# Deployment

### O que é um deployment:

O ***deployment*** e um recurso que gerencia o processo de implantação de aplicações em contêineres no cluster. Ele proporciona atualizações declarativas para aplicações e permite que você descreva o estado desejado de uma aplicação, deixando que o Kubernetes execute as mudanças necessárias para alcançar esse estado

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