# Create Kubernetes Cluster using kubeadm #

Ansible playbook to create a Kubernetes cluster latest release 1.6.4 using "kubeadm" on system(CentOS-7.x). 
- Ansible version is 2.3.0.0
- Git Hub Version 2.7.4

There are 5 roles defined in this ansible playbook.

Role Description:

Role: "pre" 
base configuration for "all hosts":
  - Create and insert all entry on file "hosts" for all Server (If you don't have a DNS Server for hostname resolution) 
  - Disable "SELinux"
  - Disable "firewalld"
  - Install "chronyd" for NTP synch
  - Install "wget"
  - Install "bash-completion"

Role: "master" 
initial Kubernetes setup for "kubernetes-master" 
  - Install Start and Enable "Docker"
  - Install "Kubectl"
  - Install Start and Enable "Kubelet"
  - Install "Kubeadm"
  
Role: "edge" 
initial Kubernetes setup for "kubernetes-edge"
  - Install Start and Enable "Docker"
  - Install Start and Enable "Kubelet"
  - Install "Kubeadm" 
  
Role: "admission_token"
create token needed for cluster initialization
  - Generate toked that will be used for:
    - Master cluster inizialization
    - Node cluster join
  
Role: "confmaster" 
configuration for "kubernetes-master"
  - Initialize master with kubeadm 
  - Export env var for Kubernetes
  - Install pod network "weave-kube 1.6"
  
Role: "confedge" 
configuration for "kubernetes-edge"
  - Join Edge Nodes to cluster with kubeadm
  
 
Following the below steps to create Kubernetes cluster on Centos-7.

Prerequisite: 
  - Full network connectivty between Servers and Ansible;
  - Ansible can ssh into all Server and can sudo with no password prompt;
  - Servers have access to the Internet;
  - Servers are time-synchronized;

  - Kuberneter: N°1 "Master" Server
  - Kubernetes: N°1 or more "Edge" Server
  
  - On Kubernetes Server:
    - Create User "centos";
    - Configure User "centos" in /etc/sudoers. Execute as root:
      #echo "centos  ALL=(ALL)       NOPASSWD: ALL" >> /etc/sudoers

  - On Ansbile machine:
    - Download the "Kubernetes" playbook from Git and: 
    
    - Copy /kubernetes/ansible/hosts and replace /etc/ansible/hosts;
        - Change [clustername] var with your cluster name;
        - Insert your master Server hostname, ip address in [clustername_master]
          - Example:
            Master ansible_ssh_host=192.168.234.143
        - Insert your edge node Servers hostname, ip address in [clustername_edge]
          - Example:
            Edge1 ansible_ssh_host=192.168.234.144
            Edge2 ansible_ssh_host=192.168.234.145
        - Insert [clustername_master:vars] master ip address
          - Example:
            master_ip_address=192.168.234.143
        - Insert var required in [all:vars]
        
    - Copy /kubernetes/ansible/config on /root/.ssh/config (Disable StrictHostKeyChecking); 
  
    - Create ssh key and copy an all Kubernetes Server:
      - ssh-keygen
      - ssh-copy-id centos@"MasterServerIP"
      - ssh-copy-id centos@"Edge1ServerIP"
      - ssh-copy-id centos@"Edge2ServerIP"


# Create Kubernetes cluster	  
How to Run Ansible playbook with kubernetes as clustername and centos as user for your server 
On Ansible server clone git repo:

root@Ansible:~# cd /etc/ansible/
root@Ansible:/etc/ansible# git init
git clone https://github.com/lorenzoeusepi77/kubernetes.git
ansible-playbook -i kubernetes/inventories/production/hosts site.yml -e clustername=kubernetes -u centos

You can change vars:
  - clustername = your cluster name
  - username = user for remote systems access -u username 

  
# Add Kubernetes cluster edge node
ansible-playbook -i kubernetes/inventories/production/addedge site-addedge.yml -e clustername=kubernetes -u centos
  
# Delete Kubernetes cluster edge node
ansible-playbook -i kubernetes/inventories/production/deledge site-deledge.yml -e clustername=kubernetes -u centos
