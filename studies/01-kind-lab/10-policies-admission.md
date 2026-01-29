# Admission Controller e Policy as Code (Kyverno)

## Objetivo
Bloquear configurações inseguras no cluster via políticas declarativas.

---
## Instalar Kyverno
Instalar:
kubectl apply --server-side -f https://github.com/kyverno/kyverno/releases/download/v1.14.0/install.yaml

Validar namespace:
kubectl get ns kyverno

Ver Pods:
kubectl -n kyverno get pods

Acompanhar rollout:
kubectl -n kyverno rollout status deploy/kyverno-admission-controller

**Observação de compatibilidade (registro do aprendizado)**
Problema encontrado:
- `kubectl` muito atualizado e cluster/server desatualizado
- diferença recomendada costuma ser pequena (ex: 1 versão)
- cenário observado: client 1.35 vs server 1.29

---
## Política: negar tag `latest`
Aplicar:
kubectl apply -f manifests/policies/deny-latest.yaml

Testar (deve ser bloqueado):
kubectl -n <ns_name> run bad-pod --image=nginx:latest

---
## Política: exigir requests/limits
Aplicar:
kubectl apply -f manifests/policies/require-resources.yaml

Testar (deve ser bloqueado se não tiver resources):
kubectl -n <ns_name> run bad-pod2 --image=nginx:1.27
