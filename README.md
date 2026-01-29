# k8s-kind-lab

Laboratório prático para aprender Kubernetes de forma **estrutural, observável e progressiva**, usando Kind (cluster local).

Este repositório não segue leitura linear.  
O aprendizado acontece por **prática + consulta conceitual sob demanda**.

---
## Como usar este repositório (ordem recomendada)

### 0) Fundação mínima (leitura inicial obrigatória)

Antes de qualquer prática, leia apenas:

- [01 — Core Concepts](docs/01-core-concepts.md)  

> Objetivo: formar vocabulário básico e modelo mental.
> Não é para decorar, é para saber “o que existe”.

---
### 1) Aprendizado prático (eixo principal)

A partir daqui, o aprendizado acontece **nos studies**:

📂 `studies/01-kind-lab/`

- [01 — Cluster Lifecycle](studies/01-kind-lab/01-cluster-lifecycle.md)
- [02 — Namespace and Observation](studies/01-kind-lab/02-namespace-and-observation.md)
- [03 — Workloads](studies/01-kind-lab/03-workloads.md)
- [04 — Networking](studies/01-kind-lab/04-networking.md)
- [05 — Config and Secrets](studies/01-kind-lab/05-config-and-secrets.md)
- [06 — Resources and Autoscaling](studies/01-kind-lab/06-resources-and-autoscaling.md)
- [07 — Resilience](studies/01-kind-lab/07-resilience.md)
- [08 — Observability](studies/01-kind-lab/08-observability.md)
- [09 — Security / RBAC](studies/01-kind-lab/09-security-rbac.md)
- [10 — Policies / Admission](studies/01-kind-lab/10-policies-admission.md)
- [11 — Pipeline / GitOps](studies/01-kind-lab/11-pipeline-gitops.md)

---
### 2) Documentação de apoio (consulta contínua)

Estes documentos **não devem ser lidos em sequência**.  
Use-os sempre que surgir dúvida durante a prática.

- [02 — Glossary](docs/02-glossary.md)  
  → para vocabulário e termos técnicos

- [03 — Mental Models](docs/03-mental-models.md)
  → mapa mental para apoiar no entendimento

- [04 — Quality Insights](docs/04-quality-insights.md)  
  → para entender impacto real em deploy, estabilidade, escala e produção

---
## Estrutura do repositório

- `docs/` → documentação conceitual e de apoio
- `studies/` → aprendizado prático guiado (hands-on)
- `manifests/` → Kubernetes puro (infra limpa usada nos estudos)

---
## Licença
Consulte: [LICENSE](LICENSE)
