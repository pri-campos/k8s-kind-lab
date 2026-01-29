# Quality Insights — Kubernetes

Em Kubernetes, **qualidade é propriedade do sistema** (aplicação + plataforma). A evidência de qualidade surge de:

1) **estado declarado** (manifests),  
2) **convergência do cluster** (controllers levando o real ao desejado), e  
3) **comportamento durante mudanças** (deploy, falhas, escala).

---
## 1. Propósito, escopo e premissas

### 1.1 O que é
Um **guia operacional de qualidade** para ambientes Kubernetes, com foco em pontos observáveis e verificáveis.

### 1.2 Para que serve
- Reduzir risco em **deploys**, **rollback** e **escala**.
- Padronizar **linguagem**, **critérios** e **evidências**.
- Aumentar previsibilidade de ambientes (reprodutibilidade).

### 1.3 Quando importa
- Migrações, janelas críticas, lançamentos, alto tráfego.
- Quando falhas “não são só do código”, mas do contrato com a plataforma K8s.

### 1.4 Escopo
Cobertura de qualidade em:
- **Antes do runtime** (manifests, políticas, pipeline).
- **Durante mudanças** (rollout, rollback).
- **Em runtime** (resiliência, saúde, escala, observabilidade).

### 1.5 Fora do escopo
- Modelagem de negócios, regras funcionais e testes de domínio.

### 1.6 Premissas do estudo original
- Cluster local (Kind), sem tráfego real e sem incidentes reais.

---
## 2. Modelo mental: dois eixos de qualidade

### 2.1 Eixo Aplicação
Evidência típica:
- unit tests
- API tests
- end-to-end tests
- testes de negócio
- entre outros ...

### 2.2 Eixo Plataforma Kubernetes
Evidência típica:
- manifests, controllers, rede/serviços, rollout, políticas, observabilidade, escala.

Esse documento foca no eixo Plataforma Kubernetes.

---
## 3. Critérios de qualidade por fase

### 3.1 Antes do runtime — Manifests e políticas
Objetivo: impedir erros previsíveis **antes** de entrar no cluster.

**Evidências:**
- manifests válidos e compatíveis (API versions).
- ausência de defaults perigosos.
- políticas de segurança e padrões aplicados.

### 3.2 Durante mudanças — Rollout/rollback
Objetivo: mudança confiável, sem degradação significativa.

**Evidências:**
- rollout respeita readiness probes.
- service mantém roteamento apenas para pods prontos.
- rollback possível e testado.

### 3.3 Em runtime — Resiliência e estabilidade
Objetivo: tolerar falhas esperadas sem impacto externo.

**Evidências:**
- reinícios dentro de limites aceitáveis.
- recuperação automática funciona.
- sinais (métricas/logs/eventos) suportam diagnóstico.

### 3.4 Em runtime — Escala e recursos
Objetivo: estabilidade e custo previsível sob carga.

**Evidências:**
- requests/limits aderentes ao consumo real.
- HPA escala antes da degradação relevante.
- ausência de thrashing.

### 3.5 Transversal — Governança e padrões
Objetivo: reduzir variação e ambiguidade organizacional.

**Evidências:**
- templates e convenções adotados.
- “critérios bloqueantes” definidos e automatizados.
- rastreabilidade por labels/annotations.

### 3.6 Transversal — Observabilidade
Objetivo: gerar evidência contínua e acionável de qualidade.

**Evidências:**
- logs estruturados e consistentes.
- métricas de SLIs coletadas e monitoradas.
- alertas com sinais e limiares úteis.

---
## 4. Checklist diário (para review e evidência)

### 4.1 Manifests (estático)
- [ ] YAML válido e schema válido (kubeconform/kubeval).
- [ ] APIs atuais (evitar versões obsoletas).
- [ ] Labels/annotations obrigatórias e consistentes.
- [ ] Imagem versionada (tag fixa ou digest), sem `latest`.
- [ ] requests/limits definidos.
- [ ] securityContext mínimo (non-root, filesystem read-only quando aplicável).
- [ ] ServiceAccount e RBAC mínimo funcional.

