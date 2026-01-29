# Quality Insights — Kubernetes

## 1. Propósito deste documento

Este documento consolida um **mapeamento inicial de aspectos de qualidade observáveis em ambientes Kubernetes**, a partir de um estudo prático realizado em cluster local (Kind).

O foco não é explicar Kubernetes em profundidade, mas **registrar como decisões e comportamentos da plataforma impactam diretamente a qualidade do sistema**, especialmente em termos de deploy, disponibilidade, escala, segurança e governança.

Trata-se de um estudo **embrionário**, limitado a ambiente local e sem carga real de produção, cujo objetivo é:
- apoiar leitura crítica de ambientes Kubernetes em projetos futuros
- identificar pontos onde QA pode atuar de forma objetiva
- aproximar práticas de teste da realidade de sistemas orquestrados

---
## 2. Qualidade em sistemas que utilizam Kubernetes

Kubernetes define:
- como a aplicação é implantada
- como falhas são absorvidas
- como tráfego é direcionado
- como escala acontece
- quais riscos são bloqueados antes do runtime

Portanto, parte da qualidade do sistema emerge:
- do **estado declarado** (manifests)
- da **convergência do cluster**
- do **comportamento durante mudanças** (deploys, falhas, escala)

Esses aspectos coexistem com os testes tradicionais da aplicação, não os substituem.

---
## 3. Dois eixos complementares de verificação de qualidade

Em ambientes Kubernetes, avaliações de qualidade passam a ocorrer em dois eixos que se cruzam:

### Eixo da aplicação
Permanece válido e necessário:
- testes unitários
- testes de API
- testes end-to-end
- testes funcionais e de negócio
- entre outros ...

### Eixo da plataforma
Passa a ser relevante:
- manifests YAML
- comportamento do cluster
- rollout e rollback
- políticas e guardrails
- observabilidade e escala

Este documento trata **principalmente do segundo eixo**, sem perder a conexão com o primeiro.

---
## 4. Validações estáticas de manifests (antes de rodar)

Assim como código é analisado antes de ser executado, manifests Kubernetes também podem ser validados previamente.

O que verificar:
- YAML válido
- versões de API corretas
- campos obrigatórios presentes
- ausência de defaults perigosos
- conformidade com padrões definidos pelo time

Ferramentas típicas:
- `kubectl apply --dry-run=client|server`
- kubeconform / kubeval
- conftest (OPA)
- kyverno (modo validate)

Essas validações costumam rodar em CI e representam **alto retorno de qualidade com baixo custo**.

---
## 5. Pipeline de qualidade para manifests Kubernetes

Em ambientes Kubernetes, grande parte da garantia de qualidade acontece
antes do runtime, por meio de uma pipeline de validação progressiva.

Uma pipeline típica inclui:

1. Validação estática
   - lint de YAML
   - validação de schema e versões de API
   - conformidade com padrões de naming, labels e annotations

2. Políticas e guardrails (shift-left)
   - exigência de requests/limits
   - restrições de segurança (non-root, filesystem read-only)
   - bloqueio de imagens `latest`
   - políticas como código (OPA, Kyverno)

3. Renderização e diffs
   - renderização de manifests (Kustomize / Helm)
   - verificação de erros de compilação
   - diffs previsíveis entre ambientes

4. Teste em cluster efêmero
   - aplicação dos manifests em cluster temporário
   - smoke tests: sobe, responde health, service discovery
   - validação de permissões mínimas

Essa abordagem permite bloquear falhas cedo, reduzir riscos no deploy
e tornar o comportamento do sistema mais previsível.

---
## 6. Testes em cluster efêmero (integração com a plataforma)

Aplicar manifests em um cluster temporário (Kind, k3d) permite observar se o sistema **converge e se mantém estável**.

Aspectos observáveis:
- Pods entram em estado Ready
- ReplicaSets são criados corretamente
- Services selecionam Pods por label
- DNS interno resolve (`<service>.<namespace>.svc`)
- permissões mínimas funcionam (ServiceAccount + RBAC)

Ferramentas e práticas:
- `kubectl get pods / events / endpoints`
- `kubectl exec` para testes internos
- scripts simples usando `kubectl` + `curl`

### Tempo de recriação de ambientes como indicador de qualidade

Em ambientes Kubernetes, a capacidade de recriar um ambiente de forma rápida
e previsível é um indicador relevante de qualidade sistêmica.

