# Zammad Helm Chart

This directory contains a Kubernetes chart to deploy Zammad

## Prerequisites Details

* Kubernetes 1.8+

## Chart Details

This chart will do the following:

* Implement a zammad deployment
* Optionally, deploy a consul agent in the pod

Please note that a backend service for zammad (for example, Consul) must
be deployed beforehand and configured with the `zammad.config` option. YAML
provided under this option will be converted to JSON for the final zammad
`config.json` file.

> See https://www.zammadproject.io/docs/configuration/ for more information.

## Installing the Chart

To install the chart, use the following, this backs zammad with a Consul cluster:

```console
$ helm repo add incubator http://storage.googleapis.com/kubernetes-charts-incubator
$ helm install incubator/zammad --set zammad.dev=false --set zammad.config.storage.consul.address="myconsul-svc-name:8500",zammad.config.storage.consul.path="zammad"
```



## Configuration

The following table lists the configurable parameters of the zammad chart and their default values.

|             Parameter             |              Description                 |               Default               |
|-----------------------------------|------------------------------------------|-------------------------------------|
| `imagePullSecret`                 | The name of the secret to use if pulling from a private registry | `nil`       |
| `image.pullPolicy`                | Container pull policy                    | `IfNotPresent`                      |
| `image.repository`                | Container image to use                   | `zammad`                             |
| `image.tag`                       | Container image tag to deploy            | `0.10.1`                            |
| `zammad.dev`                       | Use zammad in dev mode                    | true (set to false in production)   |
| `zammad.extraEnv`                  | Extra env vars for zammad pods            | `{}`                                |
| `zammad.extraContainers`           | Sidecar containers to add to the zammad pod | `{}`                              |
| `zammad.extraVolumes`              | Additional volumes to the controller pod | `{}`                                |
| `zammad.customSecrets`             | Custom secrets available to zammad        | `[]`                                |
| `zammad.config`                    | zammad configuration                      | No default backend                  |
| `replicaCount`                    | k8s replicas                             | `3`                                 |
| `resources.limits.cpu`            | Container requested CPU                  | `nil`                               |
| `resources.limits.memory`         | Container requested memory               | `nil`                               |
| `affinity`                        | Affinity settings                        | See values.yaml                     |
| `service.loadBalancerSourceRanges`| IP whitelist for service type loadbalancer   | `[]`                            |
| `service.annotations`             | Annotations for service                  | `{}`                                |
| `annotations`                     | Annotations for deployment               | `{}`                                |
| `ingress.labels`                  | Labels for ingress                       | `{}`                                |
| `podAnnotations`                  | Annotations for pods                     | `{}`                                |
| `consulAgent.join`                | If set, start start a consul agent       | `nil`                               |
| `consulAgent.repository`          | Container image for consul agent         | `consul`                            |
| `consulAgent.tag`                 | Container image tag for consul agent     | `1.0.7`                             |
| `consulAgent.pullPolicy`          | Container pull policy for consul agent   | `IfNotPresent`                      |
| `consulAgent.gossipKeySecretName` | k8s secret containing gossip key         | `nil` (see values.yaml for details) |
| `consulAgent.HttpPort`            | HTTP port for consul agent API           | `8500`                              |

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`.

## Optional Consul Agent

If you are using the consul storage for zammad, you might want a local
consul agent to handle health checks.  By setting `consulAgent.join`
to your consul server, an agent will be started in the zammad pod.  In
this case, you should configure zammad to connect to consul over
`localhost`.  For example:

```yaml
zammad:
  dev: False
  config:
    storage:
      consul:
        address: "localhost:8500"
consulAgent:
  join: consul.service.consul
```

If you are using the `stable/consul` helm chart, consul communications
are encrypted with a gossip key.  You can configure a secret with the
same format as that chart and specify it in the
`consulAgent.gossipKeySecretName` parameter.

## Using zammad

Once the zammad pod is ready, it can be accessed using a `kubectl
port-forward`:

```console
$ kubectl port-forward zammad-pod 8200
$ export zammad_ADDR=http://127.0.0.1:8200
$ zammad status
```
