
# Study — Kubernetes com Kind (Laboratório Prático)

Este diretório reúne um **estudo prático e incremental sobre fundamentos de Kubernetes**, realizado em um cluster local provisionado com [Kind](https://kind.sigs.k8s.io/).

O objetivo é **compreender a estrutura da plataforma e seus principais fluxos de entrega**, para apoiar o **mapeamento de riscos, pontos de controle e atividades de qualidade** em contextos onde o deploy e a execução de aplicações dependem de Kubernetes ou distribuições compatíveis (como OpenShift/ROSA).

Este laboratório funciona, portanto, como um **instrumento de assessment técnico para atuação em QA em ambientes orquestrados**, permitindo refletir sobre:
- onde e como qualidade pode ser incorporada nos fluxos de entrega,
- quais tipos de falhas e degradações são relevantes para o produto,
- e quais garantias podem (ou não) ser automatizadas nesse contexto.


---
## Escopo do laboratório

Este estudo cobre, de forma prática:

- ciclo de vida de cluster local
- workloads (Deployment, ReplicaSet, Pods)
- rede e exposição (Service, Endpoints)
- configuração e segredos
- governança de recursos e autoscaling
- resiliência
- observabilidade
- segurança (RBAC)
- políticas de admissão (Policy as Code)
- pipeline e validação de manifests (Kustomize / dry-run)

---
## Pré-requisitos

Ferramentas utilizadas durante o estudo:

- [kubectl](https://kubernetes.io/docs/reference/kubectl/introduction/)
- [Kind](https://kind.sigs.k8s.io/)

> O cluster é local e efêmero. Nada aqui assume ambiente cloud.

---
## Como usar este estudo

Este estudo **não deve ser lido de forma aleatória**.

A ordem dos arquivos reflete:
- dependência técnica
- evolução do entendimento
- complexidade crescente

Recomenda-se:
1. ler o arquivo
2. executar os comandos
3. observar o comportamento do cluster
4. cruzar com os conceitos em `docs/`

---
## Mapa do laboratório

### 01 — Cluster lifecycle
Criação, destruição e inspeção do cluster Kind.

📄 `01-cluster-lifecycle.md`

---
### 02 — Namespace e observação básica
Organização lógica e inspeção inicial de recursos e eventos.

📄 `02-namespace-and-observation.md`

---
### 03 — Workloads
Deployments, ReplicaSets, Pods, escala, rollout e rollback.

📄 `03-workloads.md`

---
### 04 — Networking
Service, seleção por labels e Endpoints.

📄 `04-networking.md`

---
### 05 — Configuração
ConfigMap, Secret e injeção de configuração nos Pods.

📄 `05-config-and-secrets.md`

---
### 06 — Recursos e Autoscaling
Requests, limits, métricas e HPA.

📄 `06-resources-and-autoscaling.md`

---
### 07 — Resiliência
PodDisruptionBudget e anti-affinity.

📄 `07-resilience.md`

---
### 08 — Observabilidade
Logs, logs estruturados e métricas via kubectl.

📄 `08-observability.md`

---
### 09 — Segurança (RBAC)
ServiceAccount, Role, RoleBinding e validação de permissões.

📄 `09-security-rbac.md`

---
### 10 — Policies e Admission Controller
Kyverno, políticas de segurança e validações em tempo de admissão.

📄 `10-policies-admission.md`

---
### 11 — Pipeline e GitOps
Kustomize, dry-run (client/server) e aplicação por overlay.

📄 `11-pipeline-gitops.md`

---
## Decisões e limitações conscientes

- Cluster local (Kind), não cloud
- Políticas de admissão dependem da versão do cluster
- Algumas falhas fazem parte do aprendizado e foram mantidas documentadas

> Este diretório documenta **a execução e o aprendizado**, não o código final.
