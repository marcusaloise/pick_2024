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
