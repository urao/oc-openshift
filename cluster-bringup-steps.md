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
- Perform below steps on node instances
  1. yum install kernel-devel kernel-headers nfs-utils socat wget net-tools bind-utils -y && reboot
- Perform below steps on master instance
  1. yum install ansible pyOpenSSL python-cryptography python-lxml wget net-tools bind-utils -y
  2. yum install kernel-devel kernel-headers -y && reboot
  3. Enable passwordless access to node instances from master instance
     1. ssh-keygen -f $HOME/.ssh/id_rsa -t rsa -N ''
     2. ssh-copy-id root@<private_ip_node1_instance>
     3. ssh-copy-id root@<private_ip_node2_instance>
     4. ssh-copy-id root@<private_ip_master_instance>
  4. Clone openshift-ansible project, git clone https://github.com/openshift/openshift-ansible
  5. Copy file, ose-install (https://github.com/urao/oc-openshift/blob/master/files/ose-install) to $HOME/openshift-ansible/inventory/byo/, and modify IP address, hostname with that of your instances
  6. Copy file, ose-prerequisities.yml (https://github.com/urao/oc-openshift/blob/master/files/ose-prerequisities.yml) to $HOME/openshift-ansible/inventory/byo/
  7. cd $HOME/openshift-ansible/
  7. ansible-playbook -i inventory/byo/ose-install inventory/byo/ose-prerequisites.yml 
  8. ansible-playbook -i inventory/byo/ose-install playbooks/byo/openshift_facts.yml
  9. Comment out, docker-storage in $HOME/openshift-ansible/common/openshift-cluster/config.yml
  10.Comment out, task with name, Ensure OpenShift router correctly rolls out (best-effort today) in $HOME/openshift-ansible/roles/openshift_hosted/tasks/router/router.yml
  11.Comment out, task with name, Ensure OpenShift registry correctly rolls out (best-effort today) in $HOME/openshift-ansible/roles/openshift_hosted/tasks/registry/registry.yml
  12. ansible-playbook -i inventory/byo/ose-install playbooks/byo/config.yml 
  13. Copy contrail_ansible_package(contrail-ansible-4.0.0.0-20.tar.gz) and contrail_docker_package (contrail-kubernetes-docker-images_4.0.0.0-20.tgz) onto $HOME dir
      1. Download contrail-kubernetes-docker_4.0.0.0-20_xenial.tgz, from juniper download site (http://www.juniper.net/support/downloads/?p=contrail#sw)
      2. Untar the above package to get contrail_docker_package, tar -zxvf contrail-kubernetes-docker_4.0.0.0-20_xenial.tgz
      3. Untar contrail-networking-tools_4.0.0.0-20.tgz, to get contrail_ansible_package
  14. Untar contrail_ansible_package, tar -zxvf contrail-ansible-4.0.0.0-20.tar.gz -C $HOME/contrail_ansible/
  15. Untar contrail_docker_package, tar -zxvf contrail-kubernetes-docker-images_4.0.0.0-20.tgz -C $HOME/contrail_ansible/playbooks/container_images/
  16. Modify IP address of hosts file @ $HOME/contrail_ansible/playbooks/inventory/my-inventory/
  17. Override file, all.yml with https://github.com/urao/oc-openshift/blob/master/files/all.yml under $HOME/contrail_ansible/playbooks/inventory/my-inventory/group_vars/
  18. cd $HOME/contrail_ansible/playbooks
  19. Run, ansible-playbook -i inventory/my-inventory site.yml
  20. 

  
