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

> Primeiro compose apenas com nginx
```yml
version: '3'

services:
  nginx:
     image: nginx
     ports:
        - "8080:80"
```

> Subindo a aplicação giropops senhas com o compose
```yml
version: '3'
services:
  app:
    image: marcusaloise/giropops-senhas:2.0
    ports:
      - "5000:5000"
    networks: 
      - strigus
    environment:
      REDIS_HOST: redis
  redis:
    image: redis
    ports:
      - "6379:6379"
    networks:
      - strigus
      
networks:
  strigus:
    driver: bridge 

```

> Criando volumes
```yml
version: '3'
services:
  app:
    image: marcusaloise/giropops-senhas:2.0
    ports:
      - "5000:5000"
    networks: 
      - strigus
    environment:
      REDIS_HOST: redis
    volumes:
      - girus:/girus

  redis:
    image: redis
    ports:
      - "6379:6379"
    networks:
      - strigus
    volumes:
      - girus:/girus    
networks:
  strigus:
    driver: bridge
volumes:
  girus:

```
> Buildando a imagem pelo docker compose
```yml
version: '3'
services:
  app:
    build: ./Dockerfile-app
    ports:
      - "5000:5000"
    networks: 
      - strigus
    environment:
      REDIS_HOST: redis
    volumes:
      - girus:/girus

  redis:
    image: redis
    ports:
      - "6379:6379"
    networks:
      - strigus
    volumes:
      - girus:/girus    
networks:
  strigus:
    driver: bridge
volumes:
  girus:

 ```

> Utilizando deploy para limitar recursos
```yml
version: '3'
services:
  app:
    build: ./Dockerfile-app
    ports:
      - "5000:5000"
    networks: 
      - strigus
    environment:
      REDIS_HOST: redis
    volumes:
      - girus:/girus
    deploy: 
      replicas: 1
      resources:      
        reservations:
          cpus: '0.25'
          memory: 128M
        limits:
          cpus: '0.5'
          memory: 256M

    depends_on:
            - redis    
  redis:
    image: redis
    networks:
      - strigus
    volumes:
      - girus:/girus
    deploy:
      replicas: 3
      resources:
        reservations:
          cpus: '0.25'
          memory: 128M
        limits:
          cpus: '0.5'
          memory: 256M
 
networks:
  strigus:
    driver: bridge
volumes:
  girus:

```
> Adicionando healthcheck

```yml
version: '3'
services:
  app:
    build: ./Dockerfile-app
    ports:
      - "5000:5000"
    networks: 
      - strigus
    environment:
      REDIS_HOST: redis
    volumes:
      - girus:/girus
    deploy: 
      replicas: 1
      resources:      
        reservations:
          cpus: '0.25'
          memory: 128M
        limits:
          cpus: '0.5'
          memory: 256M
    depends_on: 
      - redis  
    
  redis:
    image: redis
    command: redis-server --appendonly yes
    networks:
      - strigus
    volumes:
      - girus:/girus
    deploy:
      replicas: 1
      resources:
        reservations:
          cpus: '0.25'
          memory: 128M
        limits:
          cpus: '0.5'
          memory: 256M
    healthcheck: 
      test: ["CMD", "redis-cli", "ping"]
      interval: 30s
      timeout: 5s
      retries: 3
      start_period: 10s
 
networks:
  strigus:
    driver: bridge
volumes:
  girus:
```

> Criando um docker compose mais avançado

```yml
version: '3'
services:
  app:
    build: ./Dockerfile-app
    ports:
      - "5000:5000"
    networks: 
      - strigus
    environment:
      REDIS_HOST: redis
    env_file:
      - .env      
    volumes:
      - type: volume
        source: girus
        target: /girus 
    depends_on:
      - redis
    labels:
      io.linuxtips.description: "Giropops senhas App"
      io.linuxtips.version: "1.0"
        
    deploy: 
      replicas: 1
      update_config:
        parallelism: 1
        delay: 10s        
      resources:      
        reservations:
          cpus: '0.25'
          memory: 128M
        limits:
          cpus: '0.5'
          memory: 256M
      restart_policy:
        condition: on-failure
        delay: 5s
        max_attempts: 3
    dns:
      - 8.8.8.8
      - 8.8.4.4   
        
    
  redis:
    image: redis
    command: redis-server --appendonly yes
    networks:
      - strigus
    volumes:
      - girus:/girus
    deploy:
      replicas: 1
      resources:
        reservations:
          cpus: '0.25'
          memory: 128M
        limits:
          cpus: '0.5'
          memory: 256M
    healthcheck: 
      test: ["CMD", "redis-cli", "ping"]
      interval: 30s
      timeout: 5s
      retries: 3
      start_period: 10s
    labels:
      io.linuxtips.description: "Redis server"
      io.linuxtips.version: "Latest"
 
networks:
  strigus:
    driver: bridge
    ipam:
      driver: default
      config:
        - subnet: "172.20.0.0/16"
    labels:
      io.linuxtips.network: "Strigus Network"      
volumes:
  girus:
    driver: local    
    driver_opts:
      type: "none"  
      device: /home/elliot/docker
      o: "bind"
    labels:
      io.linuxtips.volume: "Girus volume"      
```

> Desafio da aula

```yml
version: '3.8'
services:
  giropops-senhas:
    image: marcusaloise/giropops-senhas:2.0
    container_name: app-giropops-senhas-1
    ports: 
      - "5000:5000"
    networks:
      - giropops
    env_file:
      - .env
    volumes:
      - type: volume
        source: strigus 
        target: /strigus
    depends_on:
      - redis
    environment:
      REDIS_HOST: redis
    labels:
      com.docker,description: "Giropops"
    deploy:      
      restart_policy:
        condition: unless-stopped
        delay: 60s
      resources:
        reservations:
          cpus: '0.01'
          memory: 16M
  redis:
    image: redis
    container_name: app-redis-1 
    command: redis-server --appendonly yes
    networks:
      - giropops
    volumes:
      - strigus:/strigus 

networks:
  giropops:
    driver: bridge

volumes:
  strigus:  
    driver: local

```