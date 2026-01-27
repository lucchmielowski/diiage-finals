# GitOps – structure inspirée de `diiage-k8s-p2/gitops`

Cette arborescence est basée sur `[diiage-k8s-p2/gitops](https://github.com/lucchmielowski/diiage-k8s-p2/tree/main/gitops)` et préparée pour tes démos / TP.

## Arborescence

```text
gitops/
├── applicationsets/
│   └── demo-apps.yaml
├── argocd/
│   └── install.sh
├── bootstrap/
│   └── argocd-bootstrap.yaml
└── environments/
    ├── prod/
    │   └── namespace.yaml
    └── values/
        ├── backend-prod.yaml
        └── traffic-gen-prod.yaml
```

## Principe

- **`argocd/`** : installation / configuration d’Argo CD dans le cluster.
- **`bootstrap/`** : manifeste(s) pour bootstrapper Argo CD avec ce repo GitOps.
- **`applicationsets/`** : définitions `ApplicationSet` Argo CD qui pointent vers les charts applicatifs et les fichiers de valeurs.
- **`environments/`** : configuration pour un seul environnement (`prod`) + fichiers de valeurs spécifiques.

## Installation de l'OpenTelemetry Operator

L'OpenTelemetry Operator est requis pour que les ressources `Instrumentation` et `OpenTelemetryCollector` CRD fonctionnent. Il est inclus dans le chart `monitoring-stack` comme dépendance Helm et sera déployé automatiquement avec la stack de monitoring.

Tu peux adapter les URLs de repo, les noms de charts et les namespaces en fonction de ce projet.

