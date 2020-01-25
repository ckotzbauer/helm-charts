# Prometheus MSTeams

Alertmanager Webhook for Microsoft Teams

Learn more: [https://github.com/bzon/prometheus-msteams](https://github.com/bzon/prometheus-msteams)

## TL;DR;

```bash
$ helm install code-chris/prometheus-msteams
```

## Introduction

This chart creates a Webhook deployment on a [Kubernetes](http://kubernetes.io) cluster using the [Helm](https://helm.sh) package manager.

## Prerequisites

- Kubernetes 1.9+

## Installing the Chart

To install the chart with the release name `my-release`:

```bash
$ helm install --name my-release code-chris/prometheus-msteams
```

The command deploys this Webhook on the Kubernetes cluster using the default configuration. The [configuration](#configuration) section lists the parameters that can be configured during installation.

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```bash
$ helm delete my-release
```
The command removes all the Kubernetes components associated with the chart and deletes the release.

## Configuration

The following table lists the configurable parameters of the Prometheus MSTeams chart and their default values.

|               Parameter                |                    Description                    |            Default            |
| -------------------------------------- | ------------------------------------------------- | ----------------------------- |
| `replicaCount`                         | Instance count to deploy                          | 1                             |
| `image.repository`                     | container image repository                        | `bzon/prometheus-msteams`     |
| `image.tag`                            | container image tag                               | `v1.1.5`                      |
| `image.pullPolicy`                     | container image pull policy                       | `IfNotPresent`                |
| `extraEnvs`                            | Optional env variables for webhook deployment     | `[]`                          |
| `nodeSelector`                         | node labels for pod assignment                    | `{}`                          |
| `tolerations`                          | node tolerations for pod assignment               | `[]`                          |
| `affinity`                             | node affinity for pod assignment                  | `{}`                          |
| `connectors`                           | MS Teams Connectors (URLs)                        | `{}`                          |
| `customCardTemplate`                   | Customized template for Team cards                | `""`                          |
| `podAnnotations`                       | annotations to add to each pod                    | `{}`                          |
| `additionalArgs`                       | additional container arguments                    | `[]`                          |
| `resources`                            | pod resource requests & limits                    | `{}`                          |
| `service.type`                         | type of service to create                         | `ClusterIP`                   |
| `service.port`                         | port for the blackbox http service                | `9115`                        |
| `service.externalIPs`                  | list of external ips                              | `[]`                          |
| `service.loadBalancerIP`               | optional load balancer ip                         | `""`                          |
| `service.loadBalancerSourceRanges`     | optional load balancer source ranges              | `[]`                          |
| `service.externalTrafficPolicy`        | optional external traffic policy                  | `""`                          |

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```bash
$ helm install --name my-release \
    --set key_1=value_1,key_2=value_2 \
    code-chris/prometheus-msteams
```

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. For example,

```bash
# example for staging
$ helm install --name my-release -f values.yaml code-chris/prometheus-msteams
```

> **Tip**: You can use the default [values.yaml](values.yaml)
