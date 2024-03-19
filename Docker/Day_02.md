# Criação de dockerfile // docker images

## Oque é um docker image

Basicamente um container parado

utiliza um fhs (Onion FHS) em camadas para cada instrução dada pro container 

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

```Dockerfile
# 06
FROM nginx:alpine
RUN apk update && apk add curl
label maintainer="marcusaloise@gmail.com"
EXPOSE 80
COPY index.html /usr/share/nginx/html
WORKDIR /var/www/html
ENV APP_VERSION 1.0.0
ENTRYPOINT ["nginx"]
CMD ["-g", "daemon off;"]
HEALTHCHECK  --timeout=2s CMD curl -f localhost || exit 1
```
### Multistage Dockerfile

O Docker suporta a construção de imagens Docker em múltiplas etapas, permitindo que você crie imagens mais leves e eficientes. O multistage build é útil quando você precisa compilar código-fonte ou realizar outras tarefas durante a construção da imagem, mas não quer incluir essas dependências na imagem final.

#### Como funciona:

1. **Construção em etapas**: O Dockerfile é dividido em várias etapas, cada uma com suas próprias instruções e contexto de construção. Cada etapa pode copiar arquivos ou executar comandos, resultando em uma imagem intermediária.

2. **Imagem final**: A última etapa do Dockerfile especifica a imagem final que será criada a partir dos artefatos das etapas anteriores. A imagem final geralmente é menor e contém apenas o necessário para executar a aplicação.


```Dockerfile
#07
FROM golang:1.18 as buildando
WORKDIR /app
COPY . ./
RUN go mod init hello
RUN go build -o /app/hello

FROM alpine:3.15.9
COPY --from=buildando /app/hello /app/hello
CMD ["/app/hello"]
```

```golang
package main

import "fmt"

func main() {

    fmt.Println("GIROPOPS STRIGUS GIRUS - LINUXTIPS")

}
```
Neste exemplo, a primeira etapa compila o código Go e cria o executável `hello.go`. A segunda etapa usa uma imagem Alpine Linux mínima como base e copia o executável `hello.go` da primeira etapa, resultando em uma imagem final leve e pronta para uso.


#### Benefícios:

- Redução do tamanho da imagem: A imagem final contém apenas os artefatos necessários para executar a aplicação, sem as dependências de compilação.
- Maior segurança: Menos componentes na imagem reduzem a superfície de ataque e a vulnerabilidade.
- Eficiência no CI/CD: Menos etapas de construção e imagens menores resultam em tempos de construção mais rápidos e implantações mais rápidas.

	O multistage build é uma técnica poderosa para otimizar o tamanho e o desempenho das suas imagens Docker, especialmente para aplicações complexas com muitas dependências de compilação.

