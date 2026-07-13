# Task 2 prometheus exporter setup

### NOTE: the ip addresses and the following instruction for port forwarding and connecting to the VM can differ from device to device, everything else can be recreated as in this guide

All commands in this file are run on:

`student@lab-kubernetes:~$`

## Create the prometheus exporter deployment file

The file used is:

`promexporter-deployment.yaml`

The important argument inside the deployment is:

`--nginx.scrape-uri=http://nginx:8080/metrics`

This makes the exporter read the nginx metrics from the nginx kubernetes service.

Apply it:

`kubectl apply -f promexporter-deployment.yaml`

## Verify the exporter deployment

`kubectl get pods`

`kubectl get deployments`

## Create the prometheus exporter service file

The file used is:

`promexporter-service.yaml`

The service name is:

`promexporter`

The internal port is:

`9113`

Apply it:

`kubectl apply -f promexporter-service.yaml`

## Check services

`kubectl get services`

## Test the exporter from inside the cluster

`kubectl run curl-test --rm -it --image=curlimages/curl --restart=Never -- http://promexporter:9113/metrics`

Expected result should include nginx prometheus metrics such as:

`nginx_connections_active`

or:

`nginx_connections_accepted`
