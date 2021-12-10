# Chekr

A inspection utility for the maintenance of Kubernetes clusters.

Learn more: [https://github.com/ckotzbauer/chekr](https://github.com/ckotzbauer/chekr)

## TL;DR;

```bash
$ helm install ckotzbauer/chekr
```

## Prerequisites

- Kubernetes 1.17+ (Helm chart)

## Installing the Chart

To install the chart with the release name `my-release`:

```bash
$ helm install --name my-release ckotzbauer/chekr
```

The command deploys the nginx-pod of this chart on the Kubernetes cluster using the default configuration. The [configuration](#configuration) section lists the parameters that can be configured during installation.

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```bash
$ helm delete my-release
```
The command removes all the Kubernetes components associated with the chart and deletes the release.

## Configuration

The following table lists the configurable parameters of the Chekr chart and their default values.

|               Parameter                |                    Description                    |            Default                    |
| -------------------------------------- | ------------------------------------------------- | ------------------------------------- |
| `imagePullSecrets`                     | Pull-Secrets to use for each pod                  | `[]`                                  |
| `nameOverride`                         | Name-Override                                     | `""`                                  |
| `fullnameOverride`                     | FullName-Override                                 | `""`                                  |
| `webserver.image.repository`           | container image repository                        | `nginxinc/nginx-unprivileged`         |
| `webserver.image.tag`                  | container image tag                               | `mainline-alpine`                     |
| `webserver.image.pullPolicy`           | container image pull policy                       | `IfNotPresent`                        |
| `webserver.service.type`               | type of the webserver-service                     | `ClusterIP`                           |
| `webserver.service.port`               | port of the webserver-service                     | `8080`                                |
| `webserver.ingress.enabled`            | whether to create an Ingress                      | `false`                               |
| `webserver.ingress.className`          | name of the IngressClass                          | `""`                                  |
| `webserver.ingress.annotations`        | Annotations for the Ingress                       | `{}`                                  |
| `webserver.ingress.hosts`              | List of host-objects for the Ingress              | `[]`                                  |
| `webserver.ingress.tls`                | List of TLS-configs for the Ingress               | `[]`                                  |
| `webserver.resources`                  | webserver-pod resource requests & limits          | See [values.yaml](values.yaml)        |
| `webserver.nodeSelector`               | node labels for webserver-pod assignment          | `{}`                                  |
| `webserver.tolerations`                | node tolerations for webserver-pod assignment     | `[]`                                  |
| `webserver.affinity`                   | node affinity for webserver-pod assignment        | `{}`                                  |
| `job.image.repository`                 | container image repository                        | `ghcr.io/ckotzbauer/chekr`            |
| `job.image.tag`                        | container image tag                               | `0.5.1`                               |
| `job.image.pullPolicy`                 | container image pull policy                       | `IfNotPresent`                        |
| `job.schedule`                         | cron-schedule for the job                         | `0 22 * * *`                          |
| `job.commands`                         | Array of commands and output-files to process     | `[]` See [values.yaml](values.yaml)   |
| `job.config`                           | Global config-values for chekr-config-file        | `{}` See [values.yaml](values.yaml)   |
| `job.env`                              | List of environment-variables                     | `[]` See [values.yaml](values.yaml)   |
| `job.resources`                        | job-pod resource requests & limits                | See [values.yaml](values.yaml)        |
| `job.nodeSelector`                     | node labels for job-pod assignment                | `{}`                                  |
| `job.tolerations`                      | node tolerations for job-pod assignment           | `[]`                                  |
| `job.affinity`                         | node affinity for job-pod assignment              | `{}`                                  |
| `job.serviceAccount.create`	         | Should we create a ServiceAccount for the Job     | `true`                                |
| `job.serviceAccount.name`		         | Name of the ServiceAccount to use                 | null                                  |
| `podSecurityContext`                   | securityContext to add to each pod                | See [values.yaml](values.yaml)        |
| `securityContext`                      | securityContext to add to each container          | See [values.yaml](values.yaml)        |
| `podAnnotations`                       | annotations to add to each pod                    | `{}`                                  |
| `persistence.storageClass`             | storage class of the PVC (RWX is required)        | `-`                                   |
| `persistence.size`                     | size of the PVC (RWX is required)                 | `256Mi`                               |


Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```bash
$ helm install --name my-release \
    --set key_1=value_1,key_2=value_2 \
    ckotzbauer/chekr
```

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. For example,

```bash
# example for staging
$ helm install --name my-release -f values.yaml ckotzbauer/chekr
```

## Example

```yaml
webserver:
  ingress:
    enabled: true
    className: "nginx"
    hosts:
      - host: chekr.myorg.com
        paths:
          - path: /
            pathType: ImplementationSpecific
    tls:
      - secretName: ""
        hosts:
          - chekr.myorg.com

job:
  commands:
    - command: "deprecation list -o html"
      outputFile: "deprecation.html"
    - command: "resources -n flux-system --limits-threshold 20 -o json"
      outputFile: "flux-system-resources.json"
    - command: "resources -n infrastructure -o html"
      outputFile: "infrastructure-resources.html"

  config:
    prometheus-url: monitoring/k8s-prometheus:9090
```

This will deploy the nginx-webserver pod under the Domain "chekr.myorg.com". The CronJob will be executed each night (default schedule) and will
execute the following commands on each run:

```
chekr deprecation list -o html --output-file deprecation.html
chekr resources -n flux-system --limits-threshold 20 -o json --output-file flux-system-resources.json
chekr resources -n infrastructure -o html --output-file infrastructure-resources.html
```

The output-files are served from the webserver:

```
https://chekr.myorg.com/deprecation.html
https://chekr.myorg.com/flux-system-resources.json
https://chekr.myorg.com/infrastructure-resources.html
```

Chekr will use the in-cluster Service `k8s-prometheus` in the `monitoring` namespace at port `9090` to query prometheus for the `resources` subcommand.


> **Tip**: You can use the default [values.yaml](values.yaml)
