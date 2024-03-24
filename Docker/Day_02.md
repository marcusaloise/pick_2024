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
- `STOPSIGNAL`: Define o sinal de sistema para encerrar o container.
- `ONBUILD`: Adiciona uma instrução que será executada quando a imagem for usada como base para outra.
- 


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
> O "# 07" ta la pra baixo meu bacana!

```Dockerfile
# 08
FROM golang:1.18 as buildando
WORKDIR /app
COPY . ./
RUN go mod init hello
RUN go build -o /app/hello

FROM alpine:3.15.9
COPY --from=buildando /app/hello /app/hello
ENV APP="hello_word"
ARG GIROPOPS="strigus"
RUN echo "O giropops é: $GIROPOPS"
CMD ["/app/hello"]
```

```Dockerfile
# 09
FROM ubuntu:20.04
LABEL maintainer="marcusaloise@gmail.com"
WORKDIR /app
COPY . .
RUN apt-get update && apt-get install pip -y  && pip install --no-cache-dir -r requirements.txt && pip install flask redis prometheus_client && pip install --upgrade flask && apt-get install redis -y
ENV REDIS_HOST=127.0.0.1
EXPOSE 5000
CMD redis-server --daemonize yes && flask run --host=0.0.0.0
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

## Docker Hub

Docker Hub é um repositório público e privado de imagens que disponibiliza diversos recursos, como, por exemplo, sistema de autenticação, build automático de imagens, gerenciamento de usuários e departamentos de sua organização, entre outras funcionalidades.

Pessoas e empresas se juntam, criam seus containers seguindo as melhores práticas, testam tudo direitinho e depois disponibilizam lá para você usar sem ter nenhum trabalho. Isso é uma mão na roda gigantesca, uma vez que você não vai ter que perder tempo instalando coisas. Você também pode usá-lo para aprender como configurar determinado serviço. Para isso, basta ir no Docker Hub e procurar; provavelmente alguém já criou um container que você poderá usar pelo menos de base!


## Timeline: Criação de uma Imagem Docker

**Criação do Dockerfile**: O primeiro passo na criação de uma imagem Docker é criar um arquivo chamado Dockerfile. Esse arquivo contém as instruções necessárias para construir a imagem, como a escolha da base, a instalação de dependências e a configuração do ambiente.

**Build da Imagem**: Após a criação do Dockerfile, é necessário executar o comando docker build para iniciar o processo de construção da imagem. Nesse momento, o Docker irá ler o arquivo Dockerfile e compilar a imagem conforme as instruções especificadas.

**Tag da Imagem**: Após a conclusão do build, é possível adicionar tags à imagem para facilitar sua identificação e versionamento. Por exemplo, podemos adicionar uma tag indicando a versão da aplicação ou o ambiente de destino.

**Push da Imagem**: Para disponibilizar a imagem em um repositório remoto, é necessário utilizar o comando docker push. Esse comando envia a imagem para um registro, como o Docker Hub ou um registro privado, permitindo que outras pessoas possam baixar e utilizar a imagem.

**Verificação de Vulnerabilidades**: É importante garantir a segurança da imagem antes de disponibilizá-la para uso. Para isso, podemos utilizar ferramentas de verificação de vulnerabilidades, como o Trivy. Essas ferramentas analisam a imagem em busca de componentes com falhas de segurança conhecidas.

**Assinatura com o Cosign**: Para adicionar uma camada extra de segurança, é possível assinar digitalmente a imagem utilizando o Cosign. Essa assinatura garante a integridade da imagem, permitindo que os usuários verifiquem a autenticidade e a procedência da mesma.


