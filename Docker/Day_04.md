# volumes no Docker

O volume e criado para persistir os dados dentro de um container caso ele morra, assim quando ele voltar os dados estarão acessíveis

volume = bind
volume = volume


docker run -ti --name testando-volumes --mount type=bind,source=/home/elliot/app,target=/giropops-senhas debian

docker run -ti --name testando-volumes --mount type=bind,source=/home/elliot/app,target=/giropops-senhas,readonly debian

Docker volume ls 
Docker volume create
Docker volume rm
Docker volume inspect


docker run -d --name web-1 -v giropops:/usr/share/nginx/html -p 8080:80 nginx


