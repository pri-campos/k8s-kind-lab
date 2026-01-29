# Glossário

Este glossário define os principais termos usados ao longo do repositório **k8s-kind-lab**.  
Ele funciona como um **contrato semântico**, garantindo precisão conceitual e evitando ambiguidades.

---
## Objetos Fundamentais

### Cluster
Conjunto de Nodes gerenciados por um **Control Plane**.

É a **unidade administrativa** do Kubernetes.

---
### Node
Máquina física ou virtual responsável por executar Pods.

Um Node contém:
- runtime de containers
- kubelet
- kube-proxy

---
### Pod
Menor unidade executável do Kubernetes.

Um Pod encapsula **um ou mais containers** que:
- compartilham **IP**, **portas** e **volumes**
- são **efêmeros**
- **não escalam sozinhos**

> Um Pod representa uma **instância momentânea de execução**, não a aplicação em si.

---
## Workloads e Controle de Execução

### Workloads
Conjunto de objetos que executam aplicações para gerar valor de negócio.

Exemplos:
- Deployment
- StatefulSet
- Job
- CronJob
- DaemonSet

---
### Deployment
Objeto declarativo que define o **estado desejado de uma aplicação stateless**.

Responsabilidades:
- descreve **como a aplicação deve rodar**
- gerencia **ReplicaSets**
- controla **rollout** e **rollback**
- permite **escala**
- versiona a aplicação

> Deployment é um **contrato de execução**, não o executor direto.

---
### ReplicaSet
Objeto responsável por garantir que **N Pods idênticos** estejam sempre rodando.

Características:
- mantém a quantidade correta de Pods
- não gerencia versões
- não realiza rollout ou rollback

> Normalmente **não é criado diretamente**, mas pelo Deployment.

---
## Rede e Exposição

### Service
Abstração de rede estável que:
- fornece **DNS estável**
- fornece **IP virtual** (ex: ClusterIP)
- realiza **balanceamento de carga automático**
- desacopla consumidores do ciclo de vida dos Pods

> Service abstrai a volatilidade dos Pods.

---
### Ingress
Mecanismo **declarativo** que expõe Services internos via **HTTP/HTTPS** para fora do cluster.

Permite:
- roteamento por **host**
- roteamento por **path**
- configuração de TLS

> Requer um **Ingress Controller** para funcionar.

---
## Configuração e Segurança

### ConfigMap
Armazena **configurações não sensíveis**, como:
- variáveis de ambiente
- arquivos de configuração

---
### Secret
Armazena **dados sensíveis**, como:
- senhas
- tokens
- chaves privadas

> Base64 **não é criptografia**.

---
### ServiceAccount
Identidade associada a um **Pod/processo** para autenticação dentro do cluster.

---
### RBAC (Role-Based Access Control)
Modelo de autorização que define:
- **quem** pode fazer
- **o quê**
- **em quais recursos**

---
## Escalabilidade e Resiliência

### HPA (Horizontal Pod Autoscaler)
Ajusta automaticamente o número de réplicas de um workload com base em métricas,
normalmente CPU ou memória.

---
### PDB (Pod Disruption Budget)
Define quantos Pods podem ficar indisponíveis simultaneamente durante
**interrupções voluntárias** (ex: manutenção de Nodes).

---
### Anti-Affinity
Regra de agendamento que **espalha Pods** entre Nodes para evitar:
- ponto único de falha
- concentração de carga

---
### Probes
Mecanismos de verificação de estado de containers.

#### Readiness Probe
Indica se o Pod está **pronto para receber tráfego**.

#### Liveness Probe
Indica se o container está saudável ou se deve ser **reiniciado** pelo Kubernetes.

---
## Políticas e Governança

### Admission Controller
Camada que intercepta requisições antes de serem persistidas no cluster.

Funciona como o **porteiro do Kubernetes**, validando ou rejeitando recursos.

Ferramentas comuns:
- OPA / Gatekeeper
- Kyverno

---
### Policy as Code
Prática de definir políticas de segurança e governança como **código versionado**.

---
## Conceitos Arquiteturais

### Stateless
Aplicações que **não dependem de estado local** para funcionar.

O estado é mantido externamente (ex: banco de dados, cache distribuído).

---
### Manifest
Arquivo YAML que descreve o **estado desejado** do cluster de forma declarativa.

---
### Control Plane
Conjunto de componentes responsáveis por:
- validar requisições
- decidir
- agendar
- reconciliar o estado do cluster

> O Control Plane **não executa workloads**, apenas os orquestra.
