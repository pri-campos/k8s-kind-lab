# Cluster lifecycle com Kind

Ferramentas:
- kubectl
- kind

Criar cluster:
kind create cluster --name <cluster_name>

Deletar cluster:
kind delete cluster --name <cluster_name>

Verificar nodes:
kubectl get nodes
