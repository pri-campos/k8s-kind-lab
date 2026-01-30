# k8s-kind-lab

Este diretório reúne um estudo prático e exploratório sobre Kubernetes, realizado em ambiente local com kind, com foco na compreensão da plataforma e de seus principais fluxos de entrega e execução.

O objetivo é apoiar o mapeamento de riscos, pontos de controle e atividades de qualidade em contextos onde o deploy e a operação de aplicações dependem de Kubernetes ou distribuições compatíveis (por exemplo, OpenShift/ROSA).

O repositório não pressupõe leitura sequencial: o aprendizado ocorre por experimentação prática, com consulta conceitual sob demanda, conforme dúvidas e hipóteses surgem.

O laboratório funciona como um instrumento de assessment técnico para atuação em QA em ambientes orquestrados, permitindo refletir sobre onde qualidade pode ser incorporada, quais falhas e degradações são relevantes e quais garantias são viáveis nesse contexto, reconhecendo seus limites.

---
## Como usar este repositório

### 0) Fundação conceitual (opcional, mas recomendada)

Caso você **ainda não esteja familiarizado** com os conceitos fundamentais de Kubernetes, inicie por este material.  
Se já possuir esse repertório, avance para a próxima seção.

- [01 — Core Concepts](docs/01-core-concepts.md)  
  → formação de vocabulário técnico mínimo e referências conceituais iniciais.

---
### 1) Aprendizado prático (eixo principal)

Esta é a **linha central do estudo**.  
Os exercícios hands-on têm como objetivo **expor comportamentos da plataforma** para apoiar a identificação de **riscos, pontos de controle e oportunidades de qualidade**.

Essa prática ocorre **em paralelo** com:
- o glossário (para consulta de termos),
- modelos mentais (para organização cognitiva),
- e o artefato de *quality insights* (para registro analítico).

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

Esses materiais **não são pré-requisitos formais**, mas devem ser utilizados sempre que surgirem dúvidas durante a prática.

- [02 — Glossary](docs/02-glossary.md)  
  → vocabulário e termos técnicos

- [03 — Mental Models](docs/03-mental-models.md)  
  → modelos mentais para apoiar a compreensão da plataforma

- [04 — Quality Insights](docs/04-quality-insights.md)  
  → registro analítico de observações e hipóteses relacionadas à qualidade

---
## Estrutura do repositório

- `docs/` → documentação conceitual e material de apoio
- `studies/` → aprendizado prático guiado (hands-on)
- `manifests/` → manifests Kubernetes utilizados nos experimentos

---
## Licença

Consulte: [LICENSE](LICENSE)
