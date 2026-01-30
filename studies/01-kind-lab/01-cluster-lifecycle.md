# Cluster lifecycle com kind

## Pré-requisitos

Ferramentas utilizadas neste estudo:

- [kubectl](https://kubernetes.io/docs/reference/kubectl/introduction/)
- [Kind](https://kind.sigs.k8s.io/)


**Ambiente de execução observado:**
- kubectl
  - Client Version: v1.35.0
  - Kustomize Version: v5.7.1
  - Server Version: v1.29.2

- kind
  - Version: v0.22.0

---
## Como usar este estudo

Este estudo **não deve ser consumido de forma aleatória**.

A ordem dos arquivos reflete:
- dependências técnicas entre os conceitos,
- progressão do entendimento sobre o funcionamento do cluster,
- aumento gradual de complexidade operacional.

Fluxo recomendado:
1. ler o conteúdo do arquivo,
2. executar os comandos propostos,
3. observar o comportamento,
4. relacionar as observações com os materiais conceituais em `docs/`.

O objetivo não é apenas executar comandos, mas **entender como o Kubernetes converge o estado declarado**.

---
## Operações básicas de cluster

Criar um cluster local:
```bash
kind create cluster --name <cluster_name>
```

Deletar cluster
```bash
kind delete cluster --name <cluster_name>
```

Verificar nodes:
```bash
kubectl get nodes
```
