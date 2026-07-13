# Task 1 nginx setup

### NOTE: the ip addresses and the following instruction for port forwarding and connecting to the VM can differ from device to device, everything else can be recreated as in this guide

All commands in this file are run on:

`student@lab-kubernetes:~$`

## Create the kind kubernetes cluster

`kind create cluster`

## Verify the cluster

`kubectl cluster-info`

`kubectl get nodes`

## Create the nginx html ConfigMap file

The file used is:

`nginx-html.yaml`

Apply it:

`kubectl apply -f nginx-html.yaml`

## Create the nginx config ConfigMap file

The file used is:

`nginx-config.yaml`

This file contains the nginx configuration for:

- port 80 for html
- port 8080 for metrics
- location `/metrics` using `stub_status`

Apply it:

`kubectl apply -f nginx-config.yaml`

## Create the nginx deployment file

The file used is:

`nginx-deployment.yaml`

Apply it:

`kubectl apply -f nginx-deployment.yaml`

## Verify nginx deployment

`kubectl get pods`

`kubectl get deployments`

## Create the nginx service file

The file used is:

`nginx-service.yaml`

The service exposes:

- nginx html internally on port 80 and externally on nodePort 30080
- nginx metrics internally on port 8080 and externally on nodePort 30088

Apply it:

`kubectl apply -f nginx-service.yaml`

## Check the service

`kubectl get services`

## Get the kind node IP

`kubectl describe nodes kind-control-plane | grep InternalIP`

The IP used in my setup was:

`172.18.0.2`

## Test the html endpoint

`curl http://172.18.0.2:30080`

Expected result:

`<html><body>Task 1 homework</body></html>`

## Test the metrics endpoint

`curl http://172.18.0.2:30088/metrics`

Expected result should include something like:

`Active connections: 1`
