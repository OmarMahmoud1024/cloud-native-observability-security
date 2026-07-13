# Task 5 DoS testing

### NOTE: the ip addresses and the following instruction for port forwarding and connecting to the VM can differ from device to device, everything else can be recreated as in this guide

All commands in this file are run on:

`student@lab-kubernetes:~$`

## Install slowhttptest

`sudo apt update`

`sudo apt install slowhttptest -y`

## Generate light load before the attack

`for i in {1..10}; do curl http://172.18.0.2:30080 > /dev/null; done`

Take the before screenshot from Grafana.

## Run the DoS attack

`slowhttptest -c 100 -H -g -o slow -i 10 -r 200 -t GET -u http://172.18.0.2:30080 -x 24 -p 3`

Take the during screenshot from Grafana while the attack is running.

## Stop the attack

Press:

`Ctrl + C`

## Observe after the attack

Wait a little and take the after screenshot from Grafana.

## What should be observed

Before the attack the active connections should be low and stable.

During the attack the active connections and processed connections should spike because the server has many open connections.

After stopping the attack the active connections should go back down and the graphs should stabilize again.
