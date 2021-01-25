# elk-play
Automated ELK setup using Vagrant and Ansible

> :warning: This project is purely for PoC purposes!


## Info
The scripts set up 5+ nodes in total: 3+ ES, 1 Kibana, 1 Logstash. Communication between said nodes and services is all done over TLS.
Netdata agent is installed on each node for simple monitoring.

## Requirements
System (MacOS/Linux):
* Vagrant
* VirtualBox
* ansible
* cfssl

Ansible Galaxy:
* elastic.elasticsearch
* fedelemantuano.kibana
* mrlesmithjr.netdata

## Setup
1. Install system requirements
2. Install Galaxy modules
  ```bash
  ansible-galaxy install elastic.elasticsearch,v7.10.2
  ansible-galaxy install fedelemantuano.kibana
  ansible-galaxy install mrlesmithjr.netdata
  ```
### Configure
Edit `Vagrantfile` to set:
* **BASENET** - First three octets of the base network to create in VirtualBox (will make /24)
* **ESNODES** - How many ES nodes to create
* **NETDATA_CLAIM** - Full Netdata Cloud claim string (optional)

### Run
```bash
vagrant up
```
This will take a while. :)
And then, in the end:
```bash
vagrant destroy -f
```

### Use
* ES nodes: `BASENET`.11 (esnode-1), `BASENET`.12 (esnode-2), `BASENET`.13 (esnode-3), etc
* Kibana node: `BASENET`.20 (kibana)
* Logstash node: `BASENET`.30 (logstash)
* Everything except Logstash' beats (5044 -> 5042) input listen on default ports with TLS on
* User/pass for the whole stack is left defualt: elastic/changeme
* Kibana dashboard: https://`BASENET`.20:5601/
* Netdata dashboard: http://`NODEIP`:19999/


