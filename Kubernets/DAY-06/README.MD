# Gerenciamento de volumes no Kubernetes


### O que são volumes?


Para simplificar o seu entendimento nesse momento, volumes nada mais são do que um diretório dentro do Pod que pode ser utilizado para armazenar dados. Eles podem ser utilizados para armazenar dados que precisam ser persistidos, como por exemplo, dados de um banco de dados, ou dados de um sistema de arquivos distribuído.

Quando estamos falando sobre volumes no Kubernetes, precisamos entender que temos basicamente dois tipos de volumes, os ***ephemeral volumes*** e os ***persistent volumes.***

Os ***ephemeral volumes***, que inclusive já vimos durante o treinamento o emptyDir, são volumes que são criados e destruídos junto com o Pod. Ele é um volume também, porém com uma diferença, ele não é persistente. Caso ocorra algum problema com o Pod e ele seja removido, o emptyDir também será removido.

Agora quando estamos falando sobre volumes do tipo ***persistent volumes***, estamos falando sobre volumes que são criados e não são destruídos junto com o Pod, eles são persistidos, são volumes que seus dados são mantidos mesmo que o Pod seja removido.

Esse tipo de volume é super importante para aplicações que precisam armazenar dados que precisam ser mantidos mesmo que o Pod seja removido, como por exemplo, um banco de dados.

### describe storageclass.storage.k8s.io

```
Name:            standard
IsDefaultClass:  Yes
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"storage.k8s.io/v1","kind":"StorageClass","metadata":{"annotations":{"storageclass.kubernetes.io/is-default-class":"true"},"name":"standard"},"provisioner":"rancher.io/local-path","reclaimPolicy":"Delete","volumeBindingMode":"WaitForFirstConsumer"}
,storageclass.kubernetes.io/is-default-class=true
Provisioner:           rancher.io/local-path
Parameters:            <none>
AllowVolumeExpansion:  <unset>
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     WaitForFirstConsumer
Events:                <none>

```

### Criando o nosso Storage Class

```yml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata: 
  name: giropops
provisioner: kubernetes.io/no-provisioner
reclaimPolicy: Retain
volumeBindingMode: WaitForFirstConsumer
root@elliot:/home/elliot/k8s# 
```
> describe:

```
Name:            giropops
IsDefaultClass:  No
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"storage.k8s.io/v1","kind":"StorageClass","metadata":{"annotations":{},"name":"giropops"},"provisioner":"kubernetes.io/no-provisioner","reclaimPolicy":"Retain","volumeBindingMode":"WaitForFirstConsumer"}

Provisioner:           kubernetes.io/no-provisioner
Parameters:            <none>
AllowVolumeExpansion:  <unset>
MountOptions:          <none>
ReclaimPolicy:         Retain
VolumeBindingMode:     WaitForFirstConsumer
Events:                <none>
root@elliot:/home/elliot/k8s# cat storag
```


## Storage Class


Uma ***StorageClass*** no Kubernetes é um objeto que descreve e define diferentes classes de armazenamento disponíveis no cluster. Essas classes de armazenamento podem ser usadas para provisionar dinamicamente PersistentVolumes (PVs) de acordo com os requisitos dos PersistentVolumeClaims (PVCs) criados pelos usuários.

A ***StorageClass*** é útil para gerenciar e organizar diferentes tipos de armazenamento, como armazenamento em disco rápido e caro ou armazenamento em disco mais lento e barato. Além disso, a ***StorageClass*** pode ser usada para definir diferentes políticas de retenção, provisionamento e outras características de armazenamento específicas.

Os administradores do cluster podem criar e gerenciar várias StorageClasses para permitir que os usuários finais escolham a classe de armazenamento adequada para suas necessidades.

Cada ***StorageClass*** é definida com um provisionador, que é responsável por criar PersistentVolumes dinamicamente conforme necessário. Os provisionadores podem ser internos (fornecidos pelo próprio Kubernetes) ou externos (fornecidos por provedores de armazenamento específicos).

Inclusive os provisionadores podem ser diferentes para cada provedor de nuvem ou onde o Kubernetes está sendo executado. Vou listar alguns provisionadores que são usados e seus respectivos provedores:

- kubernetes.io/aws-ebs: AWS Elastic Block Store (EBS)
- kubernetes.io/azure-disk: Azure Disk
- kubernetes.io/gce-pd: Google Compute Engine (GCE) Persistent Disk
- kubernetes.io/cinder: OpenStack Cinder
- kubernetes.io/vsphere-volume: vSphere
- kubernetes.io/no-provisioner: Volumes locais
- kubernetes.io/host-path: Volumes locais


### Criando o nosso PersistentVolume

```yml
apiVersion: v1
kind: PersistentVolume
metadata:
  labels:
    storage: lento
  name: marcus-py
spec:
  capacity:
    storage: 1Gi    
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath: 
    path: /mnt/data
  storageClassName: giropops
```

> describe

