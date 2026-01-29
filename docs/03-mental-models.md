# Kubernetes — Atalhos Mentais

Esta página reúne atalhos de decisão para o uso cotidiano do Kubernetes.

> **Deployment decide como rodar.  
> Pod executa.  
> Service expõe.  
> Controller reconcilia.**

---
## Deployment
**Pergunta que responde:**  
👉 *Como a aplicação deve rodar?*

Responsabilidades mentais:
- define Pods
- define ReplicaSets
- define probes
- define estratégia de rollout
- mantém o estado desejado

> Pense no Deployment como o **contrato de execução da aplicação**.

---
## ReplicaSet
**Pergunta que responde:**  
👉 *Quantas instâncias devem existir agora?*

Responsabilidade mental:
- garantir N Pods idênticos rodando

---
## Pod
**Pergunta que responde:**  
👉 *Qual é a unidade real de execução?*

Responsabilidade mental:
- agrupar containers que precisam compartilhar:
  - IP
  - volumes
  - ciclo de vida

---
## Service
**Pergunta que responde:**  
👉 *Como acessar a aplicação na rede?*

Responsabilidades mentais:
- fornece DNS estável
- fornece IP virtual
- balanceia tráfego
- abstrai a volatilidade dos Pods

---
## Conexão Service → Pods

**Regra mental fundamental:**

- Service **não conhece Deployment**
- Service **não conhece ReplicaSet**
- Service **olha apenas para Pods**

---
## Endpoints
**Pergunta que responde:**  
👉 *Quem está realmente atrás do Service agora?*

Responsabilidade mental:
- lista dinâmica de Pods ativos associados ao Service

---
# Namespace
**Pergunta que responde:**  
👉 *Onde esse conjunto de objetivos vive?*

Responsabilidades mentais:
- organização
- separação
- governança
- escopo de políticas

---
