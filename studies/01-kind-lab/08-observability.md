# Observabilidade (Logs e Métricas)

## Objetivo
Inspecionar comportamento do sistema via logs e métricas com kubectl.

---
## Logs
Consultar logs de um Pod:
kubectl -n <ns_name> logs <pod_name>

---
## Gerar logs estruturados (JSON)
Criar Pod de log:
kubectl -n <ns_name> run json-logger \
  --image=busybox \
  --restart=Never \
  -- sh -c 'while true; do echo "{\"level\":\"info\",\"service\":\"demo\",\"msg\":\"request processed\",\"ts\":\"$(date -Iseconds)\"}"; sleep 5; done'

Ver logs:
kubectl -n <ns_name> logs json-logger

---
## Métricas
Ver consumo de Pods:
kubectl -n <ns_name> top pods
