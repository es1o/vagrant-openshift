# vagrant-openshift

This is vagrant configuration for running VirtualBox vms with 1 master and 2 nodes.

## Usage
1. Install vagrant: `brew cask install vagrant`
2. Install ansible: `brew install ansible`
3. Run vagrant: `vagrant up`
4. Configure each vagrant machine /etc/hosts, add these lines:
```
10.17.4.202 node2
10.17.4.201 node1
10.17.4.2 master1
```
5. Download repository with openshift-ansible installer: `git clone git@github.com:openshift/openshift-ansible.git`
6. Select openshift version for changing branch of openshift-ansible repository, e.g: `git checkout release-3.7`
7. Edit inventory/hosts file in openshift-ansible, example: 
```
[OSEv3:children]
masters
nodes
etcd

[OSEv3:vars]
# if your target hosts are Fedora uncomment this
#ansible_python_interpreter=/usr/bin/python3
openshift_deployment_type=origin
openshift_release=3.7
osm_cluster_network_cidr=10.128.0.0/14
openshift_portal_net=172.30.0.0/16
osm_host_subnet_length=9
# localhost likely doesn't meet the minimum requirements
openshift_disable_check=disk_availability,memory_availability

[masters]
master1 ansible_ssh_host=10.17.4.2 openshift_ip="10.17.4.2" openshift_hostname="master1"

[etcd]
master1 ansible_ssh_host=10.17.4.2 openshift_ip="10.17.4.2" openshift_hostname="master1"

[nodes]
node1 ansible_ssh_host=10.17.4.201 openshift_ip="10.17.4.201" openshift_node_labels="{'region': 'infra', 'zone': 'default'}"
node2 ansible_ssh_host=10.17.4.202 openshift_ip="10.17.4.202" openshift_node_labels="{'region': 'infra', 'zone': 'default'}"
```
8. Install prerequisites: `ansible-playbook -i inventory/hosts playbooks/prerequisites.yml`
9. Install openshift origin cluster: `ansible-playbook -i inventory/hosts playbooks/deploy_cluster.yml`

## TODO:
* create hosts file automatically
* create /etc/hosts file automatically

## Bugs:
* Add more memory to vagrant VM because standard openshift origin installation require more than 512MB.