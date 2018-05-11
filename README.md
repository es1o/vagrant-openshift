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
7. Edit inventory/hosts file in openshift-ansible, example checked in repository:
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
openshift_disable_check=disk_availability,memory_availability,docker_storage

[masters]
master1 ansible_ssh_host=10.17.4.2 openshift_ip="10.17.4.2" openshift_hostname="master1"

[etcd]
master1 ansible_ssh_host=10.17.4.2 openshift_ip="10.17.4.2" openshift_hostname="master1"

[nodes]
master1 ansible_ssh_host=10.17.4.2 openshift_ip="10.17.4.2" openshift_hostname="master1"
node1 ansible_ssh_host=10.17.4.201 openshift_ip="10.17.4.201" openshift_node_labels="{'region': 'infra', 'zone': 'default'}"
node2 ansible_ssh_host=10.17.4.202 openshift_ip="10.17.4.202" openshift_node_labels="{'region': 'infra', 'zone': 'default'}"
```
8. Install cluster: `ansible-playbook -i inventory/hosts playbooks/byo/config.yml`
9. Install oc tool from: https://www.openshift.org/download.html
10. Modify your systems /etc/hosts to add ip to master1
11. Login to master1: `oc login https://master1:8443 -u admin`, type any characters as password.

## Ansible playbooks
### Create nfs for persistent storage
You can run setup-nfs-server.yml playbook to create nfs share for persistent storage.
This playbook creates nfs shares from nfs_dirs variable.

## TODO:
* create hosts file automatically
