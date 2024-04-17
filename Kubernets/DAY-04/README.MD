# Replicaset e DaemonSets

### Replicaset
O principal objetivo de um ReplicaSet é manter um número estável de réplicas de um Pod rodando a qualquer momento. Em outras palavras, ele garante que um número especificado de réplicas de um pod esteja sempre em execução. Se um ou mais Pods falham, o ReplicaSet inicia novos Pods para compensar. Da mesma forma, se houver Pods em excesso, ele vai terminar os Pods extras.

- ReplicaSet é quem vai criar os Pods que estão dentro do Deployment.
- Quando criamos um deployment automaticamente estamos criando um replicaset também para gerenciar os pods no cluster
- Deployment > replicaSet > Pods

> [!IMPORTANT]
>  O replicaset do vai efetuar a mudança no pod se ele morrer, assim que ele voltar as mudanças passadas dentro do replicaset serão efetuadas.

### DeamonSet
O DaemonSet é uma característica do Kubernetes que permite aos administradores garantir que algumas ou todas as máquinas (nós) no cluster Kubernetes executem uma cópia de um pod. Essencialmente, sempre que um novo nó é adicionado ao cluster, o DaemonSet automaticamente implanta uma cópia do pod escolhido para esse nó, e quando um nó é removido do cluster, o pod que estava rodando no nó é coletado pelo garbage collector.


> Segue abaixo o replicaset criado pelo deployment da ultima aula:

```
Name:           nginx-deployment-7855685fd7
Namespace:      giropops
Selector:       app=nginx-deployment,pod-template-hash=7855685fd7
Labels:         app=nginx-deployment
                pod-template-hash=7855685fd7
Annotations:    deployment.kubernetes.io/desired-replicas: 5
                deployment.kubernetes.io/max-replicas: 6
                deployment.kubernetes.io/revision: 1
Controlled By:  Deployment/nginx-deployment
Replicas:       5 current / 5 desired
Pods Status:    5 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=nginx-deployment
           pod-template-hash=7855685fd7
  Containers:
   nginx-s:
    Image:      nginx:1.13.0
    Port:       <none>
    Host Port:  <none>
    Limits:
      cpu:     900m
      memory:  256Mi
    Requests:
      cpu:        400m
      memory:     64Mi
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Events:
  Type    Reason            Age   From                   Message
  ----    ------            ----  ----                   -------
  Normal  SuccessfulCreate  78s   replicaset-controller  Created pod: nginx-deployment-7855685fd7-5twmc
  Normal  SuccessfulCreate  78s   replicaset-controller  Created pod: nginx-deployment-7855685fd7-gflb2
  Normal  SuccessfulCreate  78s   replicaset-controller  Created pod: nginx-deployment-7855685fd7-cw7c5
  Normal  SuccessfulCreate  77s   replicaset-controller  Created pod: nginx-deployment-7855685fd7-7pmwh
  Normal  SuccessfulCreate  77s   replicaset-controller  Created pod: nginx-deployment-7855685fd7-5cm96

```

> **Nosso primeiro replicaset**
```yml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
   labels:
      app: nginx-server
   name: nginx-replicaset
spec:
  replicas: 3
  selector:
     matchLabels:
       app: nginx-server
  template:
    metadata:
      labels:
        app: nginx-server
    spec:
      containers:
      - name: nginx
        image: nginx:1.19.1
        resources:
          limits:
            cpu: 0.5
            memory: 256Mi
          requests:
            cpu: 0.3
            memory: 128Mi


```

> **Describe do nosso replicaset**

```
Name:         nginx-replicaset
Namespace:    default
Selector:     app=nginx-server
Labels:       app=nginx-server
Annotations:  <none>
Replicas:     3 current / 3 desired
Pods Status:  0 Running / 3 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=nginx-server
  Containers:
   nginx:
    Image:      nginx:1.19.1
    Port:       <none>
    Host Port:  <none>
    Limits:
      cpu:     500m
      memory:  256Mi
    Requests:
      cpu:        300m
      memory:     128Mi
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Events:
  Type    Reason            Age   From                   Message
  ----    ------            ----  ----                   -------
  Normal  SuccessfulCreate  14s   replicaset-controller  Created pod: nginx-replicaset-5dwbj
  Normal  SuccessfulCreate  13s   replicaset-controller  Created pod: nginx-replicaset-zxmd4
  Normal  SuccessfulCreate  13s   replicaset-controller  Created pod: nginx-replicaset-m59nr

```