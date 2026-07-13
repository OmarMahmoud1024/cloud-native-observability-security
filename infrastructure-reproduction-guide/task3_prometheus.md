# Task 3 Prometheus setup

### NOTE: the ip addresses and the following instruction for port forwarding and connecting to the VM can differ from device to device, everything else can be recreated as in this guide

All commands in this file are run on:

`student@lab-kubernetes:~$`

## Install helm

`curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash`

## Verify helm

`helm version`

## Create monitoring namespace

`kubectl create namespace monitoring`

## Verify namespace

`kubectl get namespaces`

## Add prometheus helm repository

`helm repo add prometheus-community https://prometheus-community.github.io/helm-charts`

## Update helm repositories

`helm repo update`

## Deploy prometheus in the monitoring namespace

`helm install prometheus prometheus-community/prometheus -n monitoring`

## Check prometheus pods

`kubectl get pods -n monitoring`

## Port-forward prometheus on lab-kubernetes

Keep this command running in one terminal on lab-kubernetes:

`kubectl port-forward -n monitoring svc/prometheus-server 9090:80`

## SSH tunnel from OpenStack VM to lab-kubernetes

Run on the OpenStack VM:

`ssh -N -L 9090:127.0.0.1:9090 student@192.168.100.51`

Keep it running.

## SSH tunnel from fep to OpenStack VM

Run on fep:

`ssh -N -L 9090:127.0.0.1:9090 -i ~/.ssh/id_openstack student@10.9.3.115`

Keep it running.

## SSH tunnel from Mac to fep

Run on the Mac:

`ssh -N -L 9090:127.0.0.1:9090 fep`

Keep it running.

## Access prometheus

Open in browser on Mac:

`http://localhost:9090`

## Configure prometheus to scrape the exporter

The exporter target is:

`promexporter.default.svc.cluster.local:9113`

Edit the prometheus configmap:

`kubectl edit configmap prometheus-server -n monitoring`

Under `scrape_configs`, add:

`- job_name: 'nginx-exporter'`

`  static_configs:`

`    - targets: ['promexporter.default.svc.cluster.local:9113']`

## Alternative command used to add the scrape job quickly

This command can be used instead of manual editing:

`kubectl get configmap prometheus-server -n monitoring -o yaml | sed "/scrape_configs:/a\\    - job_name: 'nginx-exporter'\\      static_configs:\\        - targets: ['promexporter.default.svc.cluster.local:9113']" | kubectl apply -f -`

## Restart prometheus after config change

Use one of these commands depending on the labels found in the cluster.

First try:

`kubectl delete pod -n monitoring -l app.kubernetes.io/name=prometheus,app.kubernetes.io/component=server`

If needed, this also worked in the homework:

`kubectl delete pod -n monitoring -l app=prometheus,component=server`

## Wait until prometheus is running again

`kubectl get pods -n monitoring`

## Verify the target

Open in browser:

`http://localhost:9090/targets`

The target `nginx-exporter` should be UP.

## Query nginx metrics

Open Graph in prometheus and query:

`nginx_connections_accepted`

## Generate traffic to see the metric change

Run on lab-kubernetes:

`for i in {1..20}; do curl http://172.18.0.2:30080; done`

The graph should increase after prometheus scrapes the new values.
