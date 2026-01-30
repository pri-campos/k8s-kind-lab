# Namespace e observabilidade básica

Criar namespace:
```bash
kubectl create ns <ns_name>
```

Listar namespaces:
```bash
kubectl get ns
```

Listar recursos no namespace:
```bash
kubectl -n <ns_name> get all
```

Eventos:
```bash
kubectl -n <ns_name> get events --sort-by=.metadata.creationTimestamp
```
