# Descomplicando o K8s

## O que é um container: 

Uma solução de virtualização leve usada para executar e isolar aplicações. Diferente das máquinas virtuais tradicionais, que virtualizam todo um sistema operacional para cada aplicação, os containers compartilham o mesmo núcleo do sistema operacional do host, mas podem ser isolados uns dos outros. Isso permite que sejam mais eficientes, leves e rápidos do que máquinas virtuais completas.

Os containers são usados para empacotar uma aplicação com todas as partes necessárias, como bibliotecas e dependências, e entregá-la como um único pacote. Isso facilita o desenvolvimento, testes, implementação e atualizações, pois garante que a aplicação funcionará da mesma forma em qualquer ambiente, seja no desenvolvimento, teste ou produção.

### O que é um docker engine:

O motor que cria e gerencia os containers Docker no host que ele está instalado. Funciona como um daemon que executa os serviços de containerização, incluindo a construção de imagens Docker, sua execução como containers, e a orquestração deles.

As principais funcionalidades do Docker Engine incluem:

- **Construção de Imagens**: Permite criar imagens Docker usando um Dockerfile, que é um arquivo de texto contendo instruções sobre como construir a imagem. Essas imagens podem incluir aplicações, bibliotecas, dependências e outros componentes necessários para a aplicação funcionar.

- **Execução de Containers**: A partir das imagens Docker, o Docker Engine pode iniciar e gerenciar containers, executando as aplicações encapsuladas em ambientes isolados.

- **Armazenamento e Compartilhamento de Imagens**: Através do Docker Hub e outros registros de container, o Docker Engine pode compartilhar imagens, facilitando a colaboração e distribuição de aplicações.

- **Networking**: Gerencia a rede dos containers, permitindo a comunicação entre containers e também entre containers e o mundo externo.

- **Volume de Dados**: Permite que os containers acessem e armazenem dados em volumes persistentes, que são independentes do ciclo de vida dos containers, garantindo a persistência de dados.

O Docker Engine pode operar em diferentes modos, sendo os mais comuns o modo standalone, para gerenciar containers em um único host, e o modo swarm, utilizado para orquestrar e gerenciar um cluster de Docker hosts, permitindo o escalonamento e gerenciamento de múltiplos containers de forma distribuída.


## O que é um docker runtime:

O "Docker runtime" refere-se ao ambiente de execução usado pelo Docker para executar containers. Este ambiente é responsável por gerenciar o ciclo de vida dos containers, desde a sua criação e execução até a sua parada e remoção. O runtime do Docker cuida de tudo que é necessário para que um container funcione corretamente, incluindo a alocação de recursos (como CPU e memória), isolamento de processo, gerenciamento de rede e segurança.

Até uma mudança recente na arquitetura do Docker, o componente principal do runtime do Docker era conhecido como Docker Engine, que incluía o daemon do Docker (`dockerd`), responsável por criar e gerenciar os containers. O Docker Engine utiliza uma série de tecnologias de baixo nível, como namespaces e cgroups do Linux, para fornecer o isolamento e gerenciamento de recursos necessários para executar containers.

Além disso, o conceito de runtime do Docker se expandiu com a introdução da Container Runtime Interface (CRI) pela comunidade Kubernetes, o que permitiu a integração de diferentes runtimes de container com o Kubernetes. Isso inclui runtimes como containerd e CRI-O, que podem ser usados em vez do Docker Engine para executar containers em ambientes orquestrados por Kubernetes. Esses runtimes são otimizados para ambientes de produção e oferecem uma camada mais leve e focada para execução de containers, seguindo as especificações da Open Container Initiative (OCI) para garantir interoperabilidade e padronização.

Portanto, quando se fala sobre "Docker runtime", está se referindo ao conjunto de componentes e mecanismos que o Docker usa para executar containers de forma eficiente e segura em diferentes ambientes.



