# Pipeline (Kustomize / Dry-run / Apply)

## Objetivo
Validar manifests antes de aplicar e executar deploy por overlay (base → ambiente).

---
## Renderizar overlay (preview do que será aplicado)
Gerar YAML renderizado:
kubectl kustomize manifests/overlays/qa > /tmp/rendered.yaml

---
## Preparar namespace (idempotente)
Criar namespace via dry-run (client):
kubectl create ns qa --dry-run=client -o yaml | kubectl apply -f -

---
## Validar aplicação (dry-run server)
Validar no servidor (sem persistir):
kubectl apply --dry-run=server -f /tmp/rendered.yaml

---
## Aplicar overlay
Aplicar kustomize:
kubectl apply -k manifests/overlays/qa

---
## Confirmar rollout
kubectl -n qa rollout status deploy/web
