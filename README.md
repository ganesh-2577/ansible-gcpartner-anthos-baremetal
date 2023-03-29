# Anthos on Baremetal Ansible Playbook
This ansible playbook will fully automate the prerequisits and installation of Google Anthos on Baremetal. 

## Assumptions
### IPs
The smallest subnet that will work is a /28

The IP addresses for this deployment follow these rules:
* Control plane node IPs are are incremented starting with the second useable IP in the range
* Worker node IPs are always the fifth useable IP in the range
* The Control Plan VIP (K8s API) is alwasy the last useable IP in the range
* The Ingress Controller VIP is always the second to last useable IP in the range
* The Load Balancer IP Pool always starts with the 13th to last useable IP in the range
* The Load Balancer IP Pool always ends with the Ingress Controller's VIP
#### Example:
Assuming the subnet is 192.0.2.0/24
* CP Node 1: 192.0.2.2
* CP Node 2: 192.0.2.3
* CP Node 3: 192.0.2.4
* Worker Node 1: 192.0.2.5
* Worker Node 2: 192.0.2.6
* Worker Node 3: 192.0.2.7
* Worker Node X: 192.0.2.X + 4
* CP VIP: 192.0.2.254
* Ingress VIP: 192.0.2.253
* Load Balancer IP Pool: 192.0.2.242-192.0.2.253

### GCP Service Account Keys
We are assuming you have created the required Service Accounts with the correct RBAC roles and placed them in the following path:

`$HOME/bootstrap/gcp_keys/`

The required keys should also be named:
* bmctl.json
* gcr.json
* connect.json
* register.json
* cloud-ops.json

## Inventory file
The inventory file should look something like this:
```ini
[all:vars]
load_balancer_subnet=192.0.2.0/24
cluster_name=my-cluster
username=ubuntu
cp_node_count=3
worker_node_count=3
gcp_project_id=my-project
home_path=/home/ubuntu
[bootstrap_node]
127.0.0.1
[cp_nodes]
198.51.100.1
198.51.100.2
198.51.100.3
[worker_nodes]
198.51.100.4
198.51.100.5
198.51.100.6
```

## TODO:
* Automate the creation of GCP Service Accounts and Keys
* Allow more customization via Variables
  * Allow a variable to be used to determine the size of the Load Balancer IP Pool
* Possibly detect the existing IP and subnet size automatically from Ansible Facts
  * Also test if IPs are in use before setting these IPs
