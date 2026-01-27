# Monitoring Stack

Stack de monitoring pour surveiller l'application backend, basée sur Prometheus, Grafana, Tempo et OpenTelemetry Collector.

## Composants

- **cert-manager** : Gestion des certificats TLS (requis pour OpenTelemetry Operator)
- **OpenTelemetry Operator** : Opérateur Kubernetes pour gérer les ressources OpenTelemetry (Instrumentation, OpenTelemetryCollector CRD)
- **OpenTelemetry Collector** : Collecte et route la télémétrie (traces, métriques, logs)
- **Tempo** : Backend de traces distribuées
- **Prometheus** : Collecte et stocke les métriques
- **Grafana** : Visualisation des métriques et traces

## Installation

Pour installer la stack de monitoring :

```bash
./install.sh
```

Le script installera automatiquement via Helm :
1. cert-manager (requis pour OpenTelemetry Operator)
2. OpenTelemetry Operator
3. OpenTelemetry Collector
4. Tempo (tracing backend)
5. Prometheus (metrics backend)
6. Grafana (visualization)

### Installation via Helm directement

Vous pouvez aussi installer directement le chart unifié :

```bash
cd chart
helm dependency update
helm install monitoring-stack . --namespace monitoring --create-namespace
```

## Structure

Le dossier `chart/` contient un chart Helm unifié qui agrège tous les composants comme dépendances :
- `Chart.yaml` : Définit les dépendances (cert-manager, OpenTelemetry Operator, Prometheus, Grafana, Tempo, OpenTelemetry Collector)
- `values.yaml` : Configuration unifiée pour tous les composants
- `templates/` : Templates pour les ressources OpenTelemetry (Instrumentation, OpenTelemetryCollector CRD) et le namespace

## Accès à Grafana

Après l'installation, pour accéder à Grafana :

```bash
kubectl port-forward -n monitoring svc/grafana 3000:3000
```

Puis ouvrir http://localhost:3000

**Identifiants par défaut** :
- Username: `admin`
- Password: `admin`

## Configuration

Toute la configuration Helm se trouve dans `chart/values.yaml` qui contient les sections :
- `cert-manager:` : Configuration cert-manager (activé par défaut)
- `opentelemetry-operator:` : Configuration OpenTelemetry Operator (activé par défaut)
- `prometheus:` : Configuration Prometheus
- `grafana:` : Configuration Grafana avec datasources pré-configurés
- `tempo:` : Configuration Tempo
- `opentelemetry-collector:` : Configuration du collector
- `instrumentation:` : Configuration des ressources Instrumentation pour l'auto-instrumentation

## Déploiement via GitOps

La stack de monitoring est également disponible via GitOps. Elle est définie dans `gitops/applicationsets/monitoring-apps.yaml` et sera automatiquement déployée par ArgoCD si le bootstrap GitOps est configuré.

## Métriques de l'application

L'application backend expose déjà des métriques Prometheus sur `/metrics` :
- `http_requests_total` : Nombre total de requêtes HTTP par chemin et code de statut
- `configmap_read_total` : Nombre de lectures de ConfigMap

Ces métriques sont automatiquement scrapées par Prometheus via l'OpenTelemetry Collector.
