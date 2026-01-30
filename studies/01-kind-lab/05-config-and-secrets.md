# Configuração (ConfigMap / Secret) e Injeção em Pod

## Objetivo
Criar ConfigMap e Secret, injetar no Deployment e validar as variáveis dentro do container.

---
## ConfigMap

Criar:
```bash
kubectl -n <ns_name> create configmap app-config \
  --from-literal=APP_MODE=lab \
  --from-literal=WELCOME_MSG="Olá do ConfigMap"
```

Consultar:
```bash
kubectl -n <ns_name> get configmap app-config -o yaml
```

---
## Secret

Criar:
```bash
kubectl -n <ns_name> create secret generic app-secret \
  --from-literal=API_KEY="chave-v1-super-secreta"
```

Consultar:
```bash
kubectl -n <ns_name> get secret app-secret
```

---
## Vincular ConfigMap e Secret ao Deployment (envFrom)

Aplicar patch:
```bash
kubectl -n <ns_name> patch deployment nginx --type='json' -p='[
  {"op":"add","path":"/spec/template/spec/containers/0/envFrom","value":[
    {"configMapRef":{"name":"app-config"}},
    {"secretRef":{"name":"app-secret"}}
  ]}
]'
```

Monitorar rollout:
```bash
kubectl -n <ns_name> rollout status deploy/nginx
```

---
## Validar variáveis dentro do Pod

Capturar um Pod:
```bash
POD=$(kubectl -n <ns_name> get pod -l app=nginx -o jsonpath='{.items[0].metadata.name}')
```

Executar comando no container:
```bash
kubectl -n <ns_name> exec -it $POD -- sh -c '
  echo "APP_MODE=$APP_MODE";
  echo "WELCOME_MSG=$WELCOME_MSG";
  echo "API_KEY=$API_KEY"
'
```
