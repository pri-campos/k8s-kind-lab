# Kubernetes — Conceitos Amplos

## O que é
Kubernetes é um **orquestrador de workloads containerizados**.

Ele não executa aplicações diretamente, mas **coordena, governa e reconcilia** como elas devem rodar em um conjunto de máquinas.

Kubernetes:
- **não é puramente hierárquico**
- é **declarativo** (você descreve o estado desejado)
- é **reativo** (reconcilia continuamente o estado atual com o desejado)

---
## Para que serve
Kubernetes serve para:

- gerenciar workloads containerizados de forma **declarativa**
- executar aplicações distribuídas com **confiabilidade**
- escalar automaticamente conforme a carga
- realizar deploy contínuo sem downtime
- padronizar a operação de infraestrutura

---
## Quando isso importa
Kubernetes se torna relevante quando:

- há **múltiplos microsserviços**
- a **carga varia ao longo do tempo**
- há necessidade de **alta disponibilidade**
- falhas são esperadas e precisam ser toleradas

---
## Kubernetes e Qualidade de Software
Kubernetes se conecta diretamente a práticas modernas de qualidade:

### CI/CD
Automação de build, teste e deploy, reduzindo erro humano e tempo de feedback.

### GitOps
Git como **fonte da verdade** do estado desejado do sistema.

### Infrastructure as Code (IaC)
Infraestrutura versionada, auditável e reproduzível  

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
## Dois Planos que Coexistem no Kubernetes

Kubernetes opera simultaneamente em **dois planos complementares**:

---
### Plano Lógico (Governança do Estado)

Responsável por **definir, governar e reconciliar o estado desejado** do sistema.

#### Estrutura lógica

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
    ├── Controllers
    │   ├── Deployment Controller
    │   ├── ReplicaSet Controller
    │   ├── StatefulSet Controller
    │   └── HPA Controller
    │
    ├── Services
    │   └── Selecionam Pods via labels
    │
    ├── Policies
    │   ├── HPA
    │   ├── PDB
    │   ├── NetworkPolicy
    │   └── Admission Policies
    │
    └── RBAC
        └── ServiceAccounts
```

**Características do plano lógico:**
 - Não executa processos
 - Define intenções
 - É mantido por controllers
 - Vive no etcd

---
### Plano Físico (Execução Real)

Responsável por **executar processos em máquinas reais**, gerenciar recursos e isolamento.

#### Estrutura física
```text
Cluster
└── Node (VM ou máquina física)
    ├── CPU / Memória / Disco
    │
    └── Pod (menor unidade executável, efêmera)
        ├── IP próprio (Network Namespace)
        ├── Volumes
        │
        └── Containers (processos)
```
**Características do plano físico:**
 - Executa processos reais
 - Consome recursos computacionais
 - Aplica isolamento (cgroups, namespaces)
 - É onde falhas realmente acontecem

---
### Conexão entre os planos

O **plano lógico governa**, o **plano físico executa**.

Controllers observam o estado real e agem continuamente para aproximá-lo do estado desejado.
