# Dia 07 - Statefulset e Services

## O que é Statefulset?

Os StatefulSets são uma funcionalidade do Kubernetes que gerencia o deployment e o scaling de um conjunto de Pods, fornecendo garantias sobre a ordem de deployment e a singularidade desses Pods.

Diferente dos Deployments e Replicasets que são considerados stateless (sem estado), os StatefulSets são utilizados quando você precisa de mais garantias sobre o deployment e scaling. Eles garantem que os nomes e endereços dos Pods sejam consistentes e estáveis ao longo do tempo.

## Quando usar StatefulSets?

Os StatefulSets são úteis para aplicações que necessitam de um ou mais dos seguintes:

 - Identidade de rede estável e única.
 - Armazenamento persistente estável.
 - Ordem de deployment e scaling garantida.
 - Ordem de rolling updates e rollbacks garantida.
 - Algumas aplicações que se encaixam nesses requisitos são bancos de dados, sistemas de filas e quaisquer aplicativos que necessitam de persistência de dados ou identidade de rede estável.