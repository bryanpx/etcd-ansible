I tested the etcd deployment on host machines with Centos 7.3 and 7.4. I provided inv_ec2.yml, inv_vmpriviateip.yml and inv_vmpublicip.yml as sample inventory files for AWS EC2 and VMs for different scenarios that you need to modify for your environment.

# Prerequisites
* Ansible: I used ansible 2.3.2.0 on my control machine.

# Ansible configuration
Modify /etc/ansible/ansible.cfg to have the following:

`host_key_checking = False`

# Inventory file format
```
<hostname> ip=<public_ip> private_etcd_ip=<private_ip>
<hostname> ip=<public_ip> private_etcd_ip=<private_ip>
...
```
`Hostname` is hostname of a machine and is required.

`Ip` is the public ip of the machine and is required. 

`Private_etcd_ip` is usually a private ip or ip of another subnet and is optional. It indicates which network interface is used for etcd installation. If you don't have it, the etcd installation will use the value of `ip`.

Each line is used to specify information for each machine.  

# To deploy etcd3 on AWS EC2 instances
The following are required  before you install ectd3:
1. Have your EC2 instances up and running.
2. Configure your Security Group for your EC2 instances to allow TCP inbound for ports 2379 and 2380 for external communication and between EC2 instances communcation.
3. The private key for ssh access to your EC2 instances.

## Configure inventory file
Use public hostnames for your EC2 instances. Use public ip for `ip` and private ip `private_etcd_ip`. 

The example below tells installation script to use 172.xx.xx.xx for the ectd installation.

```
# cat inv_ec2.yml
[nodes]
ec2-34-208-190-182.us-west-2.compute.amazonaws.com ip=34.208.190.182 private_etcd_ip=172.31.26.171
ec2-34-214-191-3.us-west-2.compute.amazonaws.com ip=34.214.191.3 private_etcd_ip=172.31.29.88
ec2-34-215-192-46.us-west-2.compute.amazonaws.com ip=34.215.192.46 private_etcd_ip=172.31.17.135
```
## Configure remote_user in etcd3.yml 
Have `remote_user: ec2-user`
## Deploy etcd3
`/amazon/yourkeypair.pem` is my private key.

Execute:

`ansible-playbook -i inv_ec2.yml etcd3.yml --private-key /amazon/yourkeypair.pem`

# To deploy etcd3 on VMs or Bare metal
The following are required  before you install ectd3:
1. Your VMs or machines are up.
2. Configure your firewall to allow TCP inbound for ports 2379 and 2380 for external communication and between machines communication. I disabled the firewall.

## Configure inventory file with 1 network interface
The example below tells installation script to install etcd on 70.0.51.x. `ansible_ssh_user` and `ansible_ssh_pass` are the ssh user name and password to all these VMs.
```
# cat inv_vmpublicip.yml
[nodes]
vm13 ip=7.0.51.13 
vm14 ip=7.0.51.14 
vm15 ip=7.0.51.15 

[all:vars]
ansible_ssh_user=root
ansible_ssh_pass=Password
```
## Configure inventory file with multiple network interfaces
The example below tells installation script to install etcd on 7.0.51.x. `ansible_ssh_user` and `ansible_ssh_pass` are the ssh user name and password to all these VMs.

```
# cat inv_vmpriviateip.yml
[nodes]
vm13 ip=7.0.51.13 private_etcd_ip=192.168.9.13
vm14 ip=7.0.51.14 private_etcd_ip=192.168.9.14
vm15 ip=7.0.51.15 private_etcd_ip=192.168.9.15

[all:vars]
ansible_ssh_user=root
ansible_ssh_pass=Password
```