## O que é Open Container Initiative (OCI):
A Open Container Initiative (OCI) é um projeto sob a Linux Foundation que foi criado para desenvolver padrões abertos em torno de formatos de containers e runtimes. A iniciativa começou em 2015 após a Docker, Inc. doar sua especificação de container e runtimes, como Docker, para ajudar a criar um padrão na indústria.

Os principais objetivos da OCI são:

1. **Definir padrões:** Estabelecer padrões para o formato da imagem de um container e para o runtime do container, promovendo a portabilidade entre diferentes sistemas e plataformas.

2. **Promover a colaboração:** Encorajar a colaboração entre líderes da indústria e desenvolvedores para evitar a fragmentação e garantir que os containers sejam padronizados e interoperáveis.

3. **Garantir a governança aberta:** Garantir que o desenvolvimento dos padrões seja feito de maneira transparente e com a participação da comunidade.

4. **Certificar produtos:** Oferecer um programa de certificação para produtos que atendem aos padrões da OCI, assegurando a conformidade e compatibilidade.

A OCI é importante para o ecossistema de containers pois permite que organizações utilizem containers de diferentes fornecedores sem se preocupar com incompatibilidades e bloqueio por fornecedor (vendor lock-in). Isso ajuda na adoção de tecnologias de container e na construção de um ecossistema mais rico e diversificado.

Os padrões definidos pela OCI incluem a Especificação de Formato de Imagem de Container (OCI Image Format specification) e a Especificação de Runtime de Container (OCI Runtime specification), que estabelecem como os containers devem ser empacotados e executados de maneira consistente.

## O que é o Kubernetes (k8s):

O Kubernetes é um sistema de orquestração de containers de código aberto que automatiza a implantação, o escalonamento e a gestão de aplicações em containers. Desenvolvido originalmente pelo Google com base em sua experiência com o Borg, um sistema interno similar, o Kubernetes foi doado à Cloud Native Computing Foundation (CNCF) e se tornou uma das plataformas mais populares para gerenciamento de containers.

Eis o que o Kubernetes faz:

1. **Orquestração de Containers:** Gerencia a vida útil de containers em diferentes ambientes de computação.

2. **Automatização da Implantação:** Permite a implantação automática de aplicações em containers conforme definido em configurações declarativas.

3. **Escalonamento:** Oferece a habilidade de escalar aplicações automaticamente com base no uso ou através de comandos manuais.

4. **Balanceamento de Carga:** Distribui o tráfego de rede entre containers para manter a estabilidade e eficiência da aplicação.

5. **Gerenciamento de Estado:** Lida com armazenamento persistente para containers, o que permite executar aplicações que necessitam de armazenamento duradouro.

6. **Autocura:** Reinicia containers que falham, substitui e reschedule containers quando nós (nodes) morrem, mata containers que não respondem ao health check definido pelo usuário e não os anuncia aos clientes até que estejam prontos para servir.

7. **Descoberta de Serviços e Balanceamento de Carga:** O Kubernetes pode expor um container usando o DNS ou usando seu próprio endereço IP. Se o tráfego para um container for alto, o Kubernetes pode balancear a carga e distribuir o tráfego de rede para que a implantação seja estável.

O Kubernetes foi projetado para ser executado em qualquer lugar, permitindo que você implante aplicações na nuvem, no local (on-premise), em um ambiente híbrido ou mesmo em múltiplos provedores de nuvem, o que o torna uma solução ideal em um mundo com várias plataformas de nuvem.


## O que são workers e control plane do kubernetes:

Dentro do Kubernetes, o cluster é composto por um conjunto de máquinas, chamadas de nós ou "nodes", que rodam as aplicações em containers. Estes nós são organizados em duas categorias principais: o Control Plane (ou plano de controle) e os Worker Nodes (nós de trabalho).

