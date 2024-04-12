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

```
> Output from ***kubectl describe pods pod-name***
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

> Output from ***kubectl describe pods pod-name -o yaml***

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