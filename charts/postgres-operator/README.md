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

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| image.registry | string | `"registry.opensource.zalan.do"` |  |
| image.repository | string | `"acid/postgres-operator"` |  |
| image.tag | string | `"v1.8.0"` |  |
| image.pullPolicy | string | `"IfNotPresent"` |  |
| podAnnotations | object | `{}` |  |
| podLabels | object | `{}` |  |
| enableJsonLogging | bool | `false` |  |
| configGeneral.enable_crd_registration | bool | `true` |  |
| configGeneral.crd_categories[0] | string | `"all"` |  |
| configGeneral.enable_lazy_spilo_upgrade | bool | `false` |  |
| configGeneral.enable_pgversion_env_var | bool | `true` |  |
| configGeneral.enable_shm_volume | bool | `true` |  |
| configGeneral.enable_spilo_wal_path_compat | bool | `false` |  |
| configGeneral.etcd_host | string | `""` |  |
| configGeneral.docker_image | string | `"registry.opensource.zalan.do/acid/spilo-14:2.1-p5"` |  |
| configGeneral.min_instances | int | `-1` |  |
| configGeneral.max_instances | int | `-1` |  |
| configGeneral.repair_period | string | `"5m"` |  |
| configGeneral.resync_period | string | `"30m"` |  |
| configGeneral.workers | int | `8` |  |
| configUsers.enable_password_rotation | bool | `false` |  |
| configUsers.password_rotation_interval | int | `90` |  |
| configUsers.password_rotation_user_retention | int | `180` |  |
| configUsers.replication_username | string | `"standby"` |  |
| configUsers.super_username | string | `"postgres"` |  |
| configMajorVersionUpgrade.major_version_upgrade_mode | string | `"off"` |  |
| configMajorVersionUpgrade.minimal_major_version | string | `"9.6"` |  |
| configMajorVersionUpgrade.target_major_version | string | `"14"` |  |
| configKubernetes.cluster_domain | string | `"cluster.local"` |  |
| configKubernetes.cluster_labels.application | string | `"spilo"` |  |
| configKubernetes.cluster_name_label | string | `"cluster-name"` |  |
| configKubernetes.enable_cross_namespace_secret | bool | `false` |  |
| configKubernetes.enable_init_containers | bool | `true` |  |
| configKubernetes.enable_pod_antiaffinity | bool | `false` |  |
| configKubernetes.enable_pod_disruption_budget | bool | `true` |  |
| configKubernetes.enable_sidecars | bool | `true` |  |
| configKubernetes.pdb_name_format | string | `"postgres-{cluster}-pdb"` |  |
| configKubernetes.pod_antiaffinity_topology_key | string | `"kubernetes.io/hostname"` |  |
| configKubernetes.pod_management_policy | string | `"ordered_ready"` |  |
| configKubernetes.pod_role_label | string | `"spilo-role"` |  |
| configKubernetes.pod_terminate_grace_period | string | `"5m"` |  |
| configKubernetes.secret_name_template | string | `"{username}.{cluster}.credentials.{tprkind}.{tprgroup}"` |  |
| configKubernetes.spilo_privileged | bool | `false` |  |
| configKubernetes.spilo_allow_privilege_escalation | bool | `true` |  |
| configKubernetes.storage_resize_mode | string | `"pvc"` |  |
| configKubernetes.watched_namespace | string | `"*"` |  |
| configPostgresPodResources.default_cpu_limit | string | `"1"` |  |
| configPostgresPodResources.default_cpu_request | string | `"100m"` |  |
| configPostgresPodResources.default_memory_limit | string | `"500Mi"` |  |
| configPostgresPodResources.default_memory_request | string | `"100Mi"` |  |
| configPostgresPodResources.min_cpu_limit | string | `"250m"` |  |
| configPostgresPodResources.min_memory_limit | string | `"250Mi"` |  |
| configTimeouts.patroni_api_check_interval | string | `"1s"` |  |
| configTimeouts.patroni_api_check_timeout | string | `"5s"` |  |
| configTimeouts.pod_deletion_wait_timeout | string | `"10m"` |  |
| configTimeouts.pod_label_wait_timeout | string | `"10m"` |  |
| configTimeouts.ready_wait_interval | string | `"3s"` |  |
| configTimeouts.ready_wait_timeout | string | `"30s"` |  |
| configTimeouts.resource_check_interval | string | `"3s"` |  |
| configTimeouts.resource_check_timeout | string | `"10m"` |  |
| configLoadBalancer.db_hosted_zone | string | `"db.example.com"` |  |
| configLoadBalancer.enable_master_load_balancer | bool | `false` |  |
| configLoadBalancer.enable_master_pooler_load_balancer | bool | `false` |  |
| configLoadBalancer.enable_replica_load_balancer | bool | `false` |  |
| configLoadBalancer.enable_replica_pooler_load_balancer | bool | `false` |  |
| configLoadBalancer.external_traffic_policy | string | `"Cluster"` |  |
| configLoadBalancer.master_dns_name_format | string | `"{cluster}.{team}.{hostedzone}"` |  |
| configLoadBalancer.replica_dns_name_format | string | `"{cluster}-repl.{team}.{hostedzone}"` |  |
| configDebug.debug_logging | bool | `true` |  |
| configDebug.enable_database_access | bool | `true` |  |
| configLoggingRestApi.api_port | int | `8080` |  |
| configLoggingRestApi.cluster_history_entries | int | `1000` |  |
| configLoggingRestApi.ring_log_lines | int | `100` |  |
| configAwsOrGcp.aws_region | string | `"eu-central-1"` |  |
| configAwsOrGcp.enable_ebs_gp3_migration | bool | `false` |  |
| configLogicalBackup.logical_backup_docker_image | string | `"registry.opensource.zalan.do/acid/logical-backup:v1.8.0"` |  |
| configLogicalBackup.logical_backup_job_prefix | string | `"logical-backup-"` |  |
| configLogicalBackup.logical_backup_provider | string | `"s3"` |  |
| configLogicalBackup.logical_backup_s3_access_key_id | string | `""` |  |
| configLogicalBackup.logical_backup_s3_bucket | string | `"my-bucket-url"` |  |
| configLogicalBackup.logical_backup_s3_region | string | `""` |  |
| configLogicalBackup.logical_backup_s3_endpoint | string | `""` |  |
| configLogicalBackup.logical_backup_s3_secret_access_key | string | `""` |  |
| configLogicalBackup.logical_backup_s3_sse | string | `"AES256"` |  |
| configLogicalBackup.logical_backup_s3_retention_time | string | `""` |  |
| configLogicalBackup.logical_backup_schedule | string | `"30 00 * * *"` |  |
| configTeamsApi.enable_admin_role_for_users | bool | `true` |  |
| configTeamsApi.enable_postgres_team_crd | bool | `false` |  |
| configTeamsApi.enable_postgres_team_crd_superusers | bool | `false` |  |
| configTeamsApi.enable_team_member_deprecation | bool | `false` |  |
| configTeamsApi.enable_team_superuser | bool | `false` |  |
| configTeamsApi.enable_teams_api | bool | `false` |  |
| configTeamsApi.pam_role_name | string | `"zalandos"` |  |
| configTeamsApi.postgres_superuser_teams[0] | string | `"postgres_superusers"` |  |
| configTeamsApi.protected_role_names[0] | string | `"admin"` |  |
| configTeamsApi.protected_role_names[1] | string | `"cron_admin"` |  |
| configTeamsApi.role_deletion_suffix | string | `"_deleted"` |  |
| configTeamsApi.team_admin_role | string | `"admin"` |  |
| configTeamsApi.team_api_role_configuration.log_statement | string | `"all"` |  |
| configConnectionPooler.connection_pooler_schema | string | `"pooler"` |  |
| configConnectionPooler.connection_pooler_user | string | `"pooler"` |  |
| configConnectionPooler.connection_pooler_image | string | `"registry.opensource.zalan.do/acid/pgbouncer:master-22"` |  |
| configConnectionPooler.connection_pooler_max_db_connections | int | `60` |  |
| configConnectionPooler.connection_pooler_mode | string | `"transaction"` |  |
| configConnectionPooler.connection_pooler_number_of_instances | int | `2` |  |
| configConnectionPooler.connection_pooler_default_cpu_request | string | `"500m"` |  |
| configConnectionPooler.connection_pooler_default_memory_request | string | `"100Mi"` |  |
| configConnectionPooler.connection_pooler_default_cpu_limit | string | `"1"` |  |
| configConnectionPooler.connection_pooler_default_memory_limit | string | `"100Mi"` |  |
| enableStreams | bool | `false` |  |
| rbac.create | bool | `true` |  |
| rbac.createAggregateClusterRoles | bool | `false` |  |
| serviceAccount.create | bool | `true` |  |
| serviceAccount.name | string | `nil` |  |
| podServiceAccount.name | string | `"postgres-pod"` |  |
| priorityClassName | string | `""` |  |
| podPriorityClassName | string | `""` |  |
| resources.limits.cpu | string | `"500m"` |  |
| resources.limits.memory | string | `"500Mi"` |  |
| resources.requests.cpu | string | `"100m"` |  |
| resources.requests.memory | string | `"250Mi"` |  |
| securityContext.runAsUser | int | `1000` |  |
| securityContext.runAsNonRoot | bool | `true` |  |
| securityContext.readOnlyRootFilesystem | bool | `true` |  |
| securityContext.allowPrivilegeEscalation | bool | `false` |  |
| securityContext.capabilities.drop[0] | string | `"ALL"` |  |
| affinity | object | `{}` |  |
| nodeSelector | object | `{}` |  |
| tolerations | list | `[]` |  |
| controllerID.create | bool | `false` |  |
| controllerID.name | string | `nil` |  |
| pod_environment_config | object | `{}` |  |


## Changes compared to the [official chart](https://github.com/zalando/postgres-operator/tree/master/charts/postgres-operator)

- No `ConfigMap` config-target.
- `pod_environment_configmap` is created automatically. Contents can be configured via `pod_environment_config`.
- Enhanced security (dropped ALL capabilities for the operator-pod).

