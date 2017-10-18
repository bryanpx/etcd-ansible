I tested the etcd deployment on host machines with Centos 7.3 and 7.4.

# Prerequisites
* Ansible: I used ansible 2.3.2.0 on my control machine.

# Ansible configuration
Modify /etc/ansible/ansible.cfg to have the following:

host_key_checking = False

# To deploy etcd3 on AWS EC2 instances
The following are required  before you install ectd3:
1. Have your EC2 instances up and running.
2. Configure your Security Group for your EC2 instances to allow TCP inbound for ports 2379-2380 between EC2 instances.
3. The private key for ssh access to your EC2 instances.

## Configure inventory file
Specify hostname as in the first column. `Ip` variable is the public ip from EC2 instance where the hostname in the first column is resolved to. `Private_etc_ip` variable is the private ip of the EC2 isntance. Each line indicates information for one EC2 instance.
```
[root@etcd-ansible]# cat inv_ec2.yml
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
2. Configure your firewall to allow TCP inbound for ports 2379-2380 and 22. I disabled the firewall.

*** To do ****
