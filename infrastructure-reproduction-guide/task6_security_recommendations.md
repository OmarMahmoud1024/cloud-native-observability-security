# Task 6 Security recommendations

### NOTE: the ip addresses and the following instruction for port forwarding and connecting to the VM can differ from device to device, everything else can be recreated as in this guide

Recommendation regarding securing the infrastructure using in this homework:

-Using HTTPS instead of HTTP: traffic between users and the server should be encryped and secured to prevent interception of traffic by attackers.

-Rate limiting: Nginx should be configured to limit the requests per client to prevent DoS attacks like the one tested in the previous task.

-Authentication and Authorization: adding password to both prometheus and grafana, and implementing access control in kubernetes.

-Firewall rules: only allow trusted IP addresses to access the system

-Using resource limits: CPU and memory usage shoul be limited for the containers so that a single service doesnt exhause the resources