Aspectos observáveis:
- tempo necessário para criar um cluster funcional
- tempo para aplicar manifests e atingir estado Ready
- previsibilidade do processo (sem ajustes manuais)
- possibilidade de descarte e recriação sob demanda

Ambientes que demoram a ser recriados ou exigem intervenções manuais
aumentam o risco operacional e reduzem a confiabilidade de testes e deploys.

Mesmo em estudos locais (ex: Kind), observar esse tempo ajuda a avaliar
o grau de reprodutibilidade e maturidade da configuração.


Esse tipo de verificação se aproxima de um **teste de integração sistêmica**, agora envolvendo cluster + aplicação.

---
## 7. Observação de rollout e mudanças declarativas

Pontos de atenção:
- criação de novo ReplicaSet
- substituição gradual de Pods
- comportamento do Service durante a troca
- respeito às readiness probes
- possibilidade de rollback

Verificações práticas:
- `kubectl rollout status`
- `kubectl describe deployment`
- `kubectl get endpoints -w`

Observar a **confiabilidade da mudança**, não apenas resultado final.

### Health checks como mecanismo de garantia de qualidade

Em Kubernetes, health checks (readiness e liveness probes) não servem para validar
regras de negócio, mas para definir **quando a aplicação está operacionalmente apta**
a participar do sistema.

Durante o deploy, esses checks influenciam diretamente:
- o avanço do rollout
- a entrada de novos Pods no tráfego
- a remoção de Pods instáveis
- a decisão de rollback em caso de falha

Do ponto de vista de qualidade, probes mal definidas representam risco real:
- readiness permissiva demais pode expor Pods não prontos
- ausência de readiness pode causar indisponibilidade durante rollout
- liveness incorreta pode gerar reinícios desnecessários
- ausência de liveness pode manter Pods degradados em execução

Aspectos observáveis para QA:
- Pods só entram em Ready quando realmente aptos
- Service não direciona tráfego para Pods não prontos
- rollout só avança após probes saudáveis
- falhas durante startup são absorvidas sem impacto externo

### Promoção da mesma imagem entre ambientes

Uma garantia fundamental de qualidade em Kubernetes é que a **mesma imagem de container**
seja executada em diferentes ambientes (qa, staging, produção), variando apenas
configuração e segredos.

Essa prática permite:
- reprodutibilidade de comportamento
- validação progressiva da mesma versão
- rollback confiável
- redução de diferenças artificiais entre ambientes

Do ponto de vista de QA, aspectos observáveis incluem:
- tag ou digest da imagem idênticos entre ambientes
- mudança apenas em ConfigMaps, Secrets ou variáveis
- ausência de rebuild de imagem por ambiente

Executar imagens diferentes por ambiente reduz a confiabilidade dos testes
e enfraquece qualquer evidência de qualidade obtida antes da produção.


Nesse processo, o Service deve permanecer estável, mesmo com a troca de Pods e IPs.
A observação de Endpoints (`kubectl get endpoints -w`) permite verificar se o tráfego
continua sendo direcionado apenas para Pods prontos, sem impacto externo.

Health checks funcionam, portanto, como um **contrato explícito entre aplicação e plataforma**,
e são um dos principais mecanismos de garantia de qualidade do deploy em Kubernetes.

---
## 8. Comportamento em runtime e resiliência

Em execução, o cluster oferece sinais objetivos de saúde.

O que observar:
- reinícios frequentes de Pods
- falhas de liveness
- readiness removendo Pods do tráfego
- recriação automática após falha

Testes exploratórios possíveis:
- deletar Pods manualmente
- observar se o Service permanece disponível
- validar atualização dinâmica de Endpoints

Esses testes ajudam a responder:
> O sistema tolera falhas esperadas sem impacto externo?

Existem cenários em que o risco sistêmico é previsivelmente maior, como:

- eventos sazonais de alto tráfego (exemplo: Black Friday, dias das mães, etc)
- grandes lançamentos
- migrações de infraestrutura
- janelas críticas de negócio

Nesses contextos, o ciclo de deploy permanece o mesmo, mas os **critérios de rigor aumentam**.

Em Kubernetes, isso se traduz em ajustes declarativos, por exemplo:
- aumento prévio de réplicas (capacidade não reativa)
- estratégia de rollout mais conservadora
- PodDisruptionBudget mais restritivo
- uso explícito de anti-affinity para workloads críticos
- controle rigoroso ou congelamento de mudanças durante o período

