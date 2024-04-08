# Descomplicando o K8s

### O que é um container: 

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


### O que é um docker runtime:

O "Docker runtime" refere-se ao ambiente de execução usado pelo Docker para executar containers. Este ambiente é responsável por gerenciar o ciclo de vida dos containers, desde a sua criação e execução até a sua parada e remoção. O runtime do Docker cuida de tudo que é necessário para que um container funcione corretamente, incluindo a alocação de recursos (como CPU e memória), isolamento de processo, gerenciamento de rede e segurança.

Até uma mudança recente na arquitetura do Docker, o componente principal do runtime do Docker era conhecido como Docker Engine, que incluía o daemon do Docker (`dockerd`), responsável por criar e gerenciar os containers. O Docker Engine utiliza uma série de tecnologias de baixo nível, como namespaces e cgroups do Linux, para fornecer o isolamento e gerenciamento de recursos necessários para executar containers.

Além disso, o conceito de runtime do Docker se expandiu com a introdução da Container Runtime Interface (CRI) pela comunidade Kubernetes, o que permitiu a integração de diferentes runtimes de container com o Kubernetes. Isso inclui runtimes como containerd e CRI-O, que podem ser usados em vez do Docker Engine para executar containers em ambientes orquestrados por Kubernetes. Esses runtimes são otimizados para ambientes de produção e oferecem uma camada mais leve e focada para execução de containers, seguindo as especificações da Open Container Initiative (OCI) para garantir interoperabilidade e padronização.

Portanto, quando se fala sobre "Docker runtime", está se referindo ao conjunto de componentes e mecanismos que o Docker usa para executar containers de forma eficiente e segura em diferentes ambientes.

