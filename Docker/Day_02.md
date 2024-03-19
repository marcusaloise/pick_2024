# Criação de dockerfile // docker images

## Oque é um docker image

Basicamente um container parado

utiliza um fhs (Onion FHS) em camadas para cada instrução dada pro container 

 
 -- nova camada RW (read and write)

 -- copy nginx.conf (COPY)

 -- Apt-get update && apt-get install nginx (RUN)

 -- Base Image (FROM)

```Dockerfile
# 01
FROM ubuntu:18.04
RUN apt-get update && apt-get install nginx -y
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

```Dockerfile
# 02
FROM ubuntu:18.04
RUN apt-get update && apt-get install nginx -y && rm -rf var/lib/apt/lists/*
EXPOSE 80
COPY index.html /var/www/html/
CMD ["nginx", "-g", "daemon off;"]
WORKDIR /var/www/html
ENV APP_VERSION 1.0.0
```

```Dockerfile
# 03
FROM ubuntu:18.04
label maintainer="marcusaloise@gmail.com"
RUN apt-get update && apt-get install nginx -y && rm -rf var/lib/apt/lists/*
EXPOSE 80
COPY index.html /var/www/html/
WORKDIR /var/www/html
ENV APP_VERSION 1.0.0
ENTRYPOINT ["nginx"]
CMD ["-g", "daemon off;"]
```

```Dockerfile
# 04
FROM ubuntu:18.04
label maintainer="marcusaloise@gmail.com"
RUN apt-get update && apt-get install nginx curl -y && rm -rf var/lib/apt/lists/*
EXPOSE 80
ADD https://github.com/prometheus/node_exporter/releases/download/v1.7.0/node_exporter-1.7.0.linux-amd64.tar.gz /root/node-exporter
COPY index.html /var/www/html/
WORKDIR /var/www/html
ENV APP_VERSION 1.0.0
ENTRYPOINT ["nginx"]
CMD ["-g", "daemon off;"]
HEALTHCHECK  --timeout=2s CMD curl -f localhost || exit 1
```

```Dockerfile
# 05
FROM ubuntu:18.04
label maintainer="marcusaloise@gmail.com"
RUN apt-get update && apt-get install nginx curl -y && rm -rf var/lib/apt/lists/*
EXPOSE 80
ADD https://github.com/prometheus/node_exporter/releases/download/v1.7.0/node_exporter-1.7.0.linux-amd64.tar.gz /root/node-exporter
COPY index.html /var/www/html/
WORKDIR /var/www/html
ENV APP_VERSION 1.0.0
ENTRYPOINT ["nginx"]
CMD ["-g", "daemon off;"]
HEALTHCHECK  --timeout=2s CMD curl -f localhost || exit 1
```
- `FROM`: Especifica a imagem base que você deseja usar.
- `LABEL`: Define metadados para a imagem, como o mantenedor.
- `RUN`: Executa comandos durante a construção da imagem.
- `EXPOSE`: Expõe portas do contêiner para comunicação com o host.
- `WORKDIR`: Define o diretório de trabalho dentro do contêiner.
- `ADD`: Adiciona arquivos ou URLs ao sistema de arquivos do contêiner.
- `COPY`: Copia arquivos do host para o contêiner.
- `ENV`: Define variáveis de ambiente dentro do contêiner.
- `ENTRYPOINT`: Define o comando padrão a ser executado quando o contêiner for iniciado.
- `CMD`: Fornece argumentos padrão para o `ENTRYPOINT`.
- `HEALTHCHECK`: Verifica o estado da aplicação dentro do contêiner.
- `VOLUME`:Define um volume a ser montado no container.
- `MAINTAINER`: Autor da imagem.
- `USER`: Determina qual usuário será utilizado na imagem. Por default é o root