### Control Plane (Plano de Controle)
O Control Plane é responsável pela gestão global do cluster e pelo gerenciamento da orquestração dos containers. Ele toma decisões sobre o cluster (como, por exemplo, agendamento de pods) e reage a eventos no cluster (como, por exemplo, se um pod falha, ele é o responsável por iniciar outro pod). O Control Plane inclui várias partes:

- **API Server (Servidor de API):** É o ponto de entrada para todas as REST commands usadas para controlar o cluster. Ele processa as requisições, valida-as e executa as regras de negócio do cluster.
- **Scheduler (Agendador):** Decide qual nó receberá a execução de um pod, baseado em recursos disponíveis e outros fatores.
- **Controller Manager:** Executa os processos de controle de fundo, como lidar com o ciclo de vida dos objetos do Kubernetes.
- **etcd:** Banco de dados de configuração distribuído que armazena todos os dados do cluster para garantir a persistência e o estado do cluster.

### Worker Nodes (Nós de Trabalho)
Os Worker Nodes são as máquinas onde as aplicações realmente rodam. Cada Worker Node é gerenciado pelo Control Plane e contém os serviços necessários para rodar os Pods, que são as unidades de trabalho do Kubernetes.

- **Kubelet:** Agente que roda em cada nó e se assegura de que os containers estão rodando em um Pod.
- **Kube-Proxy:** Mantém as regras de rede nos nós. Isso permite a comunicação para dentro e fora de seu cluster.
- **Container Runtime:** O software responsável por rodar os containers. Exemplos incluem Docker, containerd e CRI-O.

Juntos, o Control Plane e os Worker Nodes possibilitam a execução das aplicações em containers de forma distribuída e com alta disponibilidade.


### Arquitetura do Control Plane:

- **etcd:** grava o estado do cluster, se comunica diretamente com o `Kube ApiServer`.
- **Kube ApiServer:** Pega o estado do cluster e toda comunicação e envia para o `etcd`.
- **Kube Scheduler:** Quem gerencia a criação dos pods, container e afins.
- **Kube controler Manager:** executa processos de controlador, quase a msm pegada do schedeler.

### Arquitetura do worker:

- **kubelet:** Agent que roda em cada node
- **Kube Proxy:** Todo node tem um proxy, que faz a comunicação entre pods e todo o mundo.

### Quais são as portas essenciais:

*Control Plane:*
- **Kube-ApiServer ->** 6443 tcp
- **etcd ->** 2379 - 2380 tcp
- **Kubelet ->** 10250 tcp
- **kube-scheduler ->** 10251 tcp
- **kube-controler ->** 10252 tcp

*Worker:*
- **NodePort ->** 30000 - 32767 tcp
- **weave net ->** 6783 - 6784 tcp/udp



## Introdução pods, replica sets, deployment e services

Node > Pods > Container

### Pods: 

`Pods` e a menor unidade de gerenciamento dentro do k8s, que contem um ou mais containers.

- Trabalha com isolamento
    - Somente um IP por pod


### Replica sets:

controler de quantidade de replicas de nodes/pods, e criado juntamente com o deployment.


### Deployment:

Arquivo de configuração(deploy), realiza o deploy das aplicações com suas config e envs

 
### Services:

Gerenciamento de regra de serviço entre pods/nodes/container

## Kubectl

Comando para interagir com o cluster

```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

```
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

```


## kind: 

A ferramenta kind (Kubernetes IN Docker) é uma ferramenta que permite rodar clusters Kubernetes dentro de contêineres Docker. É uma ferramenta poderosa e popular para desenvolvimento e teste de Kubernetes, pois permite a criação rápida e fácil de um ou mais clusters Kubernetes em uma máquina local, sem a necessidade de configuração complexa ou recursos de hardware dedicados.




```yml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker


```

```yml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: giropops
  name: giropops
spec:
  containers:
  - image: nginx
    name: giropops
    ports:
    - containerPort: 80
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}


```

____


```yml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
- role: worker
```

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
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
```