A taxa e a frequência de reinícios de Pods também funcionam como um indicador
objetivo de estabilidade em runtime.

Reinícios recorrentes associados a falhas de liveness podem indicar:
- dependências indisponíveis
- problemas de startup ou aquecimento
- limites de recursos inadequados
- falhas intermitentes de código

Do ponto de vista de qualidade, é possível definir limites aceitáveis
para reinícios em um intervalo de tempo, tratando esse sinal como um
SLO interno de estabilidade operacional.


Esses ajustes não representam um novo tipo de Deployment.
Eles são variações conscientes do mesmo mecanismo, aplicadas a um contexto de risco maior.

---
## 9. Escala, recursos e impacto sistêmico

Definições de requests, limits e HPA influenciam diretamente:
- latência
- estabilidade
- custo

Aspectos verificáveis:
- Pods saturam CPU ou memória?
- HPA reage conforme esperado?
- escala ocorre antes da degradação?
- limites impedem efeito “noisy neighbor”?

### Eficiência do contrato de execução (Deployment) por serviço

O Deployment funciona como um contrato: ele define como o serviço roda, escala e se mantém estável.
Eficiência, aqui, significa: entregar o comportamento esperado com consumo de recursos e risco controlados.

Sinais práticos de que o contrato está eficiente:
- requests/limits compatíveis com o consumo real (sem excesso e sem estrangulamento)
- ausência de throttling/oomkill recorrentes e reinícios frequentes
- readiness e liveness alinhadas ao comportamento do serviço (startup, dependências, aquecimento)
- rollout conclui de forma previsível, sem 5xx e sem instabilidade prolongada
- sob carga, latência e taxa de erro permanecem dentro dos limites acordados (SLIs/SLOs)
- HPA escala de forma estável (sem oscilações contínuas) e com impacto de custo conhecido

Verificações que ajudam QA a coletar evidência:
- observar rollout e tempo até estabilizar
- correlacionar reinícios, readiness e eventos com mudanças
- rodar teste de carga controlado e observar saturação/latência/erro
- revisar diffs de manifests: mudanças de recursos, probes e estratégia de rollout

### Medição e ajuste de requests e limits

Requests e limits não são apenas valores declarativos.
Eles representam uma hipótese sobre o consumo real do serviço.

Aspectos observáveis para validar essa hipótese:
- consumo médio e de pico de CPU/memória em relação aos requests
- ocorrência de throttling (CPU) ou OOMKill (memória)
- reinícios de Pods associados a pressão de recursos
- comportamento do serviço sob carga controlada
- relação entre escala do HPA e saturação de recursos

Sinais de desalinhamento incluem:
- requests muito acima do consumo real (desperdício de capacidade)
- requests muito abaixo do consumo médio (escalas tardias)
- limits muito restritivos causando instabilidade
- ausência de limits permitindo impacto sistêmico em outros workloads

A observação contínua desses sinais permite ajustar o contrato
de execução de forma mais eficiente e previsível.

Testes de carga aqui deixam de ser apenas sobre performance e passam a ser sobre **comportamento adaptativo do sistema**.

---
## 10. Governança, padrões e acordos de time

Em um projeto real, qualidade em Kubernetes depende de acordos explícitos.

Exemplos de acordos:
- todo Deployment define readiness e requests/limits
- a mesma imagem é promovida entre ambientes
- apenas configuração muda entre qa/staging/prod
- nomenclatura consistente para cluster, namespace e services
- uso consciente de Deployment, StatefulSet, Job, DaemonSet
- Serviços críticos definem PDB (minAvailable/maxUnavailable) para reduzir risco em interrupções voluntárias (manutenção/drain).
- Workloads replicados críticos usam pod anti-affinity para evitar concentração de réplicas no mesmo Node.
- A/B testing normalmente não é resolvido pelo Deployment puro; costuma depender de camada de tráfego (Ingress avançado/service mesh) ou feature flags.

### Padrões e templates para PoC

Mesmo em provas de conceito, definir templates mínimos reduz ambiguidade
e facilita avaliação de qualidade.

Para a PoC, vale estruturar templates que padronizem:
- nomenclatura de cluster e namespace
- contrato básico de Deployment
- definição de requests/limits
- probes de saúde mínimas
- labels e annotations obrigatórias