### 4.2 Deployment como contrato de execução
- [ ] readiness probe define “pronto para tráfego”.
- [ ] liveness probe evita reinício por latência normal ou dependências instáveis.
- [ ] estratégia de rollout explícita (maxUnavailable / maxSurge) quando crítico.
- [ ] PDB para serviços críticos (minAvailable/maxUnavailable).
- [ ] anti-affinity quando a concentração de réplicas aumenta risco.

### 4.3 Service/DNS/Discovery
- [ ] Service seleciona pods por labels corretas.
- [ ] Endpoints contém apenas pods prontos durante rollout.
- [ ] Nenhuma dependência direta de IP de Pod.

### 4.4 Observabilidade
- [ ] logs estruturados (timestamp, nível, serviço, trace/span quando disponível).
- [ ] métricas mínimas: erro, latência, saturação, reinícios.
- [ ] dashboards/alertas alinhados com SLOs.

---
## 5. Pipeline de qualidade (progressiva e automatizável)

### 5.1 Validação estática (shift-left)

Ferramentas usuais:
- `kubectl apply --dry-run=client|server`
- kubeconform/kubeval
- conftest (OPA) / Gatekeeper / Kyverno (validate)

Saída esperada:
- falhas bloqueadas em CI antes de merge/deploy.

### 5.2 Políticas e guardrails
Exemplos de políticas:
- proibir `latest`
- exigir requests/limits
- exigir non-root
- restringir capabilities
- impedir secrets hardcoded

### 5.3 Renderização e diffs
- Helm/Kustomize renderizam manifests.
- diffs previsíveis por ambiente.

### 5.4 Cluster efêmero
Objetivo: validar convergência e integração plataforma+app.

Evidências mínimas:
- pods Ready
- services resolvem DNS interno: `<service>.<namespace>.svc`
- endpoints corretos
- permissões mínimas funcionam (ServiceAccount + RBAC)

---
## 6. Teste de reprodutibilidade do ambiente (indicador de qualidade)

**Indicador:** tempo e previsibilidade para recriar ambiente.

Critérios:
- [ ] cluster sobe sem intervenção manual.
- [ ] manifests aplicam e convergem até Ready.
- [ ] smoke test passa (health, service discovery).
- [ ] ambiente descartável (recriar sob demanda).

Ambiente “frágil para recriar” aumenta risco operacional e reduz confiabilidade de testes e deploys.

---
## 7. Rollout: como verificar mudança confiável

### 7.1 Sinais objetivos
- novo ReplicaSet criado e progressão controlada.
- readiness controlando entrada no tráfego.
- endpoints mudando sem expor pods não prontos.
- rollback disponível.

### 7.2 Comandos úteis
```bash
kubectl rollout status deploy/<nome>
kubectl describe deploy/<nome>
kubectl get pods -w
kubectl get endpoints -w
kubectl get events --sort-by=.lastTimestamp
```

### 7.3 Probes como contrato (risco real)
Riscos típicos:
- readiness permissiva: expõe pod não pronto.
- ausência de readiness: indisponibilidade durante rollout.
- liveness agressiva: reinícios desnecessários.
- ausência de liveness: pod degradado permanece no ar.

Evidência exigida:
- pod só fica Ready quando realmente atende.
- rollout só avança após probes saudáveis.
- service não roteia tráfego para pods não prontos.

---
## 8. Promoção da mesma imagem entre ambientes (controle de variáveis)

**Regra de qualidade:** promover a **mesma imagem** entre QA → staging → produção.

Evidências:
- tag/digest idênticos entre ambientes.
- mudanças limitadas a ConfigMaps/Secrets/vars.
- ausência de rebuild por ambiente.

Anti-padrão:
- imagens diferentes por ambiente invalidam evidências obtidas antes da produção.

---
## 9. Runtime: resiliência e testes exploratórios controlados

### 9.1 Sinais de instabilidade
- reinícios frequentes (CrashLoopBackOff).
- falhas repetidas de liveness/readiness.
- eventos de OOMKilled, throttling recorrente.
- degradação progressiva em métricas (erro/latência).

