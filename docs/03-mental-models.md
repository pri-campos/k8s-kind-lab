# Kubernetes — Atalhos Mentais

Esta página reúne **atalhos mentais** para apoiar decisões e raciocínio cotidiano
sobre o funcionamento do Kubernetes.  
Eles não descrevem a arquitetura completa, mas ajudam a reduzir carga cognitiva.

> **Deployment define a intenção.  
> Pod executa.  
> Service expõe.  
> Controller reconcilia.**

---
## Deployment
**Pergunta que responde:**  
👉🏾 *Como a aplicação deve rodar?*

Responsabilidades mentais:
- define o **template de Pods**
- define ReplicaSets
- define probes
- define estratégia de rollout
- mantém o estado desejado

> Pense no Deployment como o **contrato de execução da aplicação**.

---
## ReplicaSet
**Pergunta que responde:**  
👉🏾 *Quantas instâncias devem existir agora?*

Responsabilidade mental:
- garantir que **N Pods idênticos** estejam em execução,
  conforme definido pelo Deployment

---
## Pod
**Pergunta que responde:**  
👉🏾 *Qual é a unidade real de execução?*

Responsabilidade mental:
- agrupar containers que precisam compartilhar:
  - IP
  - volumes
  - ciclo de vida

---
## Service
**Pergunta que responde:**  
👉🏾 *Como acessar a aplicação na rede?*

Responsabilidades mentais:
- fornece DNS estável
- fornece IP virtual
- distribui tráfego
- abstrai a volatilidade dos Pods

---
## Conexão Service → Pods
**Regra mental fundamental:**

- Service **não conhece Deployment**
- Service **não conhece ReplicaSet**
- Service **seleciona Pods** com base em labels e estado

---
## Endpoints
**Pergunta que responde:**  
👉🏾 *Quem está realmente atrás do Service agora?*

Responsabilidade mental:
- manter a lista dinâmica de Pods elegíveis para tráfego,
  considerando seleção e readiness

---
## Namespace
**Pergunta que responde:**  
👉🏾 *Onde esse conjunto de objetivos vive?*

Responsabilidades mentais:
- organização
- separação
- governança
- escopo de políticas
