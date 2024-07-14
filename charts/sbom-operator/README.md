# sbom-operator

> Catalogue all images of a Kubernetes cluster to multiple targets with Syft.

Learn more: [https://github.com/ckotzbauer/sbom-operator](https://github.com/ckotzbauer/sbom-operator)


## Installing the Chart

To install the chart with the release name `my-release`:

```bash
$ helm install my-release ckotzbauer/sbom-operator
```

The command deploys the sbom-operator operator on the Kubernetes cluster using the default configuration. The [configuration](#configuration) section lists the parameters that can be configured during installation.

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```bash
$ helm delete my-release
```
The command removes all the Kubernetes components associated with the chart and deletes the release.

## Configuration

The following table lists the configurable parameters of the sbom-operator chart and their default values.

|               Parameter                |                    Description                    |            Default                       |
| -------------------------------------- | ------------------------------------------------- | ---------------------------------------- |
| `image.repository`                     | container image repository                        | `ghcr.io/ckotzbauer/sbom-operator`       |
| `image.tag`                            | container image tag                               | `0.36.0`                                 |
| `image.pullPolicy`                     | container image pull policy                       | `IfNotPresent`                           |
| `image.pullSecrets`                    | image pull-secrets                                | `[]`                                     |
| `args`                                 | argument object for cli-args                      | `{}`                                     |
| `envVars`                              | environment variables                             | `{}`                                     |
| `nodeSelector`                         | node labels for pod assignment                    | `{}`                                     |
| `tolerations`                          | node tolerations for pod assignment               | `[]`                                     |
| `affinity`                             | node affinity for pod assignment                  | `{}`                                     |
| `podAnnotations`                       | annotations to add to each pod                    | `{}`                                     |
| `priorityClassName`                    | priority class name for the pod                   | `""`                                     |
| `resources`                            | pod resource requests & limits                    | See [values.yaml](values.yaml)           |
| `podSecurityContext`                   | pod securityContext                               | See [values.yaml](values.yaml)        |
| `securityContext`                      | container securityContext                         | See [values.yaml](values.yaml)           |
| `serviceAccount.create`                | Should we create a ServiceAccount                 | `true`                                   |
| `serviceAccount.name`                  | Name of the ServiceAccount to use                 | null                                     |
| `jobImageMode`                         | Whether or not a job-image is used.               | `false`                                  |
| `extraVolumes`                         | Extra volumes (needed for GithubApp PK).          | `[]`                                     |
| `extraVolumeMounts`                    | Extra volume mounts                               | `[]`                                     |

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```bash
$ helm install my-release \
    --set key_1=value_1,key_2=value_2 \
    ckotzbauer/sbom-operator
```

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. For example,

```bash
# example for staging
$ helm install my-release -f values.yaml ckotzbauer/sbom-operator
```

> **Tip**: You can use the default [values.yaml](values.yaml)