### 9.2 Experimentos seguros (exploratórios)
- deletar pods e observar auto-recuperação.
- observar continuidade do Service e Endpoints.
- validar que readiness remove pods do tráfego.

Pergunta que guia:
> O sistema tolera falhas esperadas sem impacto externo relevante?

### 9.3 Contextos de alto risco (rigor maior)
Em períodos críticos (black friday, dia das mães, etc), ajustar o declarado:
- aumentar réplicas antecipadamente (capacidade não reativa).
- rollout mais conservador.
- PDB mais restritivo.
- anti-affinity para dispersar réplicas.
- congelamento/controle rigoroso de mudanças.

---
## 10. Escala e recursos: contrato eficiente

### 10.1 O que “eficiência do contrato” significa
Entregar comportamento esperado com consumo e risco controlados.

Sinais de contrato eficiente:
- requests/limits aderentes ao consumo real.
- ausência de OOMKill/throttling recorrentes.
- probes alinhadas ao startup/dependências/aquecimento.
- rollout previsível, sem 5xx persistente.
- sob carga, SLIs dentro do acordado.
- HPA estável (sem oscilações contínuas).

### 10.2 Como ajustar requests/limits (baseado em evidência)
Validar hipótese declarada observando:
- consumo médio e pico vs requests.
- throttling (CPU) e OOMKill (memória).
- reinícios associados a pressão de recursos.
- escala do HPA vs saturação e latência.

Sinais de desalinhamento:
- requests altos demais: desperdício.
- requests baixos demais: escala tardia.
- limits restritivos: instabilidade.
- sem limits: risco sistêmico (noisy neighbor).

---
## 11. Governança: acordos mínimos que evitam ambiguidade

### 11.1 Critérios bloqueantes para produção
- requests/limits definidos.
- readiness e liveness configuradas e validadas.
- estratégia de rollout definida para serviços críticos.
- políticas de segurança aplicadas.
- observabilidade mínima ativa.

### 11.2 Padrões e templates (mínimo viável)
Padronizar para reduzir variação:
- contrato base de Deployment (probes + recursos + securityContext).
- labels/annotations obrigatórias (app, env, version, owner).
- Service e ConfigMap/Secret consistentes.
- nomenclatura previsível.

### 11.3 Nomenclatura como mecanismo de qualidade
Nomes devem responder rapidamente:
- ambiente, propósito, criticidade.

Exemplo conceitual:
- cluster: `<ambiente>.<proposito>.<tipo>`
- namespace: `<dominio>-<app>-<ambiente>`

---
## 12. Segurança como prevenção (antes do runtime)

Critérios observáveis:
- secrets fora de manifests (Secret manager / Secret resources).
- RBAC mínimo funcional.
- policies ativas (Kyverno / OPA Gatekeeper).
- imagens versionadas e verificáveis.

---
## 13. Observabilidade como evidência de qualidade

### 13.1 Logs
Requisitos:
- estruturados, parseáveis, consistentes.
- correlação (request id / trace id quando aplicável).

### 13.2 Métricas (SLIs típicos)
- taxa de erro (5xx, timeouts).
- latência (p95/p99).
- saturação (CPU, memória).
- reinícios e causas (OOMKilled, liveness).
- disponibilidade do serviço.

### 13.3 SLOs (exemplos)
- 99,9% de requisições 2xx em 30 dias.
- latência p95 < 500ms.
- CPU < 80% (saturação) na maior parte do tempo.

---
## 14. Escolha correta de workload (redução de risco)

- **Deployment**: serviços stateless de longa duração (APIs, frontends, workers).
- **StatefulSet**: workloads com estado e identidade estável (ex.: bancos).
- **Job/CronJob**: execução batch e agendada.
- **DaemonSet**: um pod por node (agentes).

Escolher workload errado aumenta risco operacional e dificulta garantias.

---
## 15. Itens pendentes (TO DO)
- separar responsabilidades: “o que é declaração” vs “o que é garantia do control plane”.
- mapear defaults relevantes de Deployment quando campos não são declarados (risco de suposições).
- consolidar política mínima (OPA/Kyverno) para PoC.
