# Zammad Helm Chart

This directory contains a Kubernetes chart to deploy Zammad


## Prerequisites Details

* Kubernetes 1.8+


## Chart Details

This chart will do the following:

* Install Zammad deployment
* Install Elasticsearch, Memcached & PostgreSQL as requirements


## Installing the Chart

To install the chart use the following:

```console
$ helm repo add incubator http://storage.googleapis.com/kubernetes-charts-incubator
$ helm install incubator/zammad
```

## Configuration

The following table lists the configurable parameters of the zammad chart and their default values.

|             Parameter             |              Description                 |               Default               |
|-----------------------------------|------------------------------------------|-------------------------------------|
| `image.repository`                | Container image to use                   | `zammad/zammad-docker-compose`      |
| `image.tag`                       | Container image tag to deploy            | `2.5.0-43`                          |
| `image.pullPolicy`                | Container pull policy                    | `IfNotPresent`                      |

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`.


## Using zammad

Once the zammad pod is ready, it can be accessed using a `kubectl port-forward`:

```console
$ kubectl port-forward zammad-pod 80
```
