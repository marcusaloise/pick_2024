# A Arte da Minimização: Imagens Distroless e vulnerabilidade no docker


## O que é Distroless?

"Distroless" refere-se à estratégia de construir imagens de containers que são efetivamente "sem distribuição". Isso significa que elas não contêm a distribuição típica de um sistema operacional Linux, como shell de linha de comando, utilitários ou bibliotecas desnecessárias. Ao invés disso, essas imagens contêm apenas o ambiente de runtime (como Node.js, Python, Java, etc.) e o aplicativo em si.

### Benefícios do Distroless

O principal benefício das imagens Distroless é a segurança. Ao excluir componentes não necessários, o potencial de ataque é significativamente reduzido. Além disso, sem as partes extras do sistema operacional, o tamanho do container é minimizado, economizando espaço de armazenamento e aumentando a velocidade de deploy.

### Desafios do Distroless

Apesar de seus benefícios, a estratégia Distroless não está isenta de desafios. Sem um shell ou utilitários de sistema operacional, a depuração pode ser mais complicada. Além disso, a construção de imagens Distroless pode ser um pouco mais complexa, pois requer uma compreensão cuidadosa das dependências do aplicativo.

### Implementando Distroless

Existem várias maneiras de implementar uma estratégia Distroless. A mais simples é usar uma das imagens base Distroless fornecidas pelo Google e pela Chainguard. Estas imagens são projetadas para serem o mais minimalistas possível e podem ser usadas como ponto de partida para a construção de suas próprias imagens de container.

### Conclusão

Distroless representa uma evolução na maneira como pensamos sobre imagens de containers. Ele nos força a questionar o que realmente precisamos em nosso ambiente de execução e nos encoraja a minimizar o excesso. Embora a implementação de uma estratégia Distroless possa ser um desafio, os benefícios em termos de segurança e eficiência tornam essa uma consideração valiosa para qualquer equipe de desenvolvimento.

## Oque são as vuls no docker?

À medida que o desenvolvimento de software se torna cada vez mais orientado para containers, a segurança desses containers e das imagens usadas para criá-los ganha importância crítica. As imagens de container são construídas a partir de camadas de outras imagens e pacotes de software. Infelizmente, essas camadas e pacotes podem conter vulnerabilidades que tornam os containers e os aplicativos que eles executam vulneráveis a ataques. Aqui é onde o Docker Scout e o Trivy entra em cena.

### O que é o Docker Scout?
O Docker Scout é uma ferramenta de análise de imagens avançada oferecida pelo Docker. Ele foi projetado para ajudar desenvolvedores e equipes de operações a identificar e corrigir vulnerabilidades em suas imagens de containers. Ao analisar suas imagens, o Docker Scout cria um inventário completo dos pacotes e camadas, também conhecido como Software Bill of Materials (SBOM). Este inventário é então correlacionado com um banco de dados de vulnerabilidades atualizado continuamente para identificar possíveis problemas de segurança.

### O que é o Trivy?

Trivy é uma ferramenta de segurança de código aberto usada para escanear contêineres de software em busca de vulnerabilidades de segurança. Desenvolvido em Go, o Trivy verifica imagens de contêineres em busca de vulnerabilidades conhecidas em bibliotecas e pacotes de software comuns. Ele examina as dependências de software dentro dos contêineres em busca de falhas de segurança relatadas publicamente e fornece aos usuários relatórios detalhados sobre as vulnerabilidades encontradas. O Trivy é frequentemente usado por desenvolvedores e equipes de operações de TI para garantir que suas imagens de contêineres sejam seguras antes de serem implantadas em ambientes de produção.

