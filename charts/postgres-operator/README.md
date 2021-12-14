# Postgres-operator

Postgres Operator creates and manages PostgreSQL clusters running in Kubernetes

Learn more: [https://github.com/zalando/postgres-operator](https://github.com/zalando/postgres-operator)

## TL;DR;

```bash
$ helm install ckotzbauer/postgres-operator
```

## Installing the Chart

To install the chart with the release name `my-release`:

```bash
$ helm install my-release ckotzbauer/postgres-operator
```

The command deploys the operator on the Kubernetes cluster using the default configuration. The [configuration](#configuration) section lists the parameters that can be configured during installation.

## Uninstalling the Chart

To uninstall/delete the `my-release` installation:

```bash
$ helm delete my-release
```
The command removes all the Kubernetes components associated with the chart and deletes the release.


## Configuration

The following table lists the configurable parameters of the Postgres-operator chart and their default values.

| Parameter                | Description             | Default        |
| ------------------------ | ----------------------- | -------------- |
| `image.registry` |  | `"registry.opensource.zalan.do"` |
| `image.repository` |  | `"acid/postgres-operator"` |
| `image.tag` |  | `"v1.7.0"` |
| `image.pullPolicy` |  | `"IfNotPresent"` |
| `podAnnotations` |  | `{}` |
| `podLabels` |  | `{}` |
| `enableJsonLogging` |  | `false` |
| `configGeneral.enable_crd_validation` |  | `true` |
| `configGeneral.enable_lazy_spilo_upgrade` |  | `false` |
| `configGeneral.enable_pgversion_env_var` |  | `true` |
| `configGeneral.enable_shm_volume` |  | `true` |
| `configGeneral.enable_spilo_wal_path_compat` |  | `false` |
| `configGeneral.etcd_host` |  | `""` |
| `configGeneral.docker_image` |  | `"registry.opensource.zalan.do/acid/spilo-13:2.1-p1"` |
| `configGeneral.min_instances` |  | `-1` |
| `configGeneral.max_instances` |  | `-1` |
| `configGeneral.repair_period` |  | `"5m"` |
| `configGeneral.resync_period` |  | `"30m"` |
| `configGeneral.workers` |  | `8` |
| `configUsers.replication_username` |  | `"standby"` |
| `configUsers.super_username` |  | `"postgres"` |
| `configMajorVersionUpgrade.major_version_upgrade_mode` |  | `"off"` |
| `configMajorVersionUpgrade.minimal_major_version` |  | `"9.5"` |
| `configMajorVersionUpgrade.target_major_version` |  | `"13"` |
| `configKubernetes.cluster_domain` |  | `"cluster.local"` |
| `configKubernetes.cluster_labels.application` |  | `"spilo"` |
| `configKubernetes.cluster_name_label` |  | `"cluster-name"` |
| `configKubernetes.enable_cross_namespace_secret` |  | `false` |
| `configKubernetes.enable_init_containers` |  | `true` |
| `configKubernetes.enable_pod_antiaffinity` |  | `false` |
| `configKubernetes.enable_pod_disruption_budget` |  | `true` |
| `configKubernetes.enable_sidecars` |  | `true` |
| `configKubernetes.pdb_name_format` |  | `"postgres-{cluster}-pdb"` |
| `configKubernetes.pod_antiaffinity_topology_key` |  | `"kubernetes.io/hostname"` |
| `configKubernetes.pod_management_policy` |  | `"ordered_ready"` |
| `configKubernetes.pod_role_label` |  | `"spilo-role"` |
| `configKubernetes.pod_terminate_grace_period` |  | `"5m"` |
| `configKubernetes.secret_name_template` |  | `"{username}.{cluster}.credentials.{tprkind}.{tprgroup}"` |
| `configKubernetes.spilo_privileged` |  | `false` |
| `configKubernetes.spilo_allow_privilege_escalation` |  | `true` |
| `configKubernetes.storage_resize_mode` |  | `"pvc"` |
| `configKubernetes.watched_namespace` | listen to all namespaces | `"*"` |
| `configPostgresPodResources.default_cpu_limit` |  | `"1"` |
| `configPostgresPodResources.default_cpu_request` |  | `"100m"` |
| `configPostgresPodResources.default_memory_limit` |  | `"500Mi"` |
| `configPostgresPodResources.default_memory_request` |  | `"100Mi"` |
| `configPostgresPodResources.min_cpu_limit` |  | `"250m"` |
| `configPostgresPodResources.min_memory_limit` |  | `"250Mi"` |
| `configTimeouts.pod_deletion_wait_timeout` |  | `"10m"` |
| `configTimeouts.pod_label_wait_timeout` |  | `"10m"` |
| `configTimeouts.ready_wait_interval` |  | `"3s"` |
| `configTimeouts.ready_wait_timeout` |  | `"30s"` |
| `configTimeouts.resource_check_interval` |  | `"3s"` |
| `configTimeouts.resource_check_timeout` |  | `"10m"` |
| `configLoadBalancer.db_hosted_zone` |  | `"db.example.com"` |
| `configLoadBalancer.enable_master_load_balancer` |  | `false` |
| `configLoadBalancer.enable_replica_load_balancer` |  | `false` |
| `configLoadBalancer.external_traffic_policy` |  | `"Cluster"` |
| `configLoadBalancer.master_dns_name_format` |  | `"{cluster}.{team}.{hostedzone}"` |
| `configLoadBalancer.replica_dns_name_format` |  | `"{cluster}-repl.{team}.{hostedzone}"` |
| `configDebug.debug_logging` |  | `true` |
| `configDebug.enable_database_access` |  | `true` |
| `configLoggingRestApi.api_port` |  | `8080` |
| `configLoggingRestApi.cluster_history_entries` |  | `1000` |
| `configLoggingRestApi.ring_log_lines` |  | `100` |
| `configAwsOrGcp.aws_region` |  | `"eu-central-1"` |
| `configAwsOrGcp.enable_ebs_gp3_migration` |  | `false` |
| `configLogicalBackup.logical_backup_docker_image` |  | `"registry.opensource.zalan.do/acid/logical-backup:v1.7.0"` |
| `configLogicalBackup.logical_backup_job_prefix` |  | `"logical-backup-"` |
| `configLogicalBackup.logical_backup_provider` |  | `"s3"` |
| `configLogicalBackup.logical_backup_s3_access_key_id` |  | `""` |
| `configLogicalBackup.logical_backup_s3_bucket` |  | `"my-bucket-url"` |
| `configLogicalBackup.logical_backup_s3_region` |  | `""` |
| `configLogicalBackup.logical_backup_s3_endpoint` |  | `""` |
| `configLogicalBackup.logical_backup_s3_secret_access_key` |  | `""` |
| `configLogicalBackup.logical_backup_s3_sse` |  | `"AES256"` |
| `configLogicalBackup.logical_backup_schedule` |  | `"30 00 * * *"` |
| `configTeamsApi.enable_admin_role_for_users` |  | `true` |
| `configTeamsApi.enable_postgres_team_crd` |  | `false` |
| `configTeamsApi.enable_postgres_team_crd_superusers` |  | `false` |
| `configTeamsApi.enable_team_member_deprecation` |  | `false` |
| `configTeamsApi.enable_team_superuser` |  | `false` |
| `configTeamsApi.enable_teams_api` |  | `false` |
| `configTeamsApi.pam_role_name` |  | `"zalandos"` |
| `configTeamsApi.postgres_superuser_teams` |  | `["postgres_superusers"]` |
| `configTeamsApi.protected_role_names` |  | `["admin"]` |
| `configTeamsApi.role_deletion_suffix` |  | `"_deleted"` |
| `configTeamsApi.team_admin_role` |  | `"admin"` |
| `configTeamsApi.team_api_role_configuration.log_statement` |  | `"all"` |
| `configConnectionPooler.connection_pooler_schema` |  | `"pooler"` |
| `configConnectionPooler.connection_pooler_user` |  | `"pooler"` |
| `configConnectionPooler.connection_pooler_image` |  | `"registry.opensource.zalan.do/acid/pgbouncer:master-18"` |
| `configConnectionPooler.connection_pooler_max_db_connections` |  | `60` |
| `configConnectionPooler.connection_pooler_mode` |  | `"transaction"` |
| `configConnectionPooler.connection_pooler_number_of_instances` |  | `2` |
| `configConnectionPooler.connection_pooler_default_cpu_request` |  | `"500m"` |
| `configConnectionPooler.connection_pooler_default_memory_request` |  | `"100Mi"` |
| `configConnectionPooler.connection_pooler_default_cpu_limit` |  | `"1"` |
| `configConnectionPooler.connection_pooler_default_memory_limit` |  | `"100Mi"` |
| `rbac.create` |  | `true` |
| `crd.create` |  | `true` |
| `serviceAccount.create` |  | `true` |
| `serviceAccount.name` |  | `null` |
| `podServiceAccount.name` |  | `"postgres-pod"` |
| `priorityClassName` |  | `""` |
| `podPriorityClassName` |  | `""` |
| `resources.limits.cpu` |  | `"500m"` |
| `resources.limits.memory` |  | `"500Mi"` |
| `resources.requests.cpu` |  | `"100m"` |
| `resources.requests.memory` |  | `"250Mi"` |
| `securityContext.runAsUser` |  | `1000` |
| `securityContext.runAsNonRoot` |  | `true` |
| `securityContext.readOnlyRootFilesystem` |  | `true` |
| `securityContext.allowPrivilegeEscalation` |  | `false` |
| `affinity` |  | `{}` |
| `nodeSelector` |  | `{}` |
| `tolerations` |  | `[]` |
| `controllerID.create` |  | `false` |
| `controllerID.name` |  | `null` |
| `pod_environment_config` |  | `{}` |


## Changes compared to the [official chart](https://github.com/zalando/postgres-operator/tree/master/charts/postgres-operator)

- No `ConfigMap` config-target.
- `pod_environment_configmap` is created automatically. Contents can be configured via `pod_environment_config`.

