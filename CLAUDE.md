# CLAUDE.md

## Project Overview

This is a Helm chart repository maintained by **ckotzbauer** that hosts six Kubernetes Helm charts. The charts are published to a GitHub Pages-based Helm repository at `https://ckotzbauer.github.io/helm-charts` and are also listed on [Artifact Hub](https://artifacthub.io/packages/search?repo=ckotzbauer). The repository contains Kubernetes operators, monitoring tools, and infrastructure utilities -- all focused on Kubernetes cluster operations, security, and observability.

## Tech Stack

- **Helm** (v3) -- all charts use Helm templating with Go templates
- **chart-testing (ct)** -- linting and integration testing via `ct lint` and `ct install`
- **chart-releaser** -- automated chart releases via `helm/chart-releaser-action`
- **kind** -- ephemeral Kubernetes clusters for CI integration tests
- **GitHub Actions** -- CI/CD pipeline
- **Renovate + Dependabot** -- automated dependency updates (Renovate for chart image pins, Dependabot for GitHub Actions)

## Project Structure

```
helm-charts/
├── charts/
│   ├── access-manager/        # RBAC operator chart (apiVersion: v1)
│   ├── cadvisor/              # cAdvisor DaemonSet chart (apiVersion: v1)
│   ├── chekr/                 # Cluster inspection utility chart (apiVersion: v2)
│   ├── postgres-operator/     # Zalando Postgres Operator chart (apiVersion: v2)
│   ├── sbom-operator/         # SBOM cataloging operator chart (apiVersion: v2)
│   └── vulnerability-operator/ # Vulnerability scanning operator chart (apiVersion: v2)
├── .github/
│   ├── workflows/
│   │   ├── lint-test.yaml     # PR lint + integration test
│   │   ├── release.yaml       # Chart release on push to main
│   │   ├── stale.yml          # Stale issue management
│   │   ├── label-issues.yml   # Issue/PR label automation
│   │   └── size-label.yml     # PR size labeling
│   ├── dependabot.yml         # GitHub Actions dependency updates
│   └── label-commands.json    # Slash-command label config
├── ct.yaml                    # chart-testing configuration
├── renovate.json              # Renovate config (extends ckotzbauer/renovate-config)
├── LICENSE                    # Apache 2.0
└── README.md
```

Each chart follows a standard structure:

```
charts/<name>/
├── Chart.yaml
├── values.yaml
├── .helmignore
├── README.md
├── templates/
│   ├── _helpers.tpl
│   ├── deployment.yaml (or daemonset.yaml)
│   ├── serviceaccount.yaml
│   ├── clusterrole.yaml
│   ├── clusterrolebinding.yaml
│   ├── NOTES.txt
│   └── ... (chart-specific templates)
├── ci/                        # (some charts) values files for ct install tests
└── crds/                      # (postgres-operator only) CRD definitions
```

## Charts Overview

### 1. access-manager

- **Chart version:** 0.14.1 | **App version:** 0.13.0 | **API version:** v1
- **Purpose:** Kubernetes operator to simplify RBAC configurations. Manages `RbacDefinition` and `SyncSecretDefinition` custom resources (CRDs defined inline in `templates/crd.yaml`).
- **Image:** `ghcr.io/ckotzbauer/access-manager` (pinned by SHA256 digest)
- **Workload:** Single-replica Deployment with optional leader election when `replicas > 1`
- **RBAC:** Binds to `cluster-admin` ClusterRole
- **Templates:** deployment, serviceaccount, clusterrolebinding, crd, NOTES.txt
- **Key values:** `replicas`, `image`, `resources` (defaults: 10m/128Mi request, 50m/128Mi limit), `securityContext` (non-root UID 1001, read-only root filesystem, all capabilities dropped)

### 2. cadvisor

- **Chart version:** 2.4.1 | **App version:** 0.52.1 | **API version:** v1
- **Purpose:** Deploys Google cAdvisor as a DaemonSet for container resource monitoring and metrics export.
- **Image:** `gcr.io/cadvisor/cadvisor:v0.52.1` (pinned by SHA256 digest)
- **Workload:** DaemonSet with host path volume mounts (`/`, `/var/run`, `/sys`, `/var/lib/docker`, `/dev/disk`)
- **Networking:** ClusterIP Service on port 8080; optional `hostNetwork` mode
- **Monitoring:** Optional Prometheus `ServiceMonitor` (gated by `metrics.enabled`)
- **Security:** Optional PodSecurityPolicy, ClusterRole/ClusterRoleBinding (gated by `podSecurityPolicy.create`); optional securityContext for privileged mode
- **Templates:** daemonset, service, serviceaccount, servicemonitor, psp, clusterrole, clusterrolebinding, NOTES.txt
- **Key values:** `container.additionalArgs` (housekeeping intervals, disabled metrics), `container.hostPaths`, `metrics.enabled/interval/scrapeTimeout`, `podSecurityPolicy.create`, `commonLabels`, `podLabels`

### 3. chekr

- **Chart version:** 0.5.1 | **App version:** 0.5.1 | **API version:** v2, type: application
- **Purpose:** Inspection utility for Kubernetes cluster maintenance. Runs scheduled checks (deprecation analysis, resource usage) as CronJobs and serves HTML/JSON reports via an nginx webserver Deployment.
- **Images:** `ghcr.io/ckotzbauer/chekr` (job, pinned by digest) + `nginxinc/nginx-unprivileged:mainline-alpine` (webserver, pinned by digest)
- **Workload:** Deployment (nginx webserver) + CronJob (chekr commands). Both share a PersistentVolumeClaim for report storage.
- **Storage:** Requires PVC with `ReadWriteMany` access mode by default; configurable `storageClass` and `size` (default 256Mi)
- **Networking:** ClusterIP Service on port 8080; optional Ingress (multi-version API support for K8s >= 1.14)
- **Configuration:** Optional ConfigMap from `job.config` mounted at `/.config/chekr`; commands defined in `job.commands` list
- **CronJob API:** Uses `semverCompare` to select `batch/v1` (K8s >= 1.21) or `batch/v1beta1`
- **Templates:** deployment, cronjob, service, ingress, configmap, persistentvolumeclaim, serviceaccount, clusterrole, clusterrolebinding
- **CI values:** `charts/chekr/ci/ci-values.yaml` overrides persistence to `storageClass: "standard"`, `accessMode: ReadWriteOnce`

### 4. postgres-operator

- **Chart version:** 1.12.0 | **App version:** 1.12.0 | **API version:** v2
- **Purpose:** Deploys the [Zalando Postgres Operator](https://github.com/zalando/postgres-operator) which creates and manages PostgreSQL clusters (Spilo/Patroni-based) in Kubernetes.
- **Image:** `registry.opensource.zalan.do/acid/postgres-operator:v1.11.0` (pinned by SHA256 digest)
- **Workload:** Single-replica Deployment with readiness probe on `/readyz`
- **CRDs:** Stored in `crds/` directory (auto-applied by Helm): `postgresqls.acid.zalan.do`, `operatorconfigurations.acid.zalan.do`, `postgresteams.acid.zalan.do`
- **Configuration:** Extensive `OperatorConfiguration` CR generated from values sections: `configGeneral`, `configUsers`, `configKubernetes`, `configPostgresPodResources`, `configTimeouts`, `configLoadBalancer`, `configAwsOrGcp`, `configLogicalBackup`, `configTeamsApi`, `configConnectionPooler`, `configPatroni`, `configMajorVersionUpgrade`, `configDebug`, `configLoggingRestApi`
- **RBAC:** Detailed ClusterRole with conditional rules based on config (e.g., `enable_crd_registration`, `kubernetes_use_configmaps`, `storage_resize_mode`, `spilo_privileged`, `enableStreams`). Separate ClusterRole for postgres pods (Patroni). Optional aggregate ClusterRoles for K8s default roles (`createAggregateClusterRoles`).
- **Special features:** Pod environment ConfigMap, PriorityClass for database pods (default priority 1000000), controller ID support, pod service account for Postgres pods, checksum annotation on Deployment for config change detection
- **Templates:** deployment, operatorconfiguration, pod-env-configmap, clusterrole, clusterrole-postgres-pod, clusterrolebinding, serviceaccount, service, user-facing-clusterroles, postgres-pod-priority-class, NOTES.txt
- **Key values:** `configGeneral.docker_image` (Spilo image), `configGeneral.workers: 8`, `configPostgresPodResources` (defaults: 100m-1 CPU, 100Mi-500Mi memory), `configConnectionPooler` (PgBouncer), `configLogicalBackup` (S3/GCS/Azure backup)
- **Helpers:** Unique among charts -- defines `postgres-pod.serviceAccountName`, `postgres-pod.priorityClassName`, `postgres-operator.controllerID` in addition to standard name/fullname/chart helpers

### 5. sbom-operator

- **Chart version:** 0.41.0 | **App version:** 0.40.0 | **API version:** v2
- **Purpose:** Catalogues container images running in a Kubernetes cluster and generates SBOMs (Software Bill of Materials) using Syft. Can push SBOMs to multiple targets (git, configmap, etc.).
- **Image:** `ghcr.io/ckotzbauer/sbom-operator` (pinned by SHA256 digest)
- **Workload:** Single-replica Deployment with liveness/readiness probes on `/health:8080`
- **Configuration:** Dual configuration mode -- CLI args via `args` map (rendered as `--key=value` flags) or environment variables via `envVars` list. Supports `jobImageMode` for running as Kubernetes Jobs.
- **RBAC:** ClusterRole with conditional rules: adds configmap CRUD if `targets` contains "configmap"; adds secrets and jobs CRUD if `jobImageMode` is true
- **Volumes:** `work` and `tmp` emptyDir volumes (supports read-only root filesystem); `extraVolumes`/`extraVolumeMounts` for custom mounts
- **Templates:** deployment, serviceaccount, clusterrole, clusterrolebinding, NOTES.txt
- **CI values:** Two test configurations in `ci/`:
  - `all-args-values.yaml` -- configures via `args` map (format, verbosity, cron, targets, git settings)
  - `all-env-values.yaml` -- same config via `envVars` environment variables (SBOM_FORMAT, SBOM_VERBOSITY, etc.)

### 6. vulnerability-operator

- **Chart version:** 0.29.0 | **App version:** 0.27.0 | **API version:** v2
- **Purpose:** Scans SBOMs for known vulnerabilities using Grype. Provides metrics, reports, and filtering capabilities.
- **Image:** `ghcr.io/ckotzbauer/vulnerability-operator` (pinned by SHA256 digest)
- **Workload:** Single-replica Deployment with liveness/readiness probes on `/health:8080`
- **Configuration:** CLI args via `args` map + environment variables via `envVars`. Supports Grype ignore rules (`ignoreRules`) and vulnerability filter config (`filterConfig`) mounted as ConfigMap at `/vuln/`.
- **Monitoring:** Optional Prometheus `ServiceMonitor` (gated by `servicemonitor.enabled`) with relabelings support; Service on port 8080
- **Networking:** Optional Ingress (multi-version API support)
- **Volumes:** `reports` emptyDir (toggleable via `useReportsEmptyDir`), `work`, `tmp`; `extraVolumes`/`extraVolumeMounts`; optional `grype` ConfigMap volume
- **Templates:** deployment, service, serviceaccount, clusterrole, clusterrolebinding, servicemonitor, ingress, configmap, NOTES.txt
- **CI values:** `ci/git.yaml` sets `args.sources: kubernetes`

## Architecture and Patterns

### Helper Template Naming

Two naming conventions exist across charts:

- **Older charts** (access-manager, sbom-operator, vulnerability-operator): Use `app.name`, `app.fullname`, `app.chart`, `app.labels`, `app.selectorLabels`, `app.serviceAccountName` as template names
- **Newer/external charts** (cadvisor, chekr, postgres-operator): Use chart-specific prefixes like `cadvisor.name`, `chekr.fullname`, `postgres-operator.name`

All helpers follow the standard pattern: name truncated to 63 characters, fullname combining release name and chart name, chart label combining name and version.

### Label Patterns

- **Older charts** (access-manager): Use `app`, `chart`, `release`, `heritage` labels
- **Newer charts** (cadvisor, chekr, sbom-operator, vulnerability-operator): Use Kubernetes recommended labels (`app.kubernetes.io/name`, `app.kubernetes.io/instance`, `app.kubernetes.io/version`, `app.kubernetes.io/managed-by`, `helm.sh/chart`)
- **postgres-operator**: Uses `app.kubernetes.io/*` labels directly in templates (not via helper includes)

### Image Pinning

All charts pin container images using SHA256 digests (e.g., `@sha256:...`) for reproducibility and supply-chain security. Some use the format `tag@sha256:...`, others use `@sha256:...` as the tag value itself.

### Security Posture

Most charts enforce security best practices by default:

- `runAsNonRoot: true`
- `readOnlyRootFilesystem: true`
- `allowPrivilegeEscalation: false`
- `capabilities.drop: [ALL]`
- `seccompProfile.type: RuntimeDefault` (sbom-operator, vulnerability-operator, access-manager)
- Non-root user IDs (1001, 1000, 101 depending on chart)

### ServiceAccount Pattern

All charts support conditional ServiceAccount creation (`serviceAccount.create: true`) with an optional custom name. If create is true and no name is set, the fullname template generates the name.

### RBAC Pattern

- Most operator charts use ClusterRole + ClusterRoleBinding (cluster-wide scope)
- access-manager binds to `cluster-admin` directly
- sbom-operator and vulnerability-operator define least-privilege ClusterRoles with only the permissions needed
- postgres-operator has the most complex RBAC with conditional rules based on configuration flags

### Kubernetes Version Compatibility

Charts use `semverCompare` for API version selection:

- Ingress: `networking.k8s.io/v1` (>= 1.19), `networking.k8s.io/v1beta1` (>= 1.14), `extensions/v1beta1` (older)
- CronJob: `batch/v1` (>= 1.21), `batch/v1beta1` (older)

### Configuration Patterns

- **Args map pattern** (sbom-operator, vulnerability-operator): `args` is a key-value map rendered as `--key=value` CLI flags. Empty values render as bare `--key` flags.
- **Config sections pattern** (postgres-operator): Extensive configuration organized into typed sections (`configGeneral`, `configKubernetes`, etc.) rendered into an `OperatorConfiguration` CR using `tpl` for template processing within values.
- **Dual config pattern** (sbom-operator): Supports both `args` map and `envVars` list, giving users flexibility in how they configure the application.

## Build and Development

### Prerequisites

- Helm v3
- Python (for chart-testing)
- chart-testing (`ct`) CLI
- kind (for local integration testing)

### Local Development

```bash
# Add the repo
helm repo add ckotzbauer https://ckotzbauer.github.io/helm-charts

# Lint all charts
ct lint --target-branch main

# Lint a specific chart
helm lint charts/<chart-name>

# Template a chart locally
helm template my-release charts/<chart-name> -f charts/<chart-name>/values.yaml

# Dry-run install
helm install --dry-run --debug my-release charts/<chart-name>
```

### Chart-Testing Configuration

Defined in `/ct.yaml`:

- Remote: `origin`
- Target branch: `main`
- Chart directories: `charts/`
- No additional chart repos configured

## Testing

### CI Integration Tests

Chart-testing (`ct`) runs two phases:

1. **Lint** (`ct lint --target-branch main`): Validates chart structure, templates, and values
2. **Install** (`ct install --target-branch main`): Installs changed charts into a kind cluster (v0.22.0) and verifies they deploy successfully

### CI Values Files

Charts provide `ci/` directories with alternative values for integration testing:

- `charts/chekr/ci/ci-values.yaml` -- overrides persistence settings for CI (standard StorageClass, ReadWriteOnce)
- `charts/sbom-operator/ci/all-args-values.yaml` -- tests args-based configuration
- `charts/sbom-operator/ci/all-env-values.yaml` -- tests envVars-based configuration
- `charts/vulnerability-operator/ci/git.yaml` -- tests with `sources: kubernetes` arg

These files are automatically picked up by `ct install` to run multiple test scenarios per chart.

## CI/CD

### Workflows

| Workflow           | Trigger                  | Purpose                                                           |
| ------------------ | ------------------------ | ----------------------------------------------------------------- |
| `lint-test.yaml`   | Pull request             | Lints all charts, installs changed charts in kind cluster         |
| `release.yaml`     | Push to `main`           | Packages and releases charts using chart-releaser to GitHub Pages |
| `stale.yml`        | Daily cron (`0 0 * * *`) | Marks stale issues/PRs via reusable workflow                      |
| `label-issues.yml` | Issue/PR events          | Automates labeling via reusable workflow                          |
| `size-label.yml`   | PR opened/synced         | Adds size labels to PRs                                           |

### Release Process

1. Make changes to chart(s) and bump `version` in `Chart.yaml`
2. Open a PR -- lint-test workflow runs automatically
3. Merge to `main` -- chart-releaser detects version changes, packages the chart, and publishes to the `gh-pages` branch
4. The release uses `REPO_ACCESS` secret for GitHub API authentication

### Dependency Management

- **Renovate**: Extends `ckotzbauer/renovate-config:default` and `ckotzbauer/renovate-config:weekly`. Handles image tag/digest updates in `values.yaml`.
- **Dependabot**: Configured for `github-actions` ecosystem only (daily interval, reviewer: ckotzbauer, label: `kind/dependencies`).
- All GitHub Actions are pinned by commit SHA with version comments.

## Key Commands

```bash
# Lint all charts
ct lint --target-branch main

# List changed charts (vs main branch)
ct list-changed --target-branch main

# Install changed charts for testing
ct install --target-branch main

# Template a specific chart
helm template my-release charts/sbom-operator

# Template with custom values
helm template my-release charts/vulnerability-operator -f custom-values.yaml

# Package a chart
helm package charts/<chart-name>

# Install a chart from the published repo
helm repo add ckotzbauer https://ckotzbauer.github.io/helm-charts
helm install my-release ckotzbauer/<chart-name>
```

## Important Conventions

1. **Version bumping:** Always increment `version` in `Chart.yaml` when making changes. The chart-releaser only publishes charts whose version has changed.

2. **Image digest pinning:** All images must be pinned by SHA256 digest, not just by tag. This ensures reproducible deployments and protects against supply-chain attacks.

3. **Chart API versions:** Older charts (access-manager, cadvisor) use `apiVersion: v1`; newer charts use `apiVersion: v2`. The v2 charts may use the `type: application` field (chekr does).

4. **No chart dependencies:** None of the charts declare Helm chart dependencies in `Chart.yaml`. All charts are standalone.

5. **CRD placement:** postgres-operator uses the Helm `crds/` directory (auto-installed, never upgraded/deleted by Helm). access-manager embeds CRDs in `templates/crd.yaml` (managed as regular templates).

6. **Security defaults:** Charts ship with restrictive security contexts by default. Do not weaken these without good reason.

7. **Maintainer:** Single maintainer -- `ckotzbauer` (christian.kotzbauer@gmail.com / git@ckotzbauer.de).

8. **Branching:** `main` is the default and release branch. All PRs target `main`.

9. **GitHub Actions pinning:** All Actions references use full commit SHAs with version comments (e.g., `uses: actions/checkout@<sha> # v6`).

10. **Label system:** Issues and PRs use structured labels (`kind/bug`, `kind/feature`, `kind/dependencies`, `lifecycle/stale`, `hold`, size labels). Label commands are available via issue comments for the allowed user.

11. **Indentation style in templates:** Two styles coexist -- older charts use `indent N` (positional), newer charts prefer `nindent N` (with newline prefix). Both are valid Helm patterns.

12. **NOTES.txt:** All operator charts (access-manager, cadvisor, sbom-operator, vulnerability-operator, postgres-operator) include post-install notes showing how to verify the deployment.
