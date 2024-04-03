# Docker network 

O Docker Network refere-se ao modelo de rede dentro do ecossistema Docker, que permite a comunicação entre contêineres Docker, além de gerenciar como os contêineres Docker se comunicam com a Internet e entre si. A plataforma Docker vem com várias redes padrão configuradas e também permite a criação de redes personalizadas. Cada rede é isolada das outras, oferecendo controle sobre como os contêineres se comunicam. Vamos explorar mais detalhadamente os aspectos da rede Docker:

### Tipos de Redes Docker

O Docker suporta diferentes tipos de redes, cada uma adequada para diferentes casos de uso:

- **Bridge**: É o tipo de rede padrão para contêineres Docker. Quando um contêiner é executado sem especificar uma rede, ele é automaticamente anexado a esta rede bridge padrão. Permite que contêineres conectados à mesma rede bridge se comuniquem, enquanto isola o tráfego de contêineres em redes bridge diferentes.

- **Host**: Ao usar a rede do tipo host, o contêiner compartilha a rede do host, removendo o isolamento entre a rede do contêiner e a rede do host, mas potencialmente oferecendo melhor desempenho para a comunicação de rede.

- **Overlay**: Usado em clusters Docker Swarm para permitir que contêineres em diferentes nós se comuniquem entre si. As redes overlay encapsulam o tráfego entre contêineres, permitindo que dados sejam compartilhados de forma segura entre contêineres em diferentes nós.

- **Macvlan**: Permite que você atribua um endereço MAC a um contêiner, fazendo com que pareça ser um dispositivo físico na rede. Isso é útil para casos em que você precisa de contêineres para aparecerem como dispositivos físicos reais para a rede externa.

- **None**: Isola completamente o contêiner da rede, oferecendo o maior nível de isolamento de rede para contêineres Docker.

### Gerenciamento de Redes

O Docker fornece várias ferramentas CLI para gerenciar redes, permitindo a criação, inspeção, listagem e remoção de redes. Alguns dos comandos mais comuns incluem:

- `docker network create`: Cria uma nova rede.
- `docker network ls`: Lista todas as redes.
- `docker network rm`: Remove uma ou mais redes.
- `docker network connect`: Conecta um contêiner a uma rede.
- `docker network disconnect`: Desconecta um contêiner de uma rede.

### Comunicação entre Contêineres

Contêineres na mesma rede podem se comunicar entre si usando o nome do contêiner como um hostname. O Docker resolve esses nomes para os endereços IP correspondentes internamente, facilitando a comunicação entre contêineres sem a necessidade de endereçar IPs diretamente.

### Isolamento e Segurança

O modelo de rede Docker oferece isolamento entre contêineres e entre contêineres e a rede externa, aumentando a segurança. As redes podem ser configuradas para limitar o acesso entre contêineres e para controlar o tráfego de entrada e saída, usando regras de firewall, por exemplo.

