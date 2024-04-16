# Pod:

### O que é um Pod:

Um ***Pod*** e um agrupamento de container que compartilha dos mesmos recursos (namespace/cgroup) e a menor unidade de processamento de um cluster.

E normal ter mais de um container dentro de um ***Pod*** para ter mais flexibilidade da aplicação.

- Comunicação entre ***pods*** e via IP
- Comunicação entre container dentro do ***Pod*** e local


### Principais comandos: 

```bash

kubectl get pods # get pods from the current namespace
kubectl get pods -A # get pods from all namespaces
kubectl describe pods # Describe Pod
kubectl delete pod # Apagar um pod
kubectl run pod --image alpine # criar um pod com um container com imagem x dentro
kubectl attach pod-name container-name -ti # entrar no pod que esta em execução
kubectl exec pod-name/container-name -- bash # executa um comando no nosso caso e o bash
kubectl run girus --image alpine --dry-run=client -o yaml > pod.yaml


```
> ***Output from: kubectl describe pods pod-name***
```
Name:             nginx-giropops
Namespace:        default
Priority:         0
Service Account:  default
Node:             giropops-worker/172.18.0.3
Start Time:       Thu, 11 Apr 2024 15:45:47 -0300
Labels:           app=giropops-strigus
                  run=nginx-giropops
Annotations:      <none>
Status:           Running
IP:               10.244.1.3
IPs:
  IP:  10.244.1.3
Containers:
  nginx-giropops:
    Container ID:   containerd://feb1faa746d2f0b344bf710c7ef6867ccd9ed193319ee3893401f072e385fc79
    Image:          nginx
    Image ID:       docker.io/library/nginx@sha256:391f9f4a9a3b27eb5ad6b5d8aae0f1678d75cba98281a3c602fc8251fef014f3
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Thu, 11 Apr 2024 15:46:01 -0300
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-2pnpf (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  kube-api-access-2pnpf:
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
  Normal  Scheduled  4m51s  default-scheduler  Successfully assigned default/nginx-giropops to giropops-worker
  Normal  Pulling    4m47s  kubelet            Pulling image "nginx"
  Normal  Pulled     4m40s  kubelet            Successfully pulled image "nginx" in 6.648s (6.648s including waiting)
  Normal  Created    4m37s  kubelet            Created container nginx-giropops
  Normal  Started    4m37s  kubelet            Started container nginx-giropops


```

> ***Output from: kubectl describe pods pod-name -o yaml***

```yml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2024-04-11T18:45:47Z"
  labels:
    app: giropops-strigus
    run: nginx-giropops
  name: nginx-giropops
  namespace: default
  resourceVersion: "33269"
  uid: 159aa21c-249a-4998-ac5d-15b22824ff80
spec:
  containers:
  - image: nginx
    imagePullPolicy: Always
    name: nginx-giropops
    ports:
    - containerPort: 80
      protocol: TCP
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-2pnpf
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  nodeName: giropops-worker
  preemptionPolicy: PreemptLowerPriority
  priority: 0
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext: {}
  serviceAccount: default
  serviceAccountName: default
  terminationGracePeriodSeconds: 30
  tolerations:
  - effect: NoExecute
    key: node.kubernetes.io/not-ready
    operator: Exists
    tolerationSeconds: 300
  - effect: NoExecute
    key: node.kubernetes.io/unreachable
    operator: Exists
    tolerationSeconds: 300
  volumes:
  - name: kube-api-access-2pnpf
    projected:
      defaultMode: 420
      sources:
      - serviceAccountToken:
          expirationSeconds: 3607
          path: token
      - configMap:
          items:
          - key: ca.crt
            path: ca.crt
          name: kube-root-ca.crt
      - downwardAPI:
          items:
          - fieldRef:
              apiVersion: v1
              fieldPath: metadata.namespace
            path: namespace
status:
  conditions:
  - lastProbeTime: null
    lastTransitionTime: "2024-04-11T18:46:01Z"
    status: "True"
    type: PodReadyToStartContainers
  - lastProbeTime: null
    lastTransitionTime: "2024-04-11T18:45:47Z"
    status: "True"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2024-04-11T18:46:01Z"
    status: "True"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2024-04-11T18:46:01Z"
    status: "True"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2024-04-11T18:45:47Z"
    status: "True"
    type: PodScheduled
  containerStatuses:
  - containerID: containerd://feb1faa746d2f0b344bf710c7ef6867ccd9ed193319ee3893401f072e385fc79
    image: docker.io/library/nginx:latest
    imageID: docker.io/library/nginx@sha256:391f9f4a9a3b27eb5ad6b5d8aae0f1678d75cba98281a3c602fc8251fef014f3
    lastState: {}
    name: nginx-giropops
    ready: true
    restartCount: 0
    started: true
    state:
      running:
        startedAt: "2024-04-11T18:46:01Z"
  hostIP: 172.18.0.3
  hostIPs:
  - ip: 172.18.0.3
  phase: Running
  podIP: 10.244.1.3
  podIPs:
  - ip: 10.244.1.3
  qosClass: BestEffort
  startTime: "2024-04-11T18:45:47Z"

```

