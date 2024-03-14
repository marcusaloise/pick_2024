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