```
Name:            marcus-py
Labels:          storage=lento
Annotations:     <none>
Finalizers:      [kubernetes.io/pv-protection]
StorageClass:    giropops
Status:          Available
Claim:           
Reclaim Policy:  Retain
Access Modes:    RWO
VolumeMode:      Filesystem
Capacity:        1Gi
Node Affinity:   <none>
Message:         
Source:
    Type:          HostPath (bare host directory volume)
    Path:          /mnt/data
    HostPathType:  
Events:            <none>
```


### PV - Persistent Volume

O ***PV*** é um objeto que representa um recurso de armazenamento físico em um cluster Kubernetes. Ele pode ser um disco rígido em um nó do cluster, um dispositivo de armazenamento em rede (NAS) ou mesmo um serviço de armazenamento em nuvem, como o AWS EBS ou Google Cloud Persistent Disk.

O ***PV*** é utilizado para fornecer armazenamento durável, ou seja, os dados armazenados no PV permanecem disponíveis mesmo quando o container é reiniciado ou movido para outro nó.

No Kubernetes, você pode usar várias soluções de armazenamento como Persistent Volumes (PVs). Essas soluções podem ser divididas em dois tipos: armazenamento local e armazenamento em rede


---------------------------------------------

### Vamos usar o nfs volume


Vamos criar um dir no /mnt/nfs e alterar o owner para o nosso user usando o seguinte comando:

```
chown user.user /mnt/nfs
```

vamos instalar os seguintes pacotes nfs-kernel-server nfs-common

```
sudo apt-get install nfs-kernel-server nfs-common
```

Vamos editar o arquivo de exports dentro de /etc/exports e add as seguintes conf dentro dele

```
/mnt/nfs	*(rw,sync,no_root,squash,no_subtree_check)
```
```
sudo exportfs -ar
```

```
ubuntu@k8s-1:~/pick$ k get pv
NAME            CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
marcus-pv-nfs   1Gi        RWO            Retain           Available           giropops       <unset>                          3s
marcus-py       1Gi        RWO            Retain           Available           giropops       <unset>                          93m


ubuntu@k8s-1:~/pick$ k describe pv marcus-pv-nfs
Name:            marcus-pv-nfs
Labels:          storage=nfs
Annotations:     <none>
Finalizers:      [kubernetes.io/pv-protection]
StorageClass:    giropops
Status:          Available
Claim:           
Reclaim Policy:  Retain
Access Modes:    RWO
VolumeMode:      Filesystem
Capacity:        1Gi
Node Affinity:   <none>
Message:         
Source:
    Type:      NFS (an NFS mount that lasts the lifetime of a pod)
    Server:    172.31.49.148
    Path:      /mnt/nfs
    ReadOnly:  false
Events:        <none>
```

```
ubuntu@k8s-1:~/pick$ cat pv-nfs.yaml 
apiVersion: v1
kind: PersistentVolume
metadata:
  labels:
    storage: nfs
  name: marcus-pv-nfs
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  nfs:
    server: 172.31.49.148
    path: "/mnt/nfs"
  storageClassName: giropops 
```

```
ubuntu@k8s-1:~/pick$ cat storageclass.yaml 
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata: 
  name: giropops
provisioner: kubernetes.io/no-provisioner
reclaimPolicy: Retain
volumeBindingMode: WaitForFirstConsumer
```

```
ubuntu@k8s-1:~/pick$ cat persistentVolume.yaml 
apiVersion: v1
kind: PersistentVolume
metadata:
  labels:
    storage: lento
  name: marcus-py
spec:
  capacity:
    storage: 1Gi    
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath: 
    path: /mnt/data
  storageClassName: giropops
```

```
ubuntu@k8s-1:~/pick$ cat pvc.yaml 
apiVersion: v1
kind: PersistentVolumeClaim
metadata: 
  labels: 
    pvc: marcus-pvc
  name: marcus-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests: 
      storage: 1Gi
  storageClassName: giropops
  selector:  
    matchLabels:
      storage: nfs
```

```
ubuntu@k8s-1:~/pick$ cat pv-nfs.yaml 
apiVersion: v1
kind: PersistentVolume
metadata:
  labels:
    storage: nfs
  name: marcus-pv-nfs
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  nfs:
    server: 172.31.49.148
    path: "/mnt/nfs"
  storageClassName: giropops 
```


## PVC - Persistent Volume Claim
O PVC é uma solicitação de armazenamento feita pelos usuários ou aplicativos no cluster Kubernetes. Ele permite que os usuários solicitem um volume específico, com base em tamanho, tipo de armazenamento e outras características. O PVC age como uma "assinatura" que reivindica um PV para ser usado por um contêiner. O Kubernetes tenta associar automaticamente um PVC a um PV compatível, garantindo que o armazenamento seja alocado corretamente.

Através do PVC, as pessoas podem abstrair os detalhes de cada tipo de armazenamento, permitindo maior flexibilidade e portabilidade entre diferentes ambientes e provedores de infraestrutura. Ele também permite que os usuários solicitem volumes com diferentes características, como tamanho, tipo de armazenamento e modo de acesso.