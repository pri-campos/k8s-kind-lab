# Networking (Service / Endpoints)

## Objetivo
Expor o Deployment via Service e observar como o Kubernetes conecta tráfego aos Pods por **labels → endpoints**.

---
## Criar Service para o Deployment
Expor Deployment:
kubectl -n <ns_name> expose deploy nginx --name=nginx-svc --port=80 --target-port=80

Listar Services:
kubectl -n <ns_name> get svc

---
## Observar Endpoints
Listar endpoints do Service:
kubectl -n <ns_name> get endpoints <service_name>

Acompanhar mudanças (watch):
kubectl -n <ns_name> get endpoints <service_name> -w

> Endpoints refletem os Pods ativos selecionados pelo Service.
