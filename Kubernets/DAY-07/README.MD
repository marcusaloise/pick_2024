# Dia 07 - Statefulset e Services

## O que é Statefulset?

Os StatefulSets são uma funcionalidade do Kubernetes que gerencia o deployment e o scaling de um conjunto de Pods, fornecendo garantias sobre a ordem de deployment e a singularidade desses Pods.

Diferente dos Deployments e Replicasets que são considerados stateless (sem estado), os StatefulSets são utilizados quando você precisa de mais garantias sobre o deployment e scaling. Eles garantem que os nomes e endereços dos Pods sejam consistentes e estáveis ao longo do tempo.

## Quando usar StatefulSets?

Os StatefulSets são úteis para aplicações que necessitam de um ou mais dos seguintes:

 - Identidade de rede estável e única.
 - Armazenamento persistente estável.
 - Ordem de deployment e scaling garantida.
 - Ordem de rolling updates e rollbacks garantida.
 - Algumas aplicações que se encaixam nesses requisitos são bancos de dados, sistemas de filas e quaisquer aplicativos que necessitam de persistência de dados ou identidade de rede estável.


> Statefull file
```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: nginx
spec:
  serviceName: "nginx"
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
       labels:
          app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.22.1
        ports: 
        - containerPort: 80
          name: http
        volumeMounts:
        - name: nginx-pv
          mountPath: /usr/share/nginx/html  
  volumeClaimTemplates:
  - metadata:
      name: nginx-pv
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 1Gi     
```

```
root@elliot:/home/elliot/k8s# k get pods
NAME      READY   STATUS    RESTARTS      AGE
nginx-0   1/1     Running   1 (30m ago)   4d23h
nginx-1   1/1     Running   1 (30m ago)   4d23h
nginx-2   1/1     Running   1 (30m ago)   4d23h

```





##  Vamos criar o Headless Service para que possamos acessar os Pods individualmente.

> Headless Service file
```yaml
apiVersion: v1
kind: Service
metadata: 
  name: nginx
  labels:
    app: nginx
spec:
  ports:
  - port: 80
    name: http
  clusterIP: None
  selector: 
    app: nginx
```

```
root@elliot:/home/elliot/k8s# k get svc
NAME             TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes       ClusterIP   10.96.0.1    <none>        443/TCP   26d
nginx            ClusterIP   None         <none>        80/TCP    5s
nginx-headless   ClusterIP   None         <none>        80/TCP    10m
```


## o que são os Services?

Os Services são uma abstração que define um conjunto lógico de Pods e uma política para acessá-los. Eles permitem que você exponha uma ou mais Pods para serem acessados por outros Pods, independentemente de onde eles estejam em execução no cluster.

Os Services são definidos usando a API do Kubernetes, normalmente através de um arquivo de manifesto YAML.

Tipos de Services
Existem quatro tipos principais de Services:

- **ClusterIP (padrão):** Expõe o Service em um IP interno no cluster. Este tipo torna o Service acessível apenas dentro do cluster.

- **NodePort:** Expõe o Service na mesma porta de cada Node selecionado no cluster usando NAT. Torna o Service acessível de fora do cluster usando :.

- **LoadBalancer:** Cria um balanceador de carga externo no ambiente de nuvem atual (se suportado) e atribui um IP fixo, externo ao cluster, ao Service.

- **ExternalName:** Mapeia o Service para o conteúdo do campo externalName (por exemplo, foo.bar.example.com), retornando um registro CNAME com seu valor.

### Como os Services funcionam?

Os Services no Kubernetes fornecem uma abstração que define um conjunto lógico de Pods e uma política para acessá-los. Os conjuntos de Pods são determinados por meio de seletores de rótulo (Label Selectors). Embora cada Pod tenha um endereço IP único, esses IPs não são expostos fora do cluster sem um serviço.

Sempre é bom reforçar a importância dos Labels no Kubernetes, pois eles são a base para a maioria das operações no Kubernetes, então cuide com carinho dos seus Labels.

### Os Services e os Endpoints

Os Services no Kubernetes representam um conjunto estável de Pods que fornecem determinada funcionalidade. A principal característica dos Services é que eles mantêm um endereço IP estável e uma porta de serviço que permanecem constantes ao longo do tempo, mesmo que os Pods subjacentes sejam substituídos.

