# Task 4 Grafana setup

### NOTE: the ip addresses and the following instruction for port forwarding and connecting to the VM can differ from device to device, everything else can be recreated as in this guide

All commands in this file are run on:

`student@lab-kubernetes:~$`

## Add grafana helm repo

`helm repo add grafana https://grafana.github.io/helm-charts`

## Update helm repositories

`helm repo update`

## Deploy grafana in monitoring namespace

`helm install grafana grafana/grafana -n monitoring`

## Check grafana pod

`kubectl get pods -n monitoring`

## Get grafana admin password

`kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo`

The username is:

`admin`

## Port-forward grafana on lab-kubernetes

Keep this command running on lab-kubernetes:

`kubectl port-forward -n monitoring svc/grafana 3000:80`

## SSH tunnel from OpenStack VM to lab-kubernetes

Run on the OpenStack VM:

`ssh -N -L 3000:127.0.0.1:3000 student@192.168.100.51`

Keep it running.

## SSH tunnel from fep to OpenStack VM

Run on fep:

`ssh -N -L 3000:127.0.0.1:3000 -i ~/.ssh/id_openstack student@10.9.3.115`

Keep it running.

## SSH tunnel from Mac to fep

Run on the Mac:

`ssh -N -L 3000:127.0.0.1:3000 fep`

Keep it running.

## Access grafana

Open in browser on Mac:

`http://localhost:3000`

Login with:

username:

`admin`

password:

`the password from the secret command`

## Add prometheus datasource

In Grafana:

Connections
→ Data sources
→ Add data source
→ Prometheus

Use this URL:

`http://prometheus-server`

Click Save and Test.

## Import nginx exporter dashboard

In Grafana:

Dashboards
→ Import

Use the nginx prometheus exporter dashboard id.

Select the prometheus datasource and import.

## Generate traffic to see dashboard updates

Run on lab-kubernetes:

`for i in {1..50}; do curl http://172.18.0.2:30080; done`

The grafana dashboard should show updated graphs.
