# Day 01 - O que é Container?

### Container e uma forma de isolar recursos para um determinado motivo.
 
 
agrupamento de uma aplicação junto com suas dependências, que compartilham o kernel do sistema operacional do host, ou seja, da máquina (virtual ou física) onde está rodando.

Containers são bem similares às máquinas virtuais, porém mais leves e mais integrados ao sistema operacional da máquina host, uma vez que, como já dissemos, compartilha o seu kernel, o que proporciona melhor desempenho por conta do gerenciamento único dos recursos.

Quando estamos utilizando máquinas virtuais, nós emulamos um novo sistema operacional e virtualizamos todo o seu hardware utilizando mais recursos da máquina host, o que não ocorre quando utilizamos containers, pois os recursos são compartilhados. O ganho óbvio disso é a capacidade de rodar mais containers em um único host, se comparado com a quantidade que se conseguiria com máquinas virtuais.

> [!IMPORTANT] 
> na máquina virtual você emula um novo sistema operacional dentro do sistema operacional do host. Já no container você emula somente as aplicações e suas dependências tornando-o portátil.

O que é vai ser isolado?

Todos os recurso da maquina para que cada aplicação tenha o seu ambiente e que não interfira em outras aplicações.

Container == isolamento 

Cgroups == isolamento de recursos

Namespaces:

    - Filesystem
    - Processos PID
    - Network
    - Users

Cgroups (Isolamento de Recursos)

    - CPU
    - Memory

### Descomplicando namespace:

O que e um namespace?

Namespaces foram adicionados no kernel Linux na versão 2.6.24 e são eles que permitem o isolamento de processos quando estamos utilizando o Docker. São os responsáveis por fazer com que cada container possua seu próprio environment, ou seja, cada container terá a sua árvore de processos, pontos de montagens, etc., fazendo com que um container não interfira na execução de outro

>Pratica:

```bash
sudo apt-install debootstrap
```

Baixar o fhs de uma distro linux:

```bash
sudo debootstrap stable /debian http://deb.debian.org/debian
```
Ele vai baixar todo o fhs dentro de /debian

Depois de baixar todos os arquivos do debian podemos criar um namespace para ele utilizando o unshare

```
unshare --help

Usage:
 unshare [options] [<program> [<argument>...]]

Run a program with some namespaces unshared from the parent.

Options:
 -m, --mount[=<file>]      unshare mounts namespace
 -u, --uts[=<file>]        unshare UTS namespace (hostname etc)
 -i, --ipc[=<file>]        unshare System V IPC namespace
 -n, --net[=<file>]        unshare network namespace
 -p, --pid[=<file>]        unshare pid namespace
 -U, --user[=<file>]       unshare user namespace
 -C, --cgroup[=<file>]     unshare cgroup namespace

 -f, --fork                fork before launching <program>
 -r, --map-root-user       map current user to root (implies --user)

 --kill-child[=<signame>]  when dying, kill the forked child (implies --fork)
                             defaults to SIGKILL
 --mount-proc[=<dir>]      mount proc filesystem first (implies --mount)
 --propagation slave|shared|private|unchanged
                           modify mount propagation in mount namespace
 --setgroups allow|deny    control the setgroups syscall in user namespaces

 -R, --root=<dir>           run the command with root directory set to <dir>
 -w, --wd=<dir>     change working directory to <dir>
 -S, --setuid <uid>         set uid in entered namespace
 -G, --setgid <gid>         set gid in entered namespace

 -h, --help                display this help
 -V, --version             display version

```

para criar a nossa namespace com o fhs baixado do debian faça o seguinte:

```
root@root:~$ unshare --mount --uts --ipc --net --map-root-user --user --pid --fork chroot /debian bash
```

### O que é Cgroups?

Cgroups é um recurso do kernel do Linux que limita, contabiliza e isola o uso de recursos de um processo. Como memoria e CPU.

> cada vez que subimos um namespace ele sobe como um processo e podemos limitar recursos usando o Cgroups.


### Copy-On-Write com ele funciona?

Uma imagem de container e apenas read only
como se você tivesse um livro e que fosse permitido fazer anotações nele caso quisesse, porém, cada vez que você estivesse prestes a tocar a página com a caneta, de repente alguém aparecesse, tirasse uma xerox dessa página e entregasse a cópia para você. É exatamente assim que o Copy-On-Write funciona.

Basicamente, significa que um novo recurso, seja ele um bloco no disco ou uma área em memória, só é alocado quando for modificado.

Docker usa um esquema de camadas, ou layers, e para montar essas camadas são usadas técnicas de Copy-On-Write. Um container é basicamente uma pilha de camadas compostas por N camadas read-only e uma, a superior, read-write.


### Docker e sua realçao com o kernel

O Docker utiliza algumas features básicas do kernel Linux para seu funcionamento.

### Instalação do Docker

```
# To install the latest stable versions of Docker CLI, Docker Engine, and their
# dependencies:
#
# 1. download the script
#
#   $ curl -fsSL https://get.docker.com -o install-docker.sh
#
# 2. verify the script's content
#
#   $ cat install-docker.sh
#
# 3. run the script with --dry-run to verify the steps it executes
#
#   $ sh install-docker.sh --dry-run
#
# 4. run the script either as root, or using sudo to perform the installation.
#
#   $ sudo sh install-docker.sh
#
# Command-line options
```

### Como deixar um container em execução

`crtl + p + q`

para entrar em um container em execução execute o seguinte comando no terminal

``` bash
root@root:~$ docker container attach container_id
```

### como ver as metricas e logs?

``` bash
root@root:~$ docker container stats
```
Podemos ver o uso de recursos do container 

``` bash
root@root:~$ docker container logs --details container_id
```
Podemos ver os logs de comandos feitos dentro do container


### Como posso inspecionar um container?

``` bash
root@root:~$ docker container inspect container-id
```

fornece informações detalhadas sobre construções controladas pelo Docker.


### criando um container Dettached e container exec

``` bash
root@root:~$ docker container run -d --name nginx nginx
```
`Utilizamos o parametro -d para ele rodar em background (dettached)`

E como eu posso entrar nesse container nginx? 

Vamos ter que rodar outro comando para entrar nele com o terminal.

``` bash
root@root:~$ docker container exec -ti <container id> bash
```
``perceba que não fizemos o attach no container e sim o exec, por conta que o container não estava executando o bash no momento que tentamos conectar nele e assim temos que utilizar o comando exec para executar o bash e conectar nele utilizando o -ti`

``` bash
root@root:~$ docker container run -d -p 8080:80 --name nginx nginx
```
Perceba que agora vamos utilizar o parametro -p para bindar a porta do nosso container 80 no nosso host 8080