Para implementar essa abstração, o Kubernetes usa uma outra abstração chamada Endpoint. Quando um Service é criado, o Kubernetes também cria um objeto Endpoint com o mesmo nome. Esse objeto Endpoint rastreia os IPs e as portas dos Pods que correspondem aos critérios de seleção do Service.

Por exemplo, quando você cria um Service automaticamente é criado os EndPoints que representam os Pods que estão sendo expostos pelo Service.

Para você ver os EndPoints criados, você pode utilizar o comando:

```
kubectl get endpoints 
```


> StatefulSet file
```
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: nginx
spec:
  serviceName: "nginx"
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
       labels:
          app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.22.1
        ports: 
        - containerPort: 80
          name: http
        volumeMounts:
        - name: nginx-pv
          mountPath: /usr/share/nginx/html  
  volumeClaimTemplates:
  - metadata:
      name: nginx-pv
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 1Gi   
```

> Service ClusterIP
```
apiVersion: v1
kind: Service
metadata:
  name: nginx
  labels:
    app: nginx
    env: dev
spec:
  selector:
    app: nginx
  ports:
  - port: 80
    name: http
    targetPort: 80
  type: ClusterIP

```

> Deployment file para o nginx (Apenas teste)
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector: 
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
        env: dev
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
          name: http 
```

> Service headless file
```
apiVersion: v1
kind: Service
metadata: 
  name: nginx
  labels:
    app: nginx
spec:
  ports:
  - port: 80
    name: http
  clusterIP: None
  selector: 
    app: nginx
```

> Loadbalancer service file
```
apiVersion: v1
kind: Service
metadata:
  name: nginx-loadbalancer
  labels:
    app: nginx
    env: dev
spec:
  selector: 
    app: nginx
  ports:
  - port: 80
    name: http
    targetPort: 80
  type: LoadBalancer
```

> NodePort service file
```
apiVersion: v1
kind: Service
metadata: 
  name: nginx-nodeport
  labels:
    app: nginx
    env: dev
spec: 
  selector:
    app: nginx
  ports:
  - port: 80
    name: http
    targetPort: 80
    nodePort: 32000
  type: NodePort

```


---------------

### Desafio dia 07 

#### Parte 1

```
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: giropops-set
spec:
  serviceName: "giropops-set"
  replicas: 3
  selector:
    matchLabels:
      app: giropops-set
  template:
    metadata:
       labels:
          app: giropops-set
    spec:
      containers:
      - name: giropops-set
        image: nginx:1.19.0
        ports: 
        - containerPort: 80
          name: http
        volumeMounts:
        - name: giropops-pv
          mountPath: /usr/share/nginx/html  
  volumeClaimTemplates:
  - metadata:
      name: giropops-pv
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 1Gi   
```

#### Parte 2

E o deploy do giropops senhas

```
apiVersion: v1
kind: Service
metadata: 
  name: giropops-service
  labels:
    app: giropops-service
spec:
  ports:
  - port: 80
    name: http
  clusterIP: None
  selector: 
    app: giropops-set
```


```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: giropops-senhas
  name: giropops-senhas
spec:
  replicas: 2
  selector:
    matchLabels:
      app: giropops-senhas
  template:
    metadata:
      labels:
        app: giropops-senhas
    spec:
      containers:
      - image: linuxtips/giropops-senhas:1.0
        name: giropops-senhas
        ports:
        - containerPort: 5000
        imagePullPolicy: Always
```


```
apiVersion: v1
kind: Service
metadata:
  name: giropops-senhas
  labels:
    app: giropops-senhas
spec:
  selector:
    app: giropops-senhas
  ports:
    - protocol: TCP
      port: 5000
      nodePort: 32500
      targetPort: 5000
      name: tcp-app
  type: NodePort
```

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: redis
  name: redis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - image: redis
        name: redis
        ports:
          - containerPort: 6379
```

```
apiVersion: v1
kind: Service
metadata:
  name: redis-service
spec:
  selector:
    app: redis
  ports:
    - protocol: TCP
      port: 6379
      targetPort: 6379
  type: ClusterIP
```