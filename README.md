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
    

  Role: "heapster" -- Add-on to kubernetes cluster
    
    - Heapster with an InfluxDB backend and a Grafana UI



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

3) On Ansbile machine:
    a) Install git
        - yum install git-1.8.3.1-6.el7_2.1.x86_64
    b) Install ansible
        - yum install epel-release
        - yum install ansible-2.3.1.0-1.el7.noarch

    c) Download the "Kubernetes" playbook from Git and replace value in inventory file     
       /etc/ansible/hosts;
        
       root@Ansible:~# cd /etc/ansible/
       root@Ansible:~# git init
       root@Ansible:~# git clone https://github.com/lorenzoeusepi77/kubernetes.git

    d) For "new Kubernetes cluster" edit all the necessary parameters in accordance with your        
       environment in hosts file: "kubernetes/inventories/production/hosts"
    
    e) For "new Kubernetes cluster" edit all the necessary parameters in accordance with your        
       environment in hosts file: "kubernetes/inventories/production/addedge"
    
    f) For "new Kubernetes cluster" edit all the necessary parameters in accordance with your        
       environment in hosts file: "kubernetes/inventories/production/glusterfs"
        
    g) Copy /kubernetes/ansible/config on /root/.ssh/config (Disable StrictHostKeyChecking); 
       
       root@Ansible:~#cp /etc/ansible/kubernetes/cfg/Ansible-Git/file/config /root/.ssh/


    h) Create ssh key and copy an all Kubernetes Server:
      
       root@Ansible:~#ssh-keygen
       root@Ansible:~#ssh-copy-id centos@"MasterServerIP"
       root@Ansible:~#ssh-copy-id centos@"Edge1ServerIP"
       root@Ansible:~#ssh-copy-id centos@"Edge2ServerIP"
       root@Ansible:~#ssh-copy-id centos@"Edge3ServerIP"

#### Create Kubernetes cluster with Kubeadm ####  
Run Ansible playbook with "kubernetes" as clustername and "centos" as user for your server. 

 You need to change this vars according on previous step:
    - clustername = your cluster name
    - username = user for remote systems access -u username 
    - hostname, ip address and var 

       root@Ansible:~# cd /etc/ansible/

       root@Ansible:~# ansible-playbook -i kubernetes/inventories/production/hosts kubernetes/site-create_cluster.yml -e clustername=kubernetes -u centos


### Add to Kubernetes cluster "edge nodes" ###
How to add edge node to existing cluster.

       root@Ansible:~# cd /etc/ansible/

       root@Ansible:~# ansible-playbook -i kubernetes/inventories/production/addedge kubernetes/site-add_edgenode.yml -e clustername=kubernetes -u centos


### Add to Kubernetes cluster "GlusterFS" for Persistent Volume ###
How to add Gluster File System to kubernetes cluster.

       root@Ansible:~# cd /etc/ansible/

       root@Ansible:~# ansible-playbook -i kubernetes/inventories/production/glusterfs kubernetes/site-glusterfs.yml -e clustername=kubernetes -u centos


NOTE: If you are using ssh key to connect to hosts add this parameter to previous script 

    --private-key key.pub 

    Note: The key must have chmod 400 permission   