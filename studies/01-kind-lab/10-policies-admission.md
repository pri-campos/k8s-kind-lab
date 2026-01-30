# Admission Controller e Policy as Code (Kyverno)

## Objetivo
Bloquear configurações inseguras no cluster via políticas declarativas.

---
## Instalar Kyverno

Instalar:
```bash
kubectl apply --server-side -f https://github.com/kyverno/kyverno/releases/download/v1.14.0/install.yaml
```

Validar namespace:
```bash
kubectl get ns kyverno
```

Ver Pods:
```bash
kubectl -n kyverno get pods
```

Acompanhar rollout:
```bash
kubectl -n kyverno rollout status deploy/kyverno-admission-controller
```

**Observação de compatibilidade (registro do aprendizado)**
Problema encontrado:
- `kubectl` muito atualizado e cluster/server desatualizado
- diferença recomendada costuma ser pequena (ex: 1 versão)
- precisei instalar kyverno mais desatualizado.

---
## Política: negar tag `latest`

Aplicar:
```bash
kubectl apply -f manifests/policies/deny-latest.yaml
```

Testar (deve ser bloqueado):
```bash
kubectl -n <ns_name> run bad-pod --image=nginx:latest
```

---
## Política: exigir requests/limits

Aplicar:
```bash
kubectl apply -f manifests/policies/require-resources.yaml
```

Testar (deve ser bloqueado se não tiver resources):
```bash
kubectl -n <ns_name> run bad-pod2 --image=nginx:1.27
```