> ***kubectl get pods pod_name -o yaml***
```yml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2024-04-14T18:16:15Z"
  labels:
    run: strigus
  name: strigus
  namespace: default
  resourceVersion: "37733"
  uid: 93848b5d-490e-49b1-9e8a-8ad9ce56d5d6
spec:
  containers:
  - image: nginx
    imagePullPolicy: Always
    name: strigus
    ports:
    - containerPort: 80
      protocol: TCP
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-6nt8f
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  nodeName: giropops-worker
  preemptionPolicy: PreemptLowerPriority
  priority: 0
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext: {}
  serviceAccount: default
  serviceAccountName: default
  terminationGracePeriodSeconds: 30
  tolerations:
  - effect: NoExecute
    key: node.kubernetes.io/not-ready
    operator: Exists
    tolerationSeconds: 300
  - effect: NoExecute
    key: node.kubernetes.io/unreachable
    operator: Exists
    tolerationSeconds: 300
  volumes:
  - name: kube-api-access-6nt8f
    projected:
      defaultMode: 420
      sources:
      - serviceAccountToken:
          expirationSeconds: 3607
          path: token
      - configMap:
          items:
          - key: ca.crt
            path: ca.crt
          name: kube-root-ca.crt
      - downwardAPI:
          items:
          - fieldRef:
              apiVersion: v1
              fieldPath: metadata.namespace
            path: namespace
status:
  conditions:
  - lastProbeTime: null
    lastTransitionTime: "2024-04-14T18:16:27Z"
    status: "True"
    type: PodReadyToStartContainers
  - lastProbeTime: null
    lastTransitionTime: "2024-04-14T18:16:16Z"
    status: "True"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2024-04-14T18:16:27Z"
    status: "True"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2024-04-14T18:16:27Z"
    status: "True"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2024-04-14T18:16:15Z"
    status: "True"
    type: PodScheduled
  containerStatuses:
  - containerID: containerd://617895fbd3f28486760dd2aae29146dc3000a59a3486dbb7bfa5aed96db65dc9
    image: docker.io/library/nginx:latest
    imageID: docker.io/library/nginx@sha256:391f9f4a9a3b27eb5ad6b5d8aae0f1678d75cba98281a3c602fc8251fef014f3
    lastState: {}
    name: strigus
    ready: true
    restartCount: 0
    started: true
    state:
      running:
        startedAt: "2024-04-14T18:16:26Z"
  hostIP: 172.18.0.4
  hostIPs:
  - ip: 172.18.0.4
  phase: Running
  podIP: 10.244.1.3
  podIPs:
  - ip: 10.244.1.3
  qosClass: BestEffort
  startTime: "2024-04-14T18:16:16Z"

```
> ***kubectl run girus --image alpine --dry-run=client -o yaml > pod.yaml***

***Manifesto:***
```yml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: girus
  name: girus
spec:
  containers:
  - image: alpine
    name: girus
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}

```

> ***manifesto com multiplos container dentro de um pod***
```yml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: girus
    service: webserver
  name: girus
spec:
  containers:
  - image: nginx
    name: nginx-server
  - image: busybox
    name: busybox-server
    args: 
    - sleep
    - "600"          
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}

```

> ***Manifesto com limitação de recursos para o POD***

```yml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: girus
    service: webserver
  name: girus
spec:
  containers:
  - image: ubuntu
    name: ubuntu-server
    args:
    - sleep
    - "1800"          
    resources: 
      limits:
        cpu: "0.5"
        memory: "120Mi"
      requests:
        cpu: "0.3"
        memory: "64Mi"       
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}

```

```
    Restart Count:  6
    Limits:
      cpu:     500m
      memory:  120Mi
    Requests:
      cpu:        300m
      memory:     64Mi

```
> ***Criando volumes empty***
```yml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: girus
    service: webserver
  name: girus
spec:
  containers:
  - image: nginx
    name: nginx-server
    volumeMounts:
    - mountPath: /giropops
      name: primeiro-empydir
    resources: 
      limits:
        cpu: "0.5"
        memory: "120Mi"
      requests:
        cpu: "0.3"
        memory: "64Mi"       
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  volumes:
  - name: primeiro-empydir
    emptyDir:
      sizeLimit: 256Mi       


```

```

Mounts:
  /giropops from primeiro-empydir (rw)
  /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-sbb9l (ro)


--------------
Volumes:
  primeiro-empydir:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:     
    SizeLimit:  256Mi
```

> ***Desafio:***

```yml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx-giropops
    app: giropops-strigus
  name: nginx-giropops
spec:
  containers:
  - image: nginx
    name: nginx-giropops
    ports:
    - containerPort: 80
    resources:
      limits:
        memory: "256Mi"
        cpu: "0.5"
    requests:
        memory: "64Mi"
        cpu: "0.3"
  dnsPolicy: ClusterFirst
  restartPolicy: Always
```