## Setup

If needed, create a namespace and set that as the default.

[Install kustomize](https://kubernetes-sigs.github.io/kustomize/installation/), since the support bundled in kubectl is lacking remote resources support.

Apply prometheus operator and monitors:
```shell script
kustomize build github.com/itzg/prom-grafana-deployment/prom-monitors | kubectl apply -f -
```

Optionally apply kubernetes pod metrics exporter:
```shell script
kustomize build github.com/itzg/prom-grafana-deployment/kube-metrics | kubectl apply -f -
```

Apply grafana manifests:
```shell script
kustomize build github.com/itzg/prom-grafana-deployment/grafana | kubectl apply -f -
```

Port forward the Grafana service using:
```
kubectl port-forward service/grafana :80
```

Access Grafana at the forwarding port and use the default credentials admin/admin. You can "change" the admin password to "admin" if you will be continuing to use port forwarding.

## Pre-configured PodMonitors

The manifests in [prom-monitors](prom-monitors) include two pre-configured `PodMonitor`s that select the following pod labels and scrape as described:

- `prometheus/scrape-type: metrics` : uses [the default scrape strategy](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config) by scraping the HTTP path `/metrics` at [the container port](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.19/#containerport-v1-core) named "scrape"
- `prometheus/scrape-type: actuator` : scrapes Spring Boot applications via [the Actuator Prometheus endpoint](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html#production-ready-metrics-export-prometheus) `/actuator/prometheus` at the container port named "actuator"

> NOTE: make sure to declare those labels in the pod's metadata and not the deployment or statefulset metadata.