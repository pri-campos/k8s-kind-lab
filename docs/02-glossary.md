# Glossário

Este glossário define os principais termos usados ao longo do repositório **k8s-kind-lab**.  

---
## Objetos Fundamentais

### Cluster
Conjunto de **Nodes** sob um mesmo **Control Plane** Kubernetes.

É a **unidade administrativa** do Kubernetes, responsável por agrupar recursos
e aplicar políticas de governança e controle.

---
### Node
Máquina física ou virtual responsável por executar **Pods**.

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

> Um Pod representa uma **instância de execução**, não a aplicação como conceito lógico.

---
## Workloads e Controle de Execução

### Workloads
Conjunto de objetos declarativos que descrevem **como aplicações devem ser executadas**
no cluster.

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
- versiona a configuração da aplicação

> Deployment define intenção; a execução ocorre por meio de controllers.

---
### ReplicaSet
Objeto responsável por garantir que **N Pods idênticos** estejam em execução.

Características:
- mantém a quantidade correta de Pods
- não gerencia versões
- não realiza rollout ou rollback

> Normalmente **não é criado diretamente**, mas gerado e controlado por um Deployment.

---
### Controller
Componente responsável por **observar o estado atual do cluster** e **agir para
aproximá-lo do estado desejado**.

Características:
- opera de forma contínua (reconciliação)
- reage a mudanças nos recursos via API Server
- não executa workloads diretamente

Exemplos:
- Deployment Controller
- ReplicaSet Controller
- StatefulSet Controller
- HPA Controller

> Controllers são o **mecanismo central de automação e convergência** do Kubernetes.

---
## Rede e Exposição

### Service
Abstração de rede estável que:
- fornece **DNS estável**
- fornece **IP virtual** (ex: ClusterIP)
- realiza **balanceamento de carga**
- desacopla consumidores do ciclo de vida dos Pods

> Service abstrai a volatilidade dos Pods.

---
### Ingress
Mecanismo declarativo que expõe **Services internos** via **HTTP/HTTPS**.

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

> Base64 **não representa criptografia**.

---
### ServiceAccount
Identidade associada a um **Pod/processo** para autenticação dentro do cluster.

---
### RBAC (Role-Based Access Control)
Modelo de autorização que define:
- **quem** pode agir
- **o quê** pode ser feito
- **em quais recursos**

---
## Escalabilidade e Resiliência

### HPA (Horizontal Pod Autoscaler)
Recurso que ajusta automaticamente o número de réplicas de um workload
com base em métricas observadas (por exemplo, CPU ou memória).

A ação de escala é realizada por um **controller específico**.

---
### PDB (Pod Disruption Budget)
Define quantos Pods podem ficar indisponíveis simultaneamente durante
**interrupções voluntárias** (ex: manutenção de Nodes).

---
### Anti-Affinity
Regra de agendamento que **distribui Pods** entre Nodes para reduzir:
- pontos únicos de falha
- concentração de carga

---
### Probes
Mecanismos usados para verificar o estado de containers.

#### Readiness Probe
Indica se o Pod está **pronto para receber tráfego**.

#### Liveness Probe
Indica se o container deve ser **reiniciado** pelo Kubernetes.

---
## Políticas e Governança

### Admission Controller
Camada que intercepta requisições **antes de serem persistidas** no cluster.

Usada para:
- validação
- mutação
- aplicação de políticas

Ferramentas comuns:
- OPA / Gatekeeper
- Kyverno

---
### Policy as Code
Prática de definir políticas de segurança e governança como **código versionado**,
avaliado automaticamente no fluxo de entrega.

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
- persistir estado
- agendar workloads
- coordenar a reconciliação do cluster

> O Control Plane **não executa workloads**, mas governa sua execução.
