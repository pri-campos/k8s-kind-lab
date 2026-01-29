# Recursos (Requests/Limits) e Autoscaling (HPA)

## Objetivo
Aplicar requests/limits, habilitar métricas e validar autoscaling horizontal.

---
## Requests e Limits (base para governança de recursos)
Verificar se há resources definidos:
kubectl -n <ns_name> get deploy nginx -o jsonpath='{.spec.template.spec.containers[0].resources}'; echo

Adicionar requests/limits via patch:
kubectl -n <ns_name> patch deployment nginx --type='json' -p='[
  {"op":"add","path":"/spec/template/spec/containers/0/resources","value":{
    "requests":{"cpu":"100m","memory":"64Mi"},
    "limits":{"cpu":"200m","memory":"128Mi"}
  }}
]'

Conferir no describe:
kubectl -n <ns_name> describe deploy nginx | sed -n '/Containers:/,/Conditions:/p'

---
## Métricas (metrics-server) no Kind
Aplicar manifest:
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

Patch para funcionar no kind:
kubectl -n kube-system patch deployment metrics-server --type='json' -p='[
  {"op":"add","path":"/spec/template/spec/containers/0/args/-","value":"--kubelet-insecure-tls"},
  {"op":"add","path":"/spec/template/spec/containers/0/args/-","value":"--kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname"}
]'

Aguardar rollout:
kubectl -n kube-system rollout status deploy/metrics-server

Validar métricas:
kubectl top nodes
kubectl -n <ns_name> top pods

---
## Workload para gerar carga de CPU
Criar deployment cpu-hog:
kubectl -n <ns_name> create deployment cpu-hog --image=busybox -- sh -c \
  "while true; do dd if=/dev/zero of=/dev/null; done"

Aplicar resources ao cpu-hog:
kubectl -n <ns_name> patch deployment cpu-hog --type='json' -p='[
  {"op":"add","path":"/spec/template/spec/containers/0/resources","value":{
    "requests":{"cpu":"100m","memory":"32Mi"},
    "limits":{"cpu":"200m","memory":"64Mi"}
  }}
]'

---
## HPA
Criar HPA:
kubectl -n <ns_name> autoscale deploy cpu-hog --cpu-percent=50 --min=1 --max=5

Ver HPA:
kubectl -n <ns_name> get hpa
kubectl -n <ns_name> describe hpa cpu-hog

Acompanhar HPA:
kubectl -n <ns_name> get hpa -w

Acompanhar Pods do cpu-hog:
kubectl -n <ns_name> get pods -l app=cpu-hog -w

Ver consumo:
kubectl -n <ns_name> top pods -l app=cpu-hog
