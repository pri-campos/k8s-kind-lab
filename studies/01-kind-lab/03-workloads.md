# Workloads (Deployment / ReplicaSet / Pod)

## Objetivo
Executar um workload simples (nginx) e observar como o Kubernetes mantém o **estado desejado** via:

Deployment → ReplicaSet → Pods.

---
## Criar Deployment (exemplo: nginx)

Criar:
```bash
kubectl -n <ns_name> create deployment nginx --image=nginx
```

Verificar:
```bash
kubectl -n <ns_name> get deploy
```

---
## Escalar Deployment (convergência do estado desejado)

Escalar réplicas:
```bash
kubectl -n <ns_name> scale deploy/nginx --replicas=3
```

Verificar ReplicaSets:
```bash
kubectl -n <ns_name> get rs
```

Verificar Pods:
```bash
kubectl -n <ns_name> get pods
```

---
## Forçar falha e observar reconciliação

Deletar um Pod manualmente:
```bash
kubectl -n <ns_name> delete pod <pod_name>
```

Observar recriação:
```bash
kubectl -n <ns_name> get pods -w
```

---
## Inspecionar estado efetivo do Deployment

Ver YAML do Deployment:
```bash
kubectl -n <ns_name> get deploy nginx -o yaml
```

Ver descrição detalhada (inclui eventos e condições):
```bash
kubectl -n <ns_name> describe deploy nginx
```

Ver descrição de Pod:
```bash
kubectl -n <ns_name> describe pod <pod_name>
```

Eventos ordenados por tempo:
```bash
kubectl -n <ns_name> get events --sort-by=.metadata.creationTimestamp
```

---
## Rollout: trocar imagem e acompanhar

Trocar imagem:
```bash
kubectl -n <ns_name> set image deploy/<deployment> <container>=<imagem:tag>
```

Confirmar imagem aplicada:
```bash
kubectl -n <ns_name> get deploy <deploy_name> -o jsonpath='{.spec.template.spec.containers[*].image}'; echo
```

Acompanhar rollout:
```bash
kubectl -n <ns_name> rollout status deploy/<deploy_name>
```

Rollback:
```bash
kubectl -n <ns_name> rollout undo deploy/<deploy_name>
```
