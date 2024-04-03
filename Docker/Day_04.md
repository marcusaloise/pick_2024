# O que são Docker Volumes?

Os volumes Docker são o mecanismo preferido e mais usado para persistir dados gerados por e usados por contêineres Docker. Diferentemente dos dados armazenados na camada de escrita de um contêiner, que podem ser perdidos quando o contêiner é excluído, os volumes são completamente gerenciados pelo Docker e existem de forma independente dos contêineres, o que significa que os volumes permitem que os dados persistam mesmo quando os contêineres são removidos.

volume = bind
volume = volume

```bash
docker run -ti --name testando-volumes --mount type=bind,source=/home/elliot/app,target=/giropops-senhas debian
```

```bash
docker run -ti --name testando-volumes --mount type=bind,source=/home/elliot/app,target=/giropops-senhas,readonly debian
```

```bash
Docker volume ls 
Docker volume create
Docker volume rm
Docker volume inspect
```

```bash
docker run -d --name web-1 -v giropops:/usr/share/nginx/html -p 8080:80 nginx
```

### Por que usar Docker Volumes?

1. **Persistência de Dados**: Eles garantem que os dados importantes não sejam perdidos quando um contêiner é destruído ou recriado.
2. **Compartilhamento de Dados**: Os volumes podem ser compartilhados entre vários contêineres, facilitando a comunicação e o compartilhamento de dados entre diferentes partes de uma aplicação.
3. **Desacoplamento de Dados e Aplicações**: Separar os dados da aplicação em si permite que você trate os dados e os contêineres de aplicação de maneira independente.
4. **Performance**: O acesso aos dados através de volumes é geralmente mais rápido do que armazenar dados na camada de escrita do contêiner.

### Tipos de Volumes

Existem vários tipos de volumes em Docker, cada um com suas características próprias:

- **Volumes Gerenciados pelo Docker**: São os mais simples de usar e são completamente gerenciados pelo Docker. Eles são armazenados em uma parte do sistema de arquivos do host que é gerenciada pelo Docker (`/var/lib/docker/volumes` no Linux). Você cria e gerencia esses volumes com comandos do Docker como `docker volume create` e `docker volume rm`.

- **Bind Mounts**: Permitem que qualquer pasta no sistema do host seja montada dentro de um contêiner. Isso é útil para desenvolvimento e para situações onde você precisa de acesso direto a uma parte do sistema de arquivos do host dentro de um contêiner.

- **tmpfs Mounts**: Permitem montar um sistema de arquivos temporário na memória dentro de um contêiner. Isso pode ser usado para dados temporários que não precisam ser persistidos após o contêiner ser fechado.

### Gerenciando Docker Volumes

Os volumes são criados e gerenciados usando a interface de linha de comando (CLI) do Docker ou através da API do Docker. Alguns comandos úteis incluem:

- `docker volume create` para criar um novo volume.
- `docker volume ls` para listar todos os volumes.
- `docker volume inspect` para obter informações detalhadas sobre um volume específico.
- `docker volume rm` para remover um volume.

### Boas Práticas

- Use volumes para persistir dados importantes e para compartilhar dados entre contêineres.
- Prefira volumes gerenciados pelo Docker quando possível, pois eles são mais fáceis de usar e oferecem melhor integração com o Docker.
- Seja cauteloso ao usar bind mounts, já que eles podem causar problemas de segurança e de compatibilidade entre diferentes ambientes.

Volumes são uma ferramenta poderosa no Docker, permitindo que você gerencie dados de maneira eficaz, mantendo-os seguros e acessíveis, independentemente do ciclo de vida dos seus contêineres.