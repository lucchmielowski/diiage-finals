# Monitoring Stack

Stack de monitoring pour surveiller l'application backend, basée sur Prometheus, Grafana, Tempo et OpenTelemetry Collector.

## Composants

- **OpenTelemetry Operator** : Opérateur Kubernetes pour gérer les ressources OpenTelemetry (Instrumentation, OpenTelemetryCollector CRD)
- **OpenTelemetry Collector** : Collecte et route la télémétrie (traces, métriques, logs)
- **Tempo** : Backend de traces distribuées
- **Prometheus** : Collecte et stocke les métriques
- **Grafana** : Visualisation des métriques et traces

**Note** : cert-manager est requis pour OpenTelemetry Operator mais est géré séparément via GitOps (`gitops/applicationsets/cert-manager-app.yaml`).

## Installation

Pour installer la stack de monitoring :

```bash
./install.sh
```

Le script installera automatiquement via Helm :
1. OpenTelemetry Operator
2. OpenTelemetry Collector
3. Tempo (tracing backend)
4. Prometheus (metrics backend)
5. Grafana (visualization)

**Prérequis** : cert-manager doit être installé séparément (via GitOps ou manuellement) avant d'installer la stack de monitoring.

### Installation via Helm directement

Vous pouvez aussi installer directement le chart unifié :

```bash
cd chart
helm dependency update
helm install monitoring-stack . --namespace monitoring --create-namespace
```

## Structure

Le dossier `chart/` contient un chart Helm unifié qui agrège tous les composants comme dépendances :
- `Chart.yaml` : Définit les dépendances (OpenTelemetry Operator, Prometheus, Grafana, Tempo)
- `values.yaml` : Configuration unifiée pour tous les composants
- `templates/` : Templates pour les ressources OpenTelemetry (OpenTelemetryCollector CRD) et le namespace

**Note** : L'OpenTelemetry Collector est géré via l'OpenTelemetry Operator en utilisant une ressource CRD `OpenTelemetryCollector` (définie dans `templates/opentelemetrycollector.yaml`), et non via le chart Helm `opentelemetry-collector` qui est désactivé.

**Note** : cert-manager est géré séparément via GitOps et n'est pas inclus dans ce chart.

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
- `opentelemetry-operator:` : Configuration OpenTelemetry Operator (activé par défaut, avec support Go auto-instrumentation activé)
- `opentelemetry-collector:` : Chart Helm désactivé (le collector est géré via CRD)
- `opentelemetryCollector:` : Configuration de la ressource CRD OpenTelemetryCollector (gérée par l'operator)
- `prometheus:` : Configuration Prometheus
- `grafana:` : Configuration Grafana avec datasources pré-configurés
- `tempo:` : Configuration Tempo

**Note** : Les ressources Instrumentation sont maintenant gérées directement dans chaque chart applicatif (`app/chart/templates/instrumentation.yaml` et `traffic-gen-app/chart/templates/instrumentation.yaml`).

**Note** : cert-manager est configuré séparément via `gitops/applicationsets/cert-manager-app.yaml`.

## Déploiement via GitOps

La stack de monitoring est également disponible via GitOps. Elle est définie dans `gitops/applicationsets/monitoring-apps.yaml` et sera automatiquement déployée par ArgoCD si le bootstrap GitOps est configuré.

## Métriques de l'application

L'application backend expose déjà des métriques Prometheus sur `/metrics` :
- `http_requests_total` : Nombre total de requêtes HTTP par chemin et code de statut
- `configmap_read_total` : Nombre de lectures de ConfigMap

Ces métriques sont automatiquement scrapées par Prometheus. L'OpenTelemetry Collector (géré via CRD par l'operator) collecte les traces et métriques OTLP des applications instrumentées et les route vers Tempo (traces) et Prometheus (métriques).
