OpenShift Prepare Environment
=========

Prepare hosts from inventory file to be used for new OpenShift deployment. Will install necessary packages and configure services according OpenShift requirements.

Requirements
------------

This role requires Ansible 2.0 or higher.

Role Variables
--------------

The variables that can be passed to this role and a brief description about them are as follows. (For all variables, take a look at defaults/main.yml):

```
ope_dns_servers: 192.168.1.3
ope_search_domain: openshift.int.example.com
ope_ntpserver: ntp.ubuntu.com
ope_timezone: Europe/Helsinki
```

Dependencies
------------

Inventory file must be provided by role ``jas02.create-openshift-inventory``.

Examples
----------------

Using this role is pretty straithforward. First, you must specify, which inventory file you want to use. Inventory file should look like this:

```
[lb]
loadbalancer1 ansible_host=10.2.45.1

[masters]
master1 ansible_host=10.2.45.2
master2 ansible_host=10.2.45.3
master3 ansible_host=10.2.45.4


[nodes]
node1 ansible_host=10.2.45.5
node2 ansible_host=10.2.45.6
node3 ansible_host=10.2.45.7

[bastions]
bastion ansible_host=10.2.45.9

[dns]
ns1 ansible_host=10.2.45.10

[glusterfs]
glusterfs1 ansible_host=10.2.45.11
glusterfs2 ansible_host=10.2.45.12
glusterfs3 ansible_host=10.2.45.13

[glusterfs_registry]
glusterfs_registry1
glusterfs_registry2
glusterfs_registry3

[infra]
glusterfs1
glusterfs2
glusterfs3

[openshift:children]
lb
masters
infra
nodes
glusterfs
glusterfs_registry

[openshift:vars]
ansible_connection=ssh
ansible_become=true
ansible_become_method=sudo
ansible_ssh_user=cloud-user
ansible_ssh_private_key_file=openstack/openstack.pem
openshift_version=3.7
openshift_ansible_version=openshift-ansible-3.7.15-1
docker_version=1.12.6
openstack_network=192.168.1.0/24

[all:vars]
ansible_connection=ssh
ansible_become=true
ansible_become_method=sudo
ansible_ssh_user=cloud-user
ansible_ssh_private_key_file=openstack/openstack.pem

[dns:vars]
ansible_connection=ssh
ansible_become=true
ansible_become_method=sudo
ansible_ssh_user=cloud-user
ansible_ssh_private_key_file=openstack/openstack.pem

[bastions:vars]
ansible_connection=ssh
ansible_become=true
ansible_become_method=sudo
ansible_ssh_user=cloud-user
ansible_ssh_private_key_file=openstack/openstack.pem
openshift_version=3.7
openshift_ansible_version=openshift-ansible-3.7.15-1
docker_version=1.12.6
openstack_network=192.168.1.0/24
```

With this inventory file, you can run playbook fith following content:

```
- hosts: openshift
  vars:
    ope_dns_servers: "{{ hostvars['ns1']['dns_server'] }}"
    ope_search_domain: openshift.int.example.com
    ope_ntpserver: ntp.ubuntu.com
  roles:
    - openshift-prepare-environment

```


License
-------

BSD

Author Information
------------------

Lumír Jasiok
