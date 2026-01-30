# Kubernetes — Conceitos Amplos

## O que é
Kubernetes é um **orquestrador de workloads containerizados**.

Ele não executa aplicações diretamente; ele **agenda, coordena e reconcilia** como essas aplicações devem rodar em um conjunto de máquinas.

Kubernetes:
- é **declarativo** (você descreve o estado desejado)
- é **reativo** (reconcilia continuamente o estado atual com o desejado)
- opera por meio de **controllers** que observam e atuam sobre recursos via API

---
## Para que serve
Kubernetes serve para:

- gerenciar workloads containerizados de forma **declarativa**
- executar aplicações distribuídas com **confiabilidade operacional**
- escalar workloads conforme demanda (quando configurado)
- suportar estratégias de rollout/rollback (quando utilizadas no fluxo de entrega)
- padronizar aspectos operacionais de execução em cluster (deploy, rede, configuração, acesso)

---
## Quando isso importa
Kubernetes se torna relevante quando:

- há **múltiplos serviços** e dependências entre componentes
- a **carga varia ao longo do tempo**
- há necessidade de **alta disponibilidade**
- falhas são esperadas e precisam ser toleradas com mecanismos de recuperação

---
## Kubernetes e Qualidade de Software
Kubernetes se conecta a práticas modernas de qualidade em sistemas distribuídos:

### CI/CD
Automação de build, teste e deploy, reduzindo erro humano e encurtando ciclos de feedback.

### GitOps
Git como **fonte de referência** do estado desejado do sistema, com reconciliação automatizada.

### Infrastructure as Code (IaC)
Infraestrutura versionada, auditável e reproduzível.

### SRE
Foco em confiabilidade do sistema:
- SLOs
- SLIs
- gestão de incidentes
- error budgets

### Supply Chain Security
Segurança do pipeline e dos artefatos:
- imagens
- dependências
- proveniência
- assinaturas

---
## Dois Planos que Coexistem no Kubernetes (modelo mental)

Este estudo utiliza um **modelo mental** com dois planos complementares para organizar a compreensão do Kubernetes.
Esse modelo não substitui a nomenclatura oficial (*control plane* e *data plane*), mas ajuda a separar **governança de estado** de **execução**.

---
### Plano Lógico (Governança do Estado)

Responsável por **definir e representar o estado desejado** e por viabilizar a **reconciliação** via controllers.

#### Estrutura lógica (visão por recursos)

```text
Cluster
└── Namespace
    ├── Workloads
    │   ├── Deployment
    │   │   └── ReplicaSet
    │   │       └── Pod(s)
    │   ├── StatefulSet
    │   │   └── Pod(s) com identidade
    │   └── Job / CronJob
    │
    ├── Services
    │   └── Selecionam Pods via labels
    │
    ├── Políticas e Controles (recursos e governança)
    │   ├── PodDisruptionBudget (PDB)
    │   ├── NetworkPolicy
    │   └── Admission Policies (quando aplicável)
    │
    └── RBAC
        ├── Roles / RoleBindings
        └── ServiceAccounts
```

**Características do plano lógico:**
 - não executa processos de aplicação
 - representa intenções e configurações
 - o estado desejado é registrado via API e persistido no banco de dados distribuído usado pelo Kubernetes para persistir o estado do cluster (etcd)
 - controllers observam o estado e atuam para convergência (via API Server)


---
### Plano Físico (Execução Real)

Responsável por **executar processos em máquinas reais**, gerenciar recursos e isolamento.

#### Estrutura física

```text
Cluster (conjunto de nodes sob uma mesma instância de controle K8s)
└── Node (VM ou máquina física)
    ├── CPU / Memória / Disco
    │
    └── Pod (unidade executável, efêmera)
        ├── IP próprio (Network Namespace)
        ├── Volumes
        │
        └── Containers (processos)
```

**Características do plano físico:**
 - executa processos reais (containers)
 - consome recursos computacionais
 - aplica isolamento (cgroups, namespaces)
 - é onde falhas de execução e saturação se manifestam

---
### Conexão entre os planos

O plano lógico governa o estado desejado, e o plano físico executa.

Controllers observam o estado atual e agem continuamente para aproximá-lo do estado desejado.

> Ajustes como autoscaling (HPA) são realizados por controllers específicos que observam métricas e atualizam o estado desejado (por exemplo, quantidade de réplicas), influenciando a execução no plano físico.
