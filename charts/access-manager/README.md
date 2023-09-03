# Access-Manager

Kubernetes-Operator to simplify RBAC configurations and spread secrets across namespaces.

Learn more: [https://github.com/ckotzbauer/access-manager](https://github.com/ckotzbauer/access-manager)

## TL;DR;

```bash
$ helm install ckotzbauer/access-manager
```

## Prerequisites

- Kubernetes 1.9+ (Helm chart)
- Kubernetes 1.16+ (Operator)

## Installing the Chart

To install the chart with the release name `my-release`:

```bash
$ helm install --name my-release ckotzbauer/access-manager
```

The command deploys the access-manager operator on the Kubernetes cluster using the default configuration. The [configuration](#configuration) section lists the parameters that can be configured during installation.

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```bash
$ helm delete my-release
```
The command removes all the Kubernetes components associated with the chart and deletes the release.

## Configuration

The following table lists the configurable parameters of the Access-Manager chart and their default values.

|               Parameter                |                    Description                    |            Default                    |
| -------------------------------------- | ------------------------------------------------- | ------------------------------------- |
| `replicas`                             | replicas of deployment (leader-election only > 1) | `1`                                   |
| `image.repository`                     | container image repository                        | `ghcr.io/ckotzbauer/access-manager`   |
| `image.tag`                            | container image tag                               | `0.12.0`                              |
| `image.pullPolicy`                     | container image pull policy                       | `IfNotPresent`                        |
| `nodeSelector`                         | node labels for pod assignment                    | `{}`                                  |
| `tolerations`                          | node tolerations for pod assignment               | `[]`                                  |
| `affinity`                             | node affinity for pod assignment                  | `{}`                                  |
| `podAnnotations`                       | annotations to add to each pod                    | `{}`                                  |
| `priorityClassName`                    | priority class name for the pod                   | `""`                                  |
| `resources`                            | pod resource requests & limits                    | See [values.yaml](values.yaml)        |
| `securityContext`                      | container securityContext                         | See [values.yaml](values.yaml)        |
| `serviceAccount.create`	             | Should we create a ServiceAccount	             | `true`                                |
| `serviceAccount.name`		             | Name of the ServiceAccount to use                 | null                                  |

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```bash
$ helm install --name my-release \
    --set key_1=value_1,key_2=value_2 \
    ckotzbauer/access-manager
```

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. For example,

```bash
# example for staging
$ helm install --name my-release -f values.yaml ckotzbauer/access-manager
```

> **Tip**: You can use the default [values.yaml](values.yaml)
