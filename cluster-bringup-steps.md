## Steps to bring opencontrail+openshift on AWS instance

### Pre-requisites:
- AWS Account

#### Steps:
- Create VPC, Subnet, RouteTable, InternetGW on AWS
- Bring up 3 EC2 instances running Centos 7.3 OS, 1 master, 2 nodes either manually or using cloudformation scripts in the above created VPC and Subnet
- Perform below steps on all 3 instances
  1. Edit hostname, /etc/hostname   (Ex: ec2-54-187-98-111)
  2. Edit /etc/hosts file, add entries for all the 3 instances  (Ex: \<private_ip\> ec2-54-187-98-111.us-west-2.compute.amazonaws.com ec2-54-187-98-111)
  3. yum upgrade -y
  4. yum install git epel-release -y
- Perfrom below steps on node instances
  1. yum install kernel-devel kernel-headers nfs-utils socat wget net-tools bind-utils -y && reboot
- Perfrom below steps on master instance
  1. yum install ansible pyOpenSSL python-cryptography python-lxml wget net-tools bind-utils -y
  2. yum install kernel-devel kernel-headers -y && reboot
  3. Enable passwordless access to node instances from master instance
     1. ssh-keygen -f $HOME/.ssh/id_rsa -t rsa -N ''
     2. ssh-copy-id root@<private_ip_node1_instance>
     3. ssh-copy-id root@<private_ip_node2_instance>
     4. ssh-copy-id root@<private_ip_master_instance>
  4. Clone openshift-ansible project, git clone https://github.com/openshift/openshift-ansible
  5. 

  
