# Namespace e observabilidade básica

Criar namespace:
kubectl create ns <ns_name>

Listar namespaces:
kubectl get ns

Listar recursos no namespace:
kubectl -n <ns_name> get all

Eventos:
kubectl -n <ns_name> get events --sort-by=.metadata.creationTimestamp
