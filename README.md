# ANOW Application Helm Chart

This Helm chart deploys the ANOW application to Kubernetes.  
Below are the configurable parameters available in the `values.yaml` file.

## 📦Docker Image

This chart requires a custom Docker image of the AutomateNOW! application.  
You can find the base image on [Docker Hub](https://hub.docker.com/r/anowautomate/app).  

>⚠️ You need to build your own image based on the AutomateNOW app and push it to your own registry.

## Parameters

### `customName`

- **Description:**  
  Allows setting a custom resource name for Kubernetes resources in the chart.  
  If not set, defaults to `{{ .Release.Name }}-{{ .Chart.Name }}`.

- **Type:** `string`  
- **Default:** `""` (empty string)

---

### Global parameters

Settings for the ANOW application container image.

| Name | Description | Value |
| ---- | ----------- |------ |
| `global.postgresql.auth.database` | Name for a custom database to create | "anow" |
| `global.postgresql.auth.username` | Name for a custom user to create | "anowapp" |
| `global.postgresql.auth.password` | Password for the custom user to create | "" |
| `global.postgresql.auth.existingSecret` | Name of existing secret to use for PostgreSQL | "" |
| `global.postgresql.auth.secretKeys.userPasswordKey` | Name of key in existing secret to use for PostgreSQL credentials.  Only used when `global.postgresql.auth.existingSecret` is set| "password" |
| `global.postgresql.service.ports.postgresql` | PostgreSQL service port | 5432 |
| `global.postgresql.external.host` | Hostname or IP address of the external PostgreSQL server. Used when `postgresql.enabled` is set to `false` | "" |

---

### Anow parameters

Configuration parameters for the ANOW application.

| Name | Description | Value |
| ---- | ----------- |------ |
| `anow.JVM_TIMEZONE` | Base JVM Timezone | "GMT"|
| `anow.ANOW_URL` | URL where the app is accessible. If using a load balancer, set this to the load balancer’s URL | "" |
| `anow.ANOW_SUPERUSER` | Superuser account name. Leave empty to disable the superuser option | "" |
| `anow.image.repository` | Docker image repository for the application | "" |
| `anow.image.tag` | Docker image tag (version) for the application | "" |
| `anow.image.pullPolicy` | Image pull policy for the container | "IfNotPresent" |
| `anow.image.pullSecrets` | Specify image pull secrets | [ ] |
| `anow.replicaCount` | Number of pod replicas to deploy | 1|
| `anow.strategy` | Deployment update strategy type | "RollingUpdate" |
| `anow.resources.requests.memory` | Memory requested by the container | "" |
| `anow.resources.requests.cpu` | CPU requested by the container | "" |
| `anow.resources.limits.memory` | Maximum memory the container is allowed to use | "" |
| `anow.resources.limits.cpu` | Maximum CPU the container is allowed to use | "" |
| `anow.volumes.log` | Persistent volume size for application logs | "10Gi" |
| `anow.service.port` | Port on which the application service is exposed | 8080|
| `anow.service.type` | Kubernetes Service type | "ClusterIP" |
---

### PostgreSQL parameters

This chart uses the official Bitnami PostgreSQL Helm chart as a dependency.
For a full list of configurable parameters and their descriptions, please refer to the documentation at the link above.
`https://artifacthub.io/packages/helm/bitnami/postgresql/15.5.38`


| Name | Description | Value |
| ---- | ----------- |------ |
| `postgresql.enabled` | Specifies whether to deploy the Bitnami PostgreSQL subchart. Set to `false` if using an external PostgreSQL instance. | true|
| `postgresql.primary.resources.requests.memory` | Memory requested by the container | "" |
| `postgresql.primary.resources.requests.cpu` | CPU requested by the container | "" |
| `postgresql.primary.resources.limits.memory` | Maximum memory the container is allowed to use | "" |
| `postgresql.primary.resources.limits.cpu` | Maximum CPU the container is allowed to use | "" |
| `postgresql.primary.persistence.size` | PVC Storage Request for PostgreSQL volume | "" |
---

### RAbbitMQ parameters

This chart uses the official Bitnami RabbitMQ Helm chart as a dependency.
For a full list of configurable parameters and their descriptions, please refer to the documentation at the link above.
`https://artifacthub.io/packages/helm/bitnami/rabbitmq/14.7.0`

| Name | Description | Value |
| ---- | ----------- |------ |
| `rabbitmq.enabled` |  Specifies whether to deploy the Bitnami RabbitMQ subchart. Set to `false` if using an external RabbitMQ instance | true|
| `rabbitmq.auth.username` | RabbitMQ application username | "anow" |
| `rabbitmq.auth.password` | 	RabbitMQ application password| "anow123" |
| `rabbitmq.auth.erlangCookie` | Erlang cookie to determine whether different nodes are allowed to communicate with each other | "AEF1231JHASDN123324SLDFG" |
| `rabbitmq.auth.tls.enabled` | Enable TLS support on RabbitMQ | false |
| `rabbitmq.auth.tls.caCertificate` | Certificate Authority (CA) bundle content | "" |
| `rabbitmq.auth.tls.serverCertificate` | Server certificate content | "" |
| `rabbitmq.auth.tls.serverKey` | Server private key content | "" |
| `rabbitmq.replicaCount` | Number of RabbitMQ replicas to deploy | 1 |
| `rabbitmq.resources.requests.cpu` | CPU requested by the container | "" |
| `rabbitmq.resources.requests.memory` | Memory requested by the container | "" |
| `rabbitmq.resources.limits.cpu` | Maximum CPU the container is allowed to use | "" |
| `rabbitmq.resources.limits.memory` | Maximum memory the container is allowed to use | "" |
| `rabbitmq.service.type` | Kubernetes Service type | "ClusterIP" |
| `rabbitmq.service.portEnabled` | Amqp port. Cannot be disabled when `rabbitmq.auth.tls.enabled` is `false` | true |
---

## Install
```sh
helm install anow-app https://anowautomate.github.io/app-helm-chart/anow-app-0.1.0.tgz -f values.yaml
```

## 🐞 Quick Troubleshooting
If the Helm release does not start correctly or resources behave unexpectedly, try these commands:
```sh
helm list -n <namespace>
```
- Shows all Helm releases in the namespace and their status.

```sh
helm status <release_name> -n <namespace>
```
- Displays the release details, including deployed resources and events.

```sh
kubectl get pods -n <namespace>
```
- Lists pods with their status (Running, Pending, CrashLoopBackOff, etc.).

```sh
kubectl describe pod <pod_name> -n <namespace>
```
- Provides detailed information about the pod, events, and possible issues.

```sh
kubectl logs <pod_name> -n <namespace>
```
- Displays logs from the container to help diagnose startup or runtime errors.
