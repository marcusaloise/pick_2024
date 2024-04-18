# Replicaset e DaemonSets

### Replicaset
O principal objetivo de um ReplicaSet é manter um número estável de réplicas de um Pod rodando a qualquer momento. Em outras palavras, ele garante que um número especificado de réplicas de um pod esteja sempre em execução. Se um ou mais Pods falham, o ReplicaSet inicia novos Pods para compensar. Da mesma forma, se houver Pods em excesso, ele vai terminar os Pods extras.

- ReplicaSet é quem vai criar os Pods que estão dentro do Deployment.
- Quando criamos um deployment automaticamente estamos criando um replicaset também para gerenciar os pods no cluster
- Deployment > replicaSet > Pods

> [!IMPORTANT]
>  O replicaset so vai efetuar a mudança no pod se ele morrer, assim que ele voltar as mudanças passadas dentro do replicaset serão efetuadas.

### DeamonSet
O DaemonSet é uma característica do Kubernetes que permite aos administradores garantir que algumas ou todas as máquinas (node) no cluster Kubernetes executem uma cópia de um pod. Essencialmente, sempre que um novo nó é adicionado ao cluster, o DaemonSet automaticamente implanta uma cópia do pod escolhido para esse node, e quando um node é removido do cluster, o pod que estava rodando no node é coletado pelo garbage collector.

- Ele vai garantir que tenha uma replica do meu pod em cada Node

### Probes

As probes são uma forma de você monitorar o seu Pod e saber se ele está em um estado saudável ou não. Com elas é possível assegurar que seus Pods estão rodando e respondendo de maneira correta (Basicamente um healthcheck dos pods)

- liveness

  - O liveness verifica se a aplicação dentro do contêiner está funcionando corretamente. Caso a aplicação falhe ou pare de responder, o livenessProbe pode automaticamente reiniciar o contêiner para tentar restaurar a operação normal.

- readinessProbe

 - O readiness verifica se o seu container está pronto para receber tráfego, se ele está pronto para receber requisições vindas de fora caso da erro o bacana fica indisponivel até a requisição funcionar

- startupProbe




> Config das probes
```yml
      containers:  
      - image: nginx:1.19.1
        name: nginx-s
        resources: 
          limits:
            cpu: "0.9"
            memory: "256Mi"
          requests:
            cpu: "0.4"
            memory: "64Mi"      
        livenessProbe:
          tcpSocket:
            port: 80   
          initialDelaySeconds: 10
          periodSeconds: 10
          timeoutSeconds: 5
          failureThreshold: 3
        readinessProbe:
          exec:
            command:
            - curl
            - -f
            - http://localhost:80    
          initialDelaySeconds: 10
          periodSeconds: 10
          timeoutSeconds: 5
          failureThreshold: 3
        startupProbe:
          tcpSocket:
            port: 80
          initialDelaySeconds: 10
          periodSeconds: 10
          timeoutSeconds: 5
          failureThreshold: 3
```
> Describe do Pod/deployment sobre o liveness
```
    Liveness:     tcp-socket :80 delay=10s timeout=5s period=10s #success=1 #failure=3
    Readiness:    exec [curl -f http://localhost:80] delay=10s timeout=5s period=10s #success=1 #failure=3
    Startup:      tcp-socket :80 delay=10s timeout=5s period=10s #success=1 #failure=3

```

--------------

## ReplicaSet:

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


## DeamonSet:

```yml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  labels:
    app: nginx-exporter-daemonset
  name: nginx-exporter-daemonset
spec:
  selector:
    matchLabels:
      app: nginx-exporter-daemonset
  template:
    metadata:
      labels:
        app: nginx-exporter-daemonset
    spec:
      hostNetwork: true
      containers:
      - name: node-exporte
        image: prom/node-exporter:latest
        ports:
        - containerPort: 9100
          hostPort: 9100
        volumeMounts:
        - name: proc
          mountPath: /host/proc
          readOnly: true
        - name: sys
          mountPath: /host/sys
          readOnly: true
      volumes:
      - name: proc
        hostPath:
          path: /proc
      - name: sys
        hostPath:
          path: /sys
```

> **Describe:**

```
root@elliot:/home/elliot/k8s# k describe daemonset
Name:           node-exporter-daemonset
Selector:       app=node-exporter-daemonset
Node-Selector:  <none>
Labels:         app=node-exporter-daemonset
Annotations:    deprecated.daemonset.template.generation: 1
Desired Number of Nodes Scheduled: 2
Current Number of Nodes Scheduled: 2
Number of Nodes Scheduled with Up-to-date Pods: 2
Number of Nodes Scheduled with Available Pods: 2
Number of Nodes Misscheduled: 0
Pods Status:  2 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=node-exporter-daemonset
  Containers:
   node-exporte:
    Image:        prom/node-exporter:latest
    Port:         9100/TCP
    Host Port:    9100/TCP
    Environment:  <none>
    Mounts:
      /host/proc from proc (ro)
      /host/sys from sys (ro)
  Volumes:
   proc:
    Type:          HostPath (bare host directory volume)
    Path:          /proc
    HostPathType:  
   sys:
    Type:          HostPath (bare host directory volume)
    Path:          /sys
    HostPathType:  
Events:
  Type    Reason            Age   From                  Message
  ----    ------            ----  ----                  -------
  Normal  SuccessfulCreate  20s   daemonset-controller  Created pod: node-exporter-daemonset-p89m8
  Normal  SuccessfulCreate  20s   daemonset-controller  Created pod: node-exporter-daemonset-99zbg

```

> Demonstrativo dos pods em cada node

```
root@elliot:/home/elliot# k get pods -o wide
NAME                            READY   STATUS    RESTARTS   AGE   IP           NODE               NOMINATED NODE   READINESS GATES
node-exporter-daemonset-99zbg   1/1     Running   0          25m   172.18.0.3   giropops-worker2   <none>           <none>
node-exporter-daemonset-p89m8   1/1     Running   0          25m   172.18.0.2   giropops-worker    <none>           <none>

```

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx-deployment
    fruta: banana-da-terra
  name: nginx-deployment
  namespace: giropops
spec:
  replicas: 5
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
      - image: nginx:1.13.0
        name: nginx-s
        resources: 
          limits:
            cpu: "0.9"
            memory: "256Mi"
          requests:
            cpu: "0.4"
            memory: "64Mi"      
        livenessProbe:
          tcpSocket:
            port: 80   
          initialDelaySeconds: 10
          periodSeconds: 10
          timeoutSeconds: 5
          failureThreshold: 3
```