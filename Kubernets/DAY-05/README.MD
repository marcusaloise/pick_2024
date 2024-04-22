# Montando o nosso primeiro Cluster

## O que é um cluster Kubernetes?
Um cluster Kubernetes é um conjunto de nodes que trabalham juntos para executar todos os nossos pods. Um cluster Kubernetes é composto por nodes que podem ser tanto control plane quanto workers. O control plane é responsável por gerenciar o cluster, enquanto os workers são responsáveis por executar os pods que são criados no cluster pelos usuários.

Quando estamos pensando em um cluster Kubernetes, precisamos lembrar que a principal função do Kubernetes é orquestrar containers. O Kubernetes é um orquestrador de containers, sendo assim quando estamos falando de um cluster Kubernetes, estamos falando de um cluster de orquestradores de containers. Eu sempre gosto de pensar em um cluster Kubernetes como se fosse uma orquestra, onde temos uma pessoa regendo a orquestra, que é o control plane, e temos as pessoas musicistas, que estão executando os instrumentos, que são os workers.

### Sendo assim, o control plane é responsável por gerenciar o cluster, como por exemplo:

- Criar e gerenciar os recursos do cluster, como por exemplo, namespaces, deployments, services, configmaps, secrets, etc.

- Gerenciar os workers do cluster.

- Gerenciar a rede do cluster.

- O etcd desempenha um papel crucial na manutenção da estabilidade e confiabilidade do cluster. Ele armazena as informações de configuração de todos os componentes do control plane, incluindo os detalhes dos serviços, pods e outros recursos do cluster. Graças ao seu design distribuído, o etcd é capaz de tolerar falhas e garantir a continuidade dos dados, mesmo em caso de falha de um ou mais nós. Além disso, ele suporta a comunicação segura entre os componentes do cluster, usando criptografia TLS para proteger os dados.

- O scheduler é o componente responsável por decidir em qual nó os pods serão executados, levando em consideração os requisitos e os recursos disponíveis. O scheduler também monitora constantemente a situação do cluster e, se necessário, ajusta a distribuição dos pods para garantir a melhor utilização dos recursos e manter a harmonia entre os componentes.

- O controller-manager é responsável por gerenciar os diferentes controladores que regulam o estado do cluster e mantêm tudo funcionando. Ele monitora constantemente o estado atual dos recursos e compara-os com o estado desejado, fazendo ajustes conforme necessário.

- Onde está o api-server é o componente central que expõe a API do Kubernetes, permitindo que outros componentes do control plane, como o controller-manager e o scheduler, bem como ferramentas externas, se comuniquem e interajam com o cluster. O api-server é a principal interface de comunicação do Kubernetes, autenticando e autorizando solicitações, processando-as e fornecendo as respostas apropriadas. Ele garante que as informações sejam compartilhadas e acessadas de forma segura e eficiente, possibilitando uma colaboração harmoniosa entre todos os componentes do cluster.

### Já no lado dos workers, as coisa são bem mais simples, pois a principal função deles é executar os pods que são criados no cluster pelos usuários. Nos workers nós temos, por padrão, os seguintes componentes do Kubernetes:

- O kubelet é o agente que funciona em cada nó do cluster, garantindo que os containers estejam funcionando conforme o esperado dentro dos pods. Ele assume o controle de cada nó, garantindo que os containers estejam sendo executados conforme as instruções recebidas do control plane. Ele monitora constantemente o estado atual dos pods e compara-os com o estado desejado. Caso haja alguma divergência, o kubelet faz os ajustes necessários para que os containers sigam funcionando perfeitamente.

- O kube-proxy, que é o componente responsável fazer ser possível que os pods e os services se comuniquem entre si e com o mundo externo. Ele observa o control plane para identificar mudanças na configuração dos serviços e, em seguida, atualiza as regras de encaminhamento de tráfego para garantir que tudo continue fluindo conforme o esperado.

- Todos os pods de nossas aplicações.

### Formas de instalar um cluster K8s:

***kubeadm:*** É uma ferramenta para criar e gerenciar um cluster Kubernetes em vários nós. Ele automatiza muitas das tarefas de configuração do cluster, incluindo a instalação do control plane e dos nodes. É altamente configurável e pode ser usado para criar clusters personalizados.

***Kubespray:*** É uma ferramenta que usa o Ansible para implantar e gerenciar um cluster Kubernetes em vários nós. Ele oferece muitas opções para personalizar a instalação do cluster, incluindo a escolha do provedor de rede, o número de réplicas do control plane, o tipo de armazenamento e muito mais. É uma boa opção para implantar um cluster em vários ambientes, incluindo nuvens públicas e privadas.

***Cloud Providers:*** Muitos provedores de nuvem, como AWS, Google Cloud Platform e Microsoft Azure, oferecem opções para implantar um cluster Kubernetes em sua infraestrutura. Eles geralmente fornecem modelos predefinidos que podem ser usados para implantar um cluster com apenas alguns cliques. Alguns provedores de nuvem também oferecem serviços gerenciados de Kubernetes que lidam com toda a configuração e gerenciamento do cluster.

***Kubernetes Gerenciados:*** São serviços gerenciados oferecidos por alguns provedores de nuvem, como Amazon EKS, o GKE do Google Cloud e o AKS, da Azure. Eles oferecem um cluster Kubernetes gerenciado onde você só precisa se preocupar em implantar e gerenciar seus aplicativos. Esses serviços lidam com a configuração, atualização e manutenção do cluster para você. Nesse caso, você não tem que gerenciar o control plane do cluster, pois ele é gerenciado pelo provedor de nuvem.

***Kops:*** É uma ferramenta para implantar e gerenciar clusters Kubernetes na nuvem. Ele foi projetado especificamente para implantação em nuvens públicas como AWS, GCP e Azure. Kops permite criar, atualizar e gerenciar clusters Kubernetes na nuvem. Algumas das principais vantagens do uso do Kops são a personalização, escalabilidade e segurança. No entanto, o uso do Kops pode ser mais complexo do que outras opções de instalação do Kubernetes, especialmente se você não estiver familiarizado com a nuvem em que está implantando.

***Minikube e kind:*** São ferramentas que permitem criar um cluster Kubernetes localmente, em um único nó. São úteis para testar e aprender sobre o Kubernetes, pois você pode criar um cluster em poucos minutos e começar a implantar aplicativos imediatamente. Elas também são úteis para pessoas desenvolvedoras que precisam testar suas aplicações em um ambiente Kubernetes sem precisar configurar um cluster em um ambiente de produção.




# Steps para criar o nosso cluster k8s

***Liberar portas: 6443, 10250-10255, 30000-32767, 2379-2380 e 6783(have) (6783 - 6784 udp)***

### Requisitos de maquina:

- 2 GB ou mais de RAM por máquina (menos de 2 GB não é recomendado)

- 2 CPUs ou mais



### Algumas portas precisam estar abertas para que o cluster funcione corretamente, as principais:

**Porta 6443:** É a porta padrão usada pelo Kubernetes API Server para se comunicar com os componentes do cluster. É a porta principal usada para gerenciar o cluster e deve estar sempre aberta.

**Portas 10250-10255:** Essas portas são usadas pelo kubelet para se comunicar com o control plane do Kubernetes. A porta 10250 é usada para comunicação de leitura/gravação e a porta 10255 é usada apenas para comunicação de leitura.

**Porta 30000-32767:** Essas portas são usadas para serviços NodePort que precisam ser acessíveis fora do cluster. O Kubernetes aloca uma porta aleatória dentro desse intervalo para cada serviço NodePort e redireciona o tráfego para o pod correspondente.

**Porta 2379-2380:** Essas portas são usadas pelo etcd, o banco de dados de chave-valor distribuído usado pelo control plane do Kubernetes. A porta 2379 é usada para comunicação de leitura/gravação e a porta 2380 é usada apenas para comunicação de eleição.


### Para essa aula usamos as seguintes maquinas para o cluster

- 3 ec2 t2.medium
- liberação das portas mencionadas acimas usando SG


### E vamos iniciar o processo siga os seguintes passos 

```bash
sudo swapoff -a
```

Acesse o arquivo k8s.conf no diretorio /etc/modules-load.d/k8s.conf e add a seguinte configuração

```conf
overlay
br_netfilter
```

Apos isso vamos carregar as probres

```bash
sudo modprobe overlay

sudo modprobe br_netfilter
```

Acesse o arquivo k8s.conf no diretorio /etc/sysctl.d/k8s.conf e add a seguinte configuração

```conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1

```

rode o seguinte comando para aplicar as config do sysctl

```bash
sudo sysctl --system
```

Agora vamos instalar algumas coisas dentro das maquinas do nosso cluster

```bash
sudo apt-get update && sudo apt-get install apt-transport-https curl -y

# Add keys
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

# Add Repo
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

```

Vamos atualizar a lista dos pacotes dentro da nossa maquina e instalar as ferramentas para gerenciar o nosso cluster


```bash
sudo apt-get update && sudo apt-get install -y kubeadm kubectl kubelet

# Vamos rodar esse comando para não atualizar os pacotes listados abaixo
sudo apt-mark hold kubelet kubeadm kubectl

```

### Vamos preparar a nossa maquina para o containerD

execute os seguintes comandos para instalar os pacotes requiridos: 

```bash
sudo apt-get install gnupg lsb-release ca-certificates


# Add keys

sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc


# Add repo

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update


```
Com os comandos acima executados vamos atualizar a nossa lista de pacotes novamente

```bash
sudo apt-get update && sudo apt-get install containerd.io -y

```

Agora vamos mudar algumas config do containerd

```bash
sudo containerd config default | sudo tee /etc/containerd/config.toml

# Vamo mudar a tag de SystemdCgroup para true
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/g' /etc/containerd/config.toml

```

Vamos restartar o containerd e ativar o kubelet

```bash
# Restar do containerd para aplicar as conf
sudo systemctl restart containerd

# Ativar toda vez q a maquina realizar o boot
sudo systemctl enable containerd

# Ativar o kubelet (finalmente)
sudo systemctl enable --now kubelet
```
### Agora vamos aplicar as seguintes config no primeiro node (manager)

```bash
# apiserver-advertise-address= use o ip da sua maquina meu bacana
sudo kubeadm init --pod-network-cidr=10.10.0.0/16 --apiserver-advertise-address=172.31.49.148

```
Após você esecutar o comando acima ele vai te gerar alguns comandos para serem executados na maquina que são os seguintes

```bash
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

```

E ele também vai gerar o comando do kubeadm para adentrar outras maquinas no cluster sendo ela os workers

> [!IMPORTANT]
> O comando abaixo e apenas nos nodes workers que foram gerados
```bash
kubeadm join 172.31.49.148:6443 --token hvjswa.pymki0vrighy3td3 \
	--discovery-token-ca-cert-hash sha256:1d2693b6013c75ffac8edc4491e867beb4151bc838b79d6d9d5c9465f33f79ed 
```
### Agora um passo muito importante e instalar o Wave para a comunicação entre os nodes

```bash
kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml
```

## Agora e só ser feliz