Esses templates não visam maturidade completa, mas **consistência observável**.

### Contrato de execução (Deployment)

O Deployment funciona como o contrato de execução de um serviço em Kubernetes.

Ele explicita:
- como a aplicação inicia
- quando pode receber tráfego
- como escala
- como reage a falhas
- como é atualizada

Do ponto de vista de qualidade, esse contrato deve ser legível, previsível
e adequado ao tipo de serviço executado.

### Formas de uso do Deployment

O Deployment pode ser utilizado de formas distintas, dependendo do perfil do serviço:

- serviços stateless de longa duração
- APIs e backends
- frontends
- workers que consomem filas
- serviços que precisam escalar horizontalmente
- serviços que exigem rollout controlado

Cada uso impõe requisitos diferentes para probes, recursos e estratégia de atualização.

### Quando não usar Deployment

Nem toda carga de trabalho deve ser executada como Deployment.

Casos em que Deployment não é a escolha adequada:
- bancos de dados com estado persistente (StatefulSet)
- processamento pontual ou batch (Job)
- tarefas agendadas (CronJob)
- agentes que precisam rodar em todos os Nodes (DaemonSet)

Escolher o workload incorreto aumenta risco operacional e dificulta garantias de qualidade.

### Estratégia de atualização e probes de saúde

A estratégia de atualização define como novas versões substituem versões antigas
sem causar impacto ao sistema.

As probes de saúde (readiness e liveness) fazem parte dessa estratégia, pois:
- controlam entrada e saída de Pods do tráfego
- influenciam o avanço do rollout
- determinam quando um Pod deve ser reiniciado

Probes inadequadas comprometem diretamente a confiabilidade do deploy.

### Descontinuação de serviços

A descontinuação de um serviço também faz parte do contrato de execução.

Boas práticas incluem:
- remover tráfego antes de encerrar Pods
- preservar logs e métricas por um período definido
- garantir que o Deployment seja removido apenas após estabilização
- evitar exclusões abruptas que causem impacto sistêmico

Descontinuar um serviço sem considerar seu contrato de execução
introduz risco equivalente a um deploy mal conduzido.

### Padrões e templates para PoC

Mesmo em provas de conceito, definir templates mínimos ajuda a reduzir variação
e facilita a avaliação de qualidade.

Para a PoC, vale estruturar templates que padronizem:
- estrutura básica de Deployment
- definição obrigatória de requests/limits
- probes mínimas de saúde
- labels e annotations essenciais
- uso consistente de Service e ConfigMap

Esses templates não visam maturidade completa, mas **previsibilidade e leitura clara**.

### Uso de labels como apoio à qualidade

Labels não servem apenas para seleção de Pods.
Elas também apoiam gestão de qualidade, observabilidade e governança.

Usos relevantes:
- seleção correta de Pods por Services
- agrupamento para métricas e logs
- rastreabilidade de versão e ambiente
- escopo de políticas e automações

Labels mal definidas aumentam risco de roteamento incorreto
e dificultam avaliação de saúde do sistema.

### Service como ponto fixo de acesso (sem dependência de IP de Pod)

Em Kubernetes, Pods são efêmeros e seus IPs não devem ser usados como dependência.

A garantia de estabilidade ocorre pelo uso correto de Services, que:
- fornecem DNS estável
- mantêm um ponto fixo de acesso
- balanceiam tráfego apenas para Pods saudáveis
- refletem dinamicamente o estado real via Endpoints

Do ponto de vista de qualidade, é essencial verificar que:
- não existe dependência direta de IP de Pod
- Services apontam apenas para Pods prontos
- mudanças de Pods não impactam o acesso ao serviço

### Padrões de nomenclatura de recursos

Padrões de nomenclatura reduzem ambiguidade e facilitam leitura operacional.

Convenções comuns:
- Service: `-svc`
- Ingress: `-ingress`
- ConfigMap: `-config`

Nomes consistentes ajudam na navegação do cluster,
na leitura de manifests e na resposta a incidentes.

### Padrões e templates para PoC

Mesmo em provas de conceito, a ausência de padrões mínimos dificulta
a avaliação de qualidade e gera variações difíceis de comparar.

Para a PoC, vale estruturar templates que garantam:
- definição explícita de requests/limits
- probes mínimas de saúde
- uso consistente de labels
- configuração externa via ConfigMap e Secret
- políticas básicas de segurança aplicáveis

