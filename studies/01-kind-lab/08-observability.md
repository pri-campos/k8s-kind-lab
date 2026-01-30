# Observabilidade (Logs e Métricas)

## Objetivo
Inspecionar comportamento do sistema via logs e métricas com kubectl.

---
## Logs

Consultar logs de um Pod:
```bash
kubectl -n <ns_name> logs <pod_name>
```

---
## Gerar logs estruturados (JSON)

Criar Pod de log:
```bash
kubectl -n <ns_name> run json-logger \
  --image=busybox \
  --restart=Never \
  -- sh -c 'while true; do echo "{\"level\":\"info\",\"service\":\"demo\",\"msg\":\"request processed\",\"ts\":\"$(date -Iseconds)\"}"; sleep 5; done'
```

Ver logs:
```bash
kubectl -n <ns_name> logs json-logger
```

---
## Métricas

Ver consumo de Pods:
```bash
kubectl -n <ns_name> top pods
```
