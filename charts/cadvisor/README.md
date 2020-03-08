# Cadvisor

A chart for a Cadvisor deployment

Learn more: [https://github.com/google/cadvisor](https://github.com/google/cadvisor)

## TL;DR;

```bash
$ helm install code-chris/cadvisor
```

## Introduction

This chart creates a daemonset on a [Kubernetes](http://kubernetes.io) cluster using the [Helm](https://helm.sh) package manager.

## Prerequisites

- Kubernetes 1.10+

## Installing the Chart

To install the chart with the release name `my-release`:

```bash
$ helm install --name my-release code-chris/cadvisor
```

The command deploys the Cadvisor on the Kubernetes cluster using the default configuration. The [configuration](#configuration) section lists the parameters that can be configured during installation.

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
| `image.repository`                     | container image repository                        | `k8s.gcr.io/cadvisor`         |
| `image.tag`                            | container image tag                               | `v0.36.0`                     |
| `image.pullPolicy`                     | container image pull policy                       | `IfNotPresent`                |
| `nodeSelector`                         | node labels for pod assignment                    | `{}`                          |
| `tolerations`                          | node tolerations for pod assignment               | `[]`                          |
| `affinity`                             | node affinity for pod assignment                  | `{}`                          |
| `container.port`                       | the container and service port to use             | 8080                          |
| `container.additionalArgs`             | additional container arguments                    | see values.yaml               |
| `container.hostPaths`                  | hostPaths to mount in the container               | see values.yaml               |
| `resources`                            | pod resource requests & limits                    | `{}`                          |
| `serviceAccount.create`                | create a own serviceAccount for the pod           | `true`                        |
| `serviceAccount.name`                  | name of the serviceAccount to create              | `""`                          |
| `podSecurityPolicy.create`             | create a own PodSecurityPolicy for the pod        | `false`                       |

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```bash
$ helm install --name my-release \
    --set key_1=value_1,key_2=value_2 \
    code-chris/cadvisor
```

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. For example,

```bash
# example for staging
$ helm install --name my-release -f values.yaml code-chris/cadvisor
```

> **Tip**: You can use the default [values.yaml](values.yaml)