Esses templates estabelecem um ponto de partida comum
para evolução posterior do ambiente.

### Governança de custo e impacto do HPA

O HPA não atua apenas sobre desempenho, mas também sobre custo de cloud.

Aspectos de qualidade a serem considerados:
- escalas frequentes aumentam consumo e custo
- ausência de limites máximos de réplicas gera custo imprevisível
- métricas instáveis provocam oscillação de escala (thrashing)

Do ponto de vista de qualidade, o uso do HPA deve:
- ter limites superiores definidos conscientemente
- ser observado sob carga controlada
- ter impacto financeiro conhecido ou estimado

### Critérios mínimos para deploy em produção

Alguns critérios devem ser tratados como bloqueantes para produção.

Exemplos:
- nenhum deploy segue para produção sem requests e limits definidos
- probes de saúde configuradas e validadas
- estratégia de rollout definida
- políticas de segurança aplicadas

A ausência desses critérios reduz a previsibilidade
e aumenta o risco operacional.

### Políticas de segurança e resiliência

Políticas de admissão e configuração fazem parte da garantia de qualidade.

Critérios comuns:
- proibição de imagens com tag `latest`
- exigência de execução como non-root
- definição de PodDisruptionBudget para serviços críticos
- uso de pod anti-affinity para evitar concentração de réplicas

Essas políticas reduzem risco sistêmico
e previnem falhas antes do runtime.

### Observabilidade mínima de logs

Logs fazem parte da evidência de qualidade do sistema.

Requisitos mínimos:
- logs estruturados
- campos parseáveis como timestamp, nível e serviço
- consistência de formato entre instâncias

Logs não estruturados dificultam diagnóstico
e reduzem a capacidade de análise de falhas.

### SLOs e SLIs como contrato de confiabilidade

Em ambientes Kubernetes, qualidade também se expressa
por meio de acordos explícitos de confiabilidade.

Exemplo de SLO:
- 99,9% das requisições retornam HTTP 2xx em 30 dias
- latência p95 inferior a 500ms
- saturação de CPU abaixo de 80%

Esses objetivos orientam decisões de escala,
capacidade e priorização de correções.


### Padrões de nomenclatura como mecanismo de qualidade

Em ambientes Kubernetes, nomes não são apenas identificadores técnicos.
Eles funcionam como **orientação operacional** e reduzem ambiguidade em
deploys, monitoramento e resposta a incidentes.

Um padrão comum para nomes de cluster permite responder rapidamente:
- onde estou? (ambiente)
- para que esse cluster existe? (propósito)
- que tipo de risco ele representa? (local, cloud, experimental, produção)

Exemplo conceitual:
- `<ambiente>.<proposito>.<tipo>`

Da mesma forma, o nome do namespace deve deixar claro:
- para quem o sistema roda
- para qual domínio ou aplicação
- qual o escopo lógico de responsabilidade

Nomes ambíguos aumentam risco operacional e dificultam avaliações de qualidade,
especialmente em cenários de múltiplos clusters e ambientes.


Esses acordos reduzem ambiguidade e facilitam avaliação de qualidade.

---
## 11. Segurança e políticas como prevenção

Parte da qualidade se manifesta antes do runtime.

Critérios observáveis:
- ausência de secrets hardcoded
- RBAC mínimo funcional
- políticas de admissão ativas
- imagens versionadas (sem `latest`)

Ferramentas:
- Kyverno
- OPA / Gatekeeper
- Conftest em CI

---
## 12. Observabilidade como evidência

Logs e métricas não são apenas operacionais.

Eles fornecem evidência para:
- SLIs
- SLOs
- degradação progressiva
- comportamento sob carga

Exemplo de indicadores:
- taxa de erro
- latência p95
- reinícios de Pods
- saturação de recursos

---
## 13. Limitações deste estudo

- ambiente local (Kind)
- ausência de tráfego real
- sem incidentes reais
- sem impacto financeiro mensurável

---
## 14. Síntese

Em ambientes Kubernetes, qualidade não se limita ao código da aplicação.

Ela emerge da interação entre:
- manifests
- controladores
- tráfego
- falhas
- escala

Como QA é importante **tornar esses comportamentos observáveis, previsíveis e governáveis**.

TO DO: Organizar responsabilidades do Kubernetes vs definição de estado
Entender quais são as declarações default do Deployment quando não declaradas