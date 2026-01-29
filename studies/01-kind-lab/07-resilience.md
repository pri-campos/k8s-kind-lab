# Resiliência (PDB e Anti-Affinity)

## Objetivo
Aplicar políticas de resiliência para reduzir indisponibilidade e evitar concentração de réplicas.

---
## PodDisruptionBudget (PDB)
Aplicar:
kubectl apply -f nginx-pdb.yaml

Consultar:
kubectl -n <ns_name> get pdb
kubectl -n <ns_name> describe pdb nginx-pdb

---
## Anti-Affinity (espalhar réplicas)
Aplicar patch no Deployment:
kubectl -n <ns_name> patch deployment nginx --type='json' -p='[
  {
    "op": "add",
    "path": "/spec/template/spec/affinity",
    "value": {
      "podAntiAffinity": {
        "preferredDuringSchedulingIgnoredDuringExecution": [
          {
            "weight": 100,
            "podAffinityTerm": {
              "labelSelector": {
                "matchLabels": {
                  "app": "nginx"
                }
              },
              "topologyKey": "kubernetes.io/hostname"
            }
          }
        ]
      }
    }
  }
]'
