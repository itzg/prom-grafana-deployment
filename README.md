## Setup

If needed, create a namespace and set that as the default.

Install the Prometheus Operator using:
```shell script
kubectl apply -f https://raw.githubusercontent.com/coreos/prometheus-operator/v0.42.1/bundle.yaml
```

> NOTE [the prerequisites](https://github.com/coreos/prometheus-operator#prerequisites) for the operator and cluster version 

> [Check here](https://github.com/prometheus-operator/prometheus-operator/releases/latest) for the latest release of the operator

Apply prometheus manifests:
```shell script
kubectl apply -f prom
```

Apply grafana manifests:
```shell script
kubectl apply -f grafana
```

Port forward the Grafana service using:
```
kubectl port-forward service/grafana :80
```

Access Grafana at the forwarding port and use the default credentials admin/admin. You can "change" the admin password to "admin" if you will be continuing to use port forwarding.

## Pre-configured PodMonitors

The manifests in `prom` include two pre-configured `PodMonitor`s that select the following pod labels and scrape as described:

- `prometheus/scrape-type: metrics` : uses [the default scrape strategy](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config) by scraping the HTTP path `/metrics` at [the container port](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.19/#containerport-v1-core) named "scrape"
- `prometheus/scrape-type: actuator` : scrapes Spring Boot applications via [the Actuator Prometheus endpoint](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html#production-ready-metrics-export-prometheus) `/actuator/prometheus` at the container port named "actuator"

> NOTE: make sure to declare those labels in the pod's metadata and not the deployment or statefulset metadata.