# prometheus-nebula-exporter

## Overview

This chart deploys a [Prometheus Exporter](https://github.com/kozmoai/prometheus-nebula-exporter) for Nebula Server, providing relevant metrics from your deployed Nebula Server instance.
Shoutout to @ialejandro for the original work on this chart!

## Installing the Chart

### Prerequisites

1. Add the Nebula Helm repository to your Helm client:

    ```bash
    helm repo add nebula https://kozmoai.github.io/nebula-helm
    helm repo update
    ```

2. Deploy a Nebula Server instance using the [Nebula Server Helm chart](https://github.com/kozmoai/nebula-helm/tree/main/charts/nebula-server)

### Install the Nebula Exporter chart

1. Configure the Nebula exporter values as needed
    Create a `values.yaml` file to customize the Prometheus Nebula Exporter configuration.

2. Install the chart
    ```bash
    helm install prometheus-nebula-exporter nebula/prometheus-nebula-exporter --namespace=<NEBULA_SERVER_NAMESPACE> -f values.yaml
    ```

3. Verify the deployment

    Check the status of your Prometheus Nebula Exporter deployment:

    ```bash
    kubectl get pods -n nebula

    NAME                                  READY   STATUS    RESTARTS       AGE
    prometheus-nebula-exporter-76vxqnq   1/1     Running   0              25m
    ```

    You should see the Prometheus Nebula Exporter pod running

## Maintainers

| Name | Email | Url |
| ---- | ------ | --- |
| jamiezieziula | <jamie@nebula.io> |  |
| jimid27 | <jimi@nebula.io> |  |
| parkedwards | <edward@nebula.io> |  |

## Requirements

| Repository | Name | Version |
|------------|------|---------|
| https://charts.bitnami.com/bitnami | common | 2.16.1 |

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` | Affinity for pod assignment |
| autoscaling | object | `{"enabled":false,"maxReplicas":100,"minReplicas":1,"targetCPUUtilizationPercentage":80}` | Autoscaling with CPU or memory utilization percentage |
| env | object | `{}` | Environment variables to configure application |
| fullnameOverride | string | `""` | String to fully override prometheus-nebula-exporter.fullname template |
| image | object | `{"pullPolicy":"IfNotPresent","repository":"kozmoai/prometheus-nebula-exporter","tag":"1.0.0"}` | Image registry |
| imagePullSecrets | list | `[]` | Global Docker registry secret names as an array |
| ingress | object | `{"annotations":{},"className":"","enabled":false,"hosts":[{"host":"chart-example.local","paths":[{"path":"/","pathType":"ImplementationSpecific"}]}],"tls":[]}` | Ingress configuration to expose app |
| livenessProbe | bool | `false` | Enable livenessProbe |
| nameOverride | string | `""` | String to partially override prometheus-nebula-exporter.fullname template (will maintain the release name) |
| nodeSelector | object | `{}` | Node labels for pod assignment |
| podAnnotations | object | `{}` | Pod annotations |
| podDisruptionBudget | object | `{}` | Limits the number of Pods of a replicated application that are down simultaneously from voluntary disruptions |
| podSecurityContext | object | `{}` | To specify security settings for a Pod |
| nebulaApiUrl | string | `"http://nebula-server.nebula.svc.cluster.local:4200/api"` | Nebula API URL to connect to for metrics |
| prometheusRule.additionalLabels | object | `{}` |  |
| prometheusRule.enabled | bool | `false` |  |
| prometheusRule.rules | list | `[]` |  |
| readinessProbe | bool | `false` | Enable readinessProbe |
| replicaCount | int | `1` | Number of replicas |
| resources | object | `{}` | The resources limits and requested |
| securityContext | object | `{}` | Defines privilege and access control settings for a Pod or Container |
| service | object | `{"port":80,"targetPort":8000,"type":"ClusterIP"}` | Kubernetes servide to expose Pod |
| service.port | int | `80` | Kubernetes Service port |
| service.targetPort | int | `8000` | Pod expose port |
| service.type | string | `"ClusterIP"` | Kubernetes Service type. Allowed values: NodePort, LoadBalancer or ClusterIP |
| serviceAccount | object | `{"annotations":{},"create":true,"name":""}` | Enable creation of ServiceAccount |
| serviceMonitor | object | `{"enabled":false,"interval":"30s","metricRelabelings":[],"relabelings":[],"scrapeTimeout":"10s"}` | Enable ServiceMonitor to get metrics ref: https://github.com/prometheus-operator/prometheus-operator/blob/main/Documentation/api.md#servicemonitor |
| serviceMonitor.enabled | bool | `false` | Enable or disable |
| tolerations | list | `[]` | Tolerations for pod assignment |
