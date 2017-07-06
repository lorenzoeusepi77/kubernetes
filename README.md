# Create Kubernetes Cluster using kubeadm

Ansible playbook to create a Kubernetes cluster, latest release, using "kubeadm" on system with CentOS-7.x operating system.

There are different roles defined in this ansible playbook.

  Role: "base" -- Base configuration for "all hosts":
    
    - Create and insert all entry on file "hosts" for all Server (If you don't have a DNS Server) 
    
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
    



## Following the below steps to create Kubernetes cluster on Centos-7.

Prerequisites: 

1) Main:

    a) One server with git and ansible software:
        - Ansible version is 2.3.1.0
        - Git Hub Version 1.8.3.1
    
    b) Kuberneter: N°1 "Master" Server
    
    c) Kubernetes: N°1 or more "Edge" Servers
        - If you need persistent storage add secondary disk to Edge Servers
    
    d) Full network connectivty between Kubernetes Servers and Ansible
    
    e) Ansible can ssh into all Server and can sudo with no password prompt
    
    f) Servers have access to the Internet
    
    g) Servers are time-synchronized
     
2) On Kubernetes Server:
    
    a) Create User "centos";
    
    b) Configure User "centos" in /etc/sudoers. Execute as root:
      
        echo "centos  ALL=(ALL)       NOPASSWD: ALL" >> /etc/sudoers

3) On Ansbile Server:
    
    a) Install git:

       root@Ansible:~# yum install git-1.8.3.1-6.el7_2.1.x86_64
    
    b) Install ansible:

       root@Ansible:~# yum install epel-release
       root@Ansible:~# yum install ansible-2.3.1.0-1.el7.noarch

    c) Download the "Kubernetes" playbook from Git and replace value in inventory file:
        
       root@Ansible:~# cd /etc/ansible/
       root@Ansible:~# git init
       root@Ansible:~# git clone https://github.com/lorenzoeusepi77/kubernetes.git

    d) For "new Kubernetes cluster" edit all the necessary parameters in accordance with your        
       environment in hosts file: "kubernetes/inventories/production/hosts"
    
    e) For "add node to Kubernetes cluster" edit all the necessary parameters in accordance with your   
       environment in hosts file: "kubernetes/inventories/production/addedge"
    
    f) For "configure glusterfs for Kubernetes cluster" edit all the necessary parameters in accordance     with your environment in hosts file: "kubernetes/inventories/production/glusterfs"
        

    g) Create ssh key and copy an all Kubernetes Server (only if you use password authentication for  
       your servers):
      
       root@Ansible:~# ssh-keygen
       root@Ansible:~# ssh-copy-id centos@"MasterServerIP"
       root@Ansible:~# ssh-copy-id centos@"Edge1ServerIP"
       root@Ansible:~# ssh-copy-id centos@"Edge2ServerIP"
       root@Ansible:~# ssh-copy-id centos@"Edge3ServerIP"

    h) Disable StrictHostKeyChecking: 
       
       root@Ansible:~# cp /etc/ansible/kubernetes/cfg/ansible/file/config /root/.ssh/


### Create Kubernetes cluster with Kubeadm ### 
Run Ansible playbook with "kubernetes" as clustername and "centos" as user for your server. 

 You need to change vars according on file:

       [root@ansible ansible]# vi /etc/ansible/kubernetes/inventories/production/hosts


Execute playbook:

       root@Ansible:~# cd /etc/ansible/

       root@Ansible:~# ansible-playbook -i kubernetes/inventories/production/hosts kubernetes/site-create_cluster.yml -e clustername=kubernetes -u centos

       Note 1: Check note if you use ssh key


### Add "edge nodes" to Kubernetes cluster  ###
Run Ansible playbook with "kubernetes" as clustername and "centos" as user for your server. 

 You need to change vars according on file:

       [root@ansible ansible]# vi /etc/ansible/kubernetes/inventories/production/hosts


Execute playbook:

       root@Ansible:~# cd /etc/ansible/

       root@Ansible:~# ansible-playbook -i kubernetes/inventories/production/addedgehosts kubernetes/site-add_edgenode.yml -e clustername=kubernetes -u centos

       Note 1: Check note if you use ssh key

Note1: if you use ssh key


### Add "GlusterFS" to Kubernetes cluster for Persistent Storage Volume ###
Run Ansible playbook with "kubernetes" as clustername and "centos" as user for your server. 

 You need to change vars according on file:

       [root@ansible ansible]# vi /etc/ansible/kubernetes/inventories/production/glusterfshosts


Execute playbook:

       root@Ansible:~# cd /etc/ansible/

       root@Ansible:~# ansible-playbook -i kubernetes/inventories/production/glusterfshosts kubernetes/site-glusterfs.yml -e clustername=kubernetes -u centos

       Note 1: Check note if you use ssh key



Note 1: If you are using ssh key to connect to hosts add this parameter to previous ansible script 

       --private-key key.pub 

       The key must have this access permission 
    
       root@Ansible:~# chmod 400 key.pub   