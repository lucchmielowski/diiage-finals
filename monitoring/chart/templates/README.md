# OpenTelemetry Resources

Ce dossier contient les templates pour les ressources OpenTelemetry qui lient toute la stack de monitoring.

## Ressources créées

### 1. OpenTelemetryCollector CRD (optionnel)

Le fichier `opentelemetrycollector.yaml` crée une ressource OpenTelemetryCollector CRD. Par défaut, cette ressource est désactivée car le collector est géré via le chart Helm `opentelemetry-collector`.

Pour activer la gestion via CRD au lieu de Helm, définissez `opentelemetryCollector.enabled: true` dans `values.yaml`.

### 2. Instrumentation Resources

Le fichier `instrumentation.yaml` crée deux ressources Instrumentation :

- **default-instrumentation** : Pour instrumenter automatiquement le backend
- **traffic-generator-instrumentation** : Pour instrumenter automatiquement le traffic-generator

Ces ressources Instrumentation permettent l'auto-instrumentation des applications Go en injectant automatiquement les variables d'environnement OpenTelemetry nécessaires.

## Auto-instrumentation

Pour activer l'auto-instrumentation, les deployments doivent avoir l'annotation suivante :

```yaml
annotations:
  instrumentation.opentelemetry.io/inject-go: "namespace/instrumentation-name"
```

Les deployments backend et traffic-generator ont déjà ces annotations configurées dans leurs charts respectifs.

## Flux de données

1. Les applications (backend, traffic-generator) envoient leurs traces/métriques/logs à l'OpenTelemetry Collector via OTLP
2. L'OpenTelemetry Collector route les données vers :
   - **Traces** → Tempo
   - **Métriques** → Prometheus (via remote write)
   - **Logs** → Debug exporter (pour développement)

## Configuration

Toute la configuration se trouve dans `values.yaml` sous les sections :
- `opentelemetryCollector` : Configuration du collector CRD (optionnel)
- `instrumentation` : Configuration des ressources Instrumentation
