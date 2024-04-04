# Docker compose

O Docker Compose é uma ferramenta para definir e gerenciar aplicações multi-contêineres com Docker. Usando um arquivo YAML para configurar os serviços da aplicação, o Docker Compose permite que você crie e inicie todos os serviços definidos com um único comando. Isso simplifica o processo de configuração e gerencia

### Como Funciona o Docker Compose

O coração do Docker Compose é o arquivo `docker-compose.yml`. Neste arquivo, você define os serviços, redes e volumes que compõem sua aplicação. Cada serviço pode ser baseado em uma imagem Docker existente ou construído a partir de um `Dockerfile`, permitindo a personalização completa da sua aplicação.

### Principais Características do Docker Compose

- **Definição de Multi-Serviços**: Permite definir em um único arquivo todos os componentes da sua aplicação, incluindo serviços, bancos de dados, filas de mensagens, etc., junto com suas configurações específicas.

- **Isolamento**: Cada serviço definido no Docker Compose pode ser isolado em sua própria rede, facilitando a comunicação segura entre os serviços e minimizando as interferências externas.

- **Gerenciamento de Dependências**: Você pode especificar as dependências entre os serviços, garantindo que os serviços sejam iniciados na ordem correta.

- **Reprodução do Ambiente**: O Docker Compose garante que o ambiente de sua aplicação seja reproduzido de maneira consistente, independentemente de onde a aplicação é iniciada, eliminando o problema "funciona na minha máquina".

- **Escala**: Embora o foco principal do Docker Compose seja o ambiente de desenvolvimento, ele também suporta a escalabilidade dos serviços com simplicidade, permitindo aumentar ou diminuir o número de instâncias de serviço.

### Componentes Principais do Arquivo `docker-compose.yml`

- **Services**: Define os serviços que compõem a aplicação. Cada serviço é um contêiner com sua própria configuração, que pode incluir a construção de uma imagem a partir de um Dockerfile, mapeamento de portas, volumes, etc.

- **Networks**: Permite definir redes personalizadas para facilitar a comunicação entre os serviços.

- **Volumes**: Define os volumes usados pelos contêineres, permitindo a persistência de dados e compartilhamento de arquivos entre o host e o contêiner ou entre contêineres.

### Comandos Básicos do Docker Compose

- `docker-compose up`: Cria e inicia todos os serviços definidos no arquivo `docker-compose.yml`.
- `docker-compose down`: Para e remove todos os recursos criados pelo `docker-compose up`.
- `docker-compose build`: Constrói ou reconstrói serviços que têm `Dockerfiles`.
- `docker-compose logs`: Exibe os logs de todos os serviços.
- `docker-compose scale`: Permite alterar o número de contêineres para um serviço.

```yml
version: '3'

services:
  nginx:
     image: nginx
     ports:
        - "8080:80"
```