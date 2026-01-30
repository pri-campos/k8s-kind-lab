# Segurança (RBAC)

## Objetivo
Criar uma identidade (ServiceAccount) com permissões mínimas e validar acesso com `kubectl auth can-i`.

---
## ServiceAccount

Criar:
```bash
kubectl -n <ns_name> create serviceaccount app-sa
```

---
## Role (permissões mínimas)

Aplicar Role:
```bash
kubectl apply -f manifests/rbac/pod-reader-role.yaml
```

---

## RoleBinding (ligar Role ao ServiceAccount)

Aplicar RoleBinding:
```bash
kubectl apply -f manifests/rbac/pod-reader-binding.yaml
```

---
## Testar permissões

Verificar se pode listar secrets (deve negar se Role não incluir secrets):
```bash
kubectl auth can-i list secrets \
  --as=system:serviceaccount:<ns_name>:app-sa \
  -n <ns_name>
```
