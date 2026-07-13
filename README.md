# Cloud-Native Observability & Security

A Kubernetes monitoring stack built from scratch — nginx workload, Prometheus metrics pipeline, Grafana dashboards — then stress-tested with a live denial-of-service simulation to validate that the observability stack actually detects an attack in real time.

## Skills demonstrated

| Area | Tools / Technologies |
|---|---|
| Container orchestration | Kubernetes (`kind`), `kubectl`, Deployments/Services/ConfigMaps |
| Infrastructure as code | Declarative YAML manifests |
| Metrics & monitoring | Prometheus, Helm, custom exporters, scrape config |
| Visualization | Grafana, dashboard provisioning, datasource configuration |
| Network security testing | DoS simulation (`slowhttptest`), traffic generation, live metrics correlation |
| Systems networking | Multi-hop SSH tunneling (Mac → gateway → VM → cluster) |
| Security engineering | Threat-informed hardening recommendations (TLS, rate limiting, auth, firewalling, resource limits) |

## What was built

1. **Kubernetes cluster** — local cluster via `kind`, verified with `kubectl get nodes`. ([`01`](screenshots/01-kind-cluster-created.png))
2. **nginx workload** — deployment + ConfigMap serving both an HTML page and a Prometheus metrics endpoint, exposed externally via a NodePort `Service`. ([`02`](screenshots/02-nginx-html-configmap-applied.png), [`03`](screenshots/03-nginx-config-metrics-endpoint.png), [`04`](screenshots/04-nginx-deployment-applied.png), [`05`](screenshots/05-nginx-pod-running.png), [`06`](screenshots/06-nginx-service-nodeport-applied.png))
3. **Prometheus exporter** — deployed and exposed as its own service, scraping the nginx metrics endpoint. ([`07`](screenshots/07-promexporter-deployment-applied.png), [`08`](screenshots/08-promexporter-service-and-services-list.png))
4. **Prometheus server** — deployed via Helm into a dedicated `monitoring` namespace ([`09`](screenshots/09-monitoring-namespace-pods.png)); configured with a custom scrape job for the exporter; accessed locally through a chain of SSH tunnels across four network hops; traffic generation confirmed the scrape pipeline end to end ([`10`](screenshots/10-prometheus-traffic-generation-test.png)).
5. **Grafana** — deployed via Helm, configured with Prometheus as its datasource, and provisioned with an nginx-exporter dashboard to visualize live connection/request metrics. ([`11`](screenshots/11-grafana-datasource-confirmed.png))
6. **DoS simulation** — generated a light baseline load first ([`12`](screenshots/12-grafana-baseline-connections.png), [`13`](screenshots/13-grafana-light-load-test.png)) to confirm normal behavior, then ran `slowhttptest` against the nginx server. Active connections spiked to 100 concurrent connections in Grafana in real time, then dropped back to baseline once the attack stopped ([`14`](screenshots/14-grafana-dos-attack-spike-and-recovery.png)) — confirming the monitoring pipeline actually detects abnormal traffic patterns like a DoS attack, not just steady-state metrics.
7. **Security hardening recommendations** — HTTPS enforcement, request rate limiting, authentication/authorization on Prometheus & Grafana, firewall allow-listing, and container resource limits.

## Repository layout

```
k8s-manifests/                       nginx + Prometheus exporter deployment/service YAML
infrastructure-reproduction-guide/   step-by-step task write-ups (nginx, exporter, Prometheus, Grafana, DoS test, hardening)
screenshots/                         numbered, captioned evidence for every stage above
Proposed_Solution_Report.pdf         full solution write-up
```

## Author

Omar Mahmoud
