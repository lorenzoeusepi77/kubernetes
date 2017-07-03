## Create Kubernetes Cluster using kubeadm ##

Ansible playbook to create a Kubernetes cluster latest release 1.6.6 using "kubeadm" on system with CentOS-7.x operating system.

There are different roles defined in this ansible playbook.


Role: "base" -- Base configuration for "all hosts":
  
  - Create and insert all entry on file "hosts" for all Server (If you don't have a DNS Server for hostname resolution) 
  
  - Disable "SELinux"
  
  - Disable "firewalld"
  
  - Install "chronyd" for NTP synch
  
  - Install "wget"
  
  - Install "bash-completion"


Role: "master" -- Initial Kubernetes setup for "kubernetes-master" 
    
  - Install Start and Enable "Docker"
  
  - Install "Kubectl"
  
  - Install Start and Enable "Kubelet"
  
  - Install "Kubeadm"
  

Role: "edge" -- Initial Kubernetes setup for "kubernetes-edge"
  
  - Install Start and Enable "Docker"
  
  - Install Start and Enable "Kubelet"
  
  - Install "Kubeadm" 
  
  
Role: "create_token" -- Create token needed for cluster initialization
  
  - Generate toked that will be used for:
  
  - Master cluster inizialization
  
  - Node cluster join
    

Role: "configmaster" -- Configuration for "kubernetes-master"
  
  - Initialize master with kubeadm 
  
  - Export env var for Kubernetes
  
  - Install pod network "weave-kube 1.6"


Role: "configedge" -- Configuration for "kubernetes-edge"
  
  - Join Edge Nodes to cluster with kubeadm


Role: "dashboard" -- Add-on to kubernetes cluster
  
  - Dashboard
  

Role: "Glusterfs" -- Add-on to kubernetes cluster
  
  - Persistent Storage using GlusterFS
  

Role: "heapster" -- Add-on to kubernetes cluster
  
  - Heapster with an InfluxDB backend and a Grafana UI


# Following the below steps to create Kubernetes cluster on Centos-7.

Prerequisites: 

1) Main:
    - One server with git and ansible software:
        - Ansible version is 2.3.1.0
        - Git Hub Version 1.8.3.1
    - Kuberneter: N°1 "Master" Server
    - Kubernetes: N°1 or more "Edge" Servers
    - Full network connectivty between Kubernetes Servers and Ansible;
    - Ansible can ssh into all Server and can sudo with no password prompt;
    - Servers have access to the Internet;
    - Servers are time-synchronized;
     
2) On Kubernetes Server:
    - Create User "centos";
    - Configure User "centos" in /etc/sudoers. Execute as root:
      
      #echo "centos  ALL=(ALL)       NOPASSWD: ALL" >> /etc/sudoers

3) On Ansbile machine:
    - Install git
        - yum install git-1.8.3.1-6.el7_2.1.x86_64
    - Install ansible
        - yum install epel-release
        - yum install ansible-2.3.1.0-1.el7.noarch

    - Download the "Kubernetes" playbook from Git and replace value in inventory file /etc/ansible/hosts;
        - root@Ansible:~# cd /etc/ansible/
        - root@Ansible:~# git init
        - root@Ansible:~# git clone https://github.com/lorenzoeusepi77/kubernetes.git

    Edit all the necessary parameters in accordance with your environment in hosts file:
    "kubernetes/inventories/production/hosts"

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
              dns_domain=test.local
              #kubectl_version=v1.6.4

    - Copy /kubernetes/ansible/config on /root/.ssh/config (Disable StrictHostKeyChecking); 
          - cp /etc/ansible/kubernetes/cfg/Ansible-Git/file/config /root/.ssh/

    - Create ssh key and copy an all Kubernetes Server:
      - ssh-keygen
      - ssh-copy-id centos@"MasterServerIP"
      - ssh-copy-id centos@"Edge1ServerIP"
      - ssh-copy-id centos@"Edge2ServerIP"

  
### Create Kubernetes cluster	with Kubeadm ###  
How to Run Ansible playbook with kubernetes as clustername and centos as user for your server: 

 You need to change this vars according on previous step:
  - clustername = your cluster name
  - username = user for remote systems access -u username 
  - hostname, ip address and var 

root@Ansible:~# cd /etc/ansible/

root@Ansible:~# ansible-playbook -i kubernetes/inventories/production/hosts kubernetes/site-create_cluster.yml -e clustername=kubernetes -u centos

If you are using ssh key to connect to hosts add this parameter to previous script 

    --private-key key.pem     (specify key path for key that you copy on the ansible server)

    Note: The key must have chmod 400 permission   


### Add to Kubernetes cluster "edge nodes" ###
  root@Ansible:~# ansible-playbook -i kubernetes/inventories/production/addedge kubernetes/site-add_edgenode.yml -e clustername=kubernetes -u centos
 
