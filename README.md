# Nebula Helm Charts

This repository contains the official Nebula Helm charts for installing and configuring Nebula on Kubernetes. These charts support multiple use cases of Nebula on Kubernetes depending on the values provided and chart selected including:

## [Nebula worker](charts/nebula-worker/)

[Workers](https://docs.nebula.io/latest/concepts/work-pools/#worker-overview) are lightweight polling services that retrieve scheduled runs from a work pool and execute them.

Workers are similar to agents, but offer greater control over infrastructure configuration and the ability to route work to specific types of execution environments.

Workers each have a type corresponding to the execution environment to which they will submit flow runs. Workers are only able to join work pools that match their type. As a result, when deployments are assigned to a work pool, you know in which execution environment scheduled flow runs for that deployment will run.

## [Nebula agent](charts/nebula-agent/)

### Note: Workers are recommended

Agents are part of the block-based deployment model. [Work Pools and Workers](https://docs.nebula.io/latest/concepts/work-pools/) simplify the specification of a flow's infrastructure and runtime environment. If you have existing agents, you can [upgrade from agents to workers](https://docs.nebula.io/latest/guides/upgrade-guide-agents-to-workers/) to significantly enhance the experience of deploying flows.

[Agent](https://docs.nebula.io/latest/concepts/agents/) processes are lightweight polling services that get scheduled work from a work pool and deploy the corresponding flow runs.

Agents poll for work every 15 seconds by default. This interval is configurable in your profile settings with the `NEBULA_AGENT_QUERY_INTERVAL` setting.

It is possible for multiple agent processes to be started for a single work pool. Each agent process sends a unique ID to the server to help disambiguate themselves and let users know how many agents are active.

## [Nebula server](charts/nebula-server/)

[Nebula server](https://docs.nebula.io/latest/guides/host/) is a self-hosted open source backend that makes it easy to observe and orchestrate your Nebula flows. It is an alternative to using the hosted [Nebula Cloud](https://docs.nebula.io/latest/cloud/) platform. Nebula Cloud provides additional features including automations and user management.

## [Prometheus Nebula Exporter](charts/prometheus-nebula-exporter/)

The Prometheus Nebula Exporter is a tool to pull relevant Nebula metrics from a hosted Nebula Server instance

## Usage

[Helm](https://helm.sh) must be installed to use the charts.
Please refer to Helm's [documentation](https://helm.sh/docs/) to get started.

### TL;DR

```bash
helm repo add nebula https://kozmoai.github.io/nebula-helm
helm search repo nebula
helm install my-release nebula/nebula-<chart>
```

### Installing released versions

Charts are automatically versioned and released together. The `appVersion` and `nebulaTag` version are pinned at package time to the current release of Nebula 2.

The charts are hosted in a [Helm repository](https://helm.sh/docs/chart_repository/) deployed to the web courtesy of GitHub Pages.

1. Add the Nebula Helm repository to Helm and list available charts and versions:

    ```bash
    helm repo add nebula https://kozmoai.github.io/nebula-helm
    helm repo update
    helm search repo nebula
    ```

2. Install the Helm chart

    Each chart will require some specific configuration values to be provided, see the individual chart README's for more information on configuring and installing the chart.

    If chart installation fails, run the same command with `--debug` to see additional diagnostic information.

    Refer to the [Helm `install` documentation](https://helm.sh/docs/helm/helm_install/) for all options.

The helm charts are tested against Kubernetes 1.26.0 and newer minor versions.

### Installing development versions

Development versions of the Helm chart will always be available directly from this Github repository.

1. Clone repository

2. Change to this directory

3. Download the chart dependencies

    ```bash
    helm dependency update
    ```

4. Install the chart

    ```bash
    helm install . --generate-name
    ```

### Upgrading

1. Look up the name of the last release

    ```bash
    helm list
    ```

2. Run the upgrade

    ```bash
    # Choose a version to upgrade to or omit the flag to use the latest version
    helm upgrade nebula-server nebula/nebula-server --version 2023.09.07
    ```

    For development versions, make sure your cloned repository is updated (`git pull`) and reference the local chart

    ```bash
    helm upgrade nebula-server .
    ```

3. Upgrades can also be used enable features or change options

   ```bash
   helm upgrade nebula-server nebula/nebula-server --set newValue=true
   ```

#### Important notes about upgrading

- Updates will only update infrastructure that is modified.
- You will need to continue to set any values that you set during the original install (e.g. `--set namespaceOverride=nebula` or `--values path/to/values.yaml`).
- If you are using the postgresql subchart with an autogenerated password, it will complain that you have not provided that password for the upgrade. Export the password as the error asks then set it within the subchart using:
  ```bash
  helm upgrade ... --set postgresql.postgresqlPassword=$POSTGRESQL_PASSWORD
  ```

## Options:

See comments in `values.yaml`.

### Security context

By default, the worker (or agent), and server run as an unprivileged user with a read-only root filesystem. You can customize the security context settings for both the worker and server in the `values.yaml` file for your use case.

If you need to install system packages or configure other settings at runtime, you can configure a writable filesystem and run as root by configuring the pod and container security context accordingly:

```yaml
podSecurityContext:
  runAsUser: 0
  runAsNonRoot: false
  fsGroup: 0

containerSecurityContext:
  runAsUser: 0
  # this must be false, since we are running as root
  runAsNonRoot: false
  # make the filesystem writable
  readOnlyRootFilesystem: false
  # this must be false, since we are running as root
  allowPrivilegeEscalation: false
```

If you are running in OpenShift, the default `restricted` security context constraint will prevent customization of the user. In this case, explicitly configure the `runAsUser` settings to `null` to use OpenShift-assigned settings:

```yaml
podSecurityContext:
  runAsUser: null
  fsGroup: null

containerSecurityContext:
  runAsUser: null
```

The other default settings, such as a read-only root filesystem, are suitable for an OpenShift environment.

## Additional permissions for Nebula agents

### Dask

If you are running flows on your worker’s pod (i.e. with Process infrastructure), and using the Dask task runner to create Dask Kubernetes clusters, you will need to grant the following permissions within `values.yaml`.

```yaml
role:
  extraPermissions:
    - apiGroups: [""]
      resources: ["pods", "services"]
      verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
    - apiGroups: ["policy"]
      resources: ["poddisruptionbudgets"]
      verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
```
