# Kubernetes 1.6.4

Ansible playbook to create a Kubernetes cluster latest release 1.6.4 using "kubeadm" on system(CentOS-7.x). 
- Ansible version is 2.3.0.0
- Git Hub Version 2.7.4

There are 5 roles defined in this ansible playbook.
Role Description:

"pre" 
base configuration for "all hosts":
  - Create and insert all entry on file "hosts" for all Server (If you don't have a DNS Server for hostname resolution) 
  - Disable "SELinux"
  - Disable "firewalld"
  - Install "chronyd" for NTP synch
  - Install "wget"
  - Install "bash-completion"

"master" 
initial Kubernetes setup for "kubernetes-master" 
  - 
  -
  
"edge" 
initial Kubernetes setup for "kubernetes-edge"
  -
  -
  
"confmaster" 
configuration for "kubernetes-master"
  -
  -
  
"confedge" 
configuration for "kubernetes-edge"
  -
  -
  

Following the below steps to create Kubernetes setup on Centos-7.
Prerequisite: 
- Kuberneter: 1 "Master" Server
- Kubernetes: 2 or more "Edge" Server
- Servers have access to the Internet;
- Servers are time-synchronized;
- On Kubernetes Server:
    - Create User "centos";
    - Configure User "centos" in /etc/sudoers. Execute as root:
      #echo "centos  ALL=(ALL)       NOPASSWD: ALL" >> /etc/sudoers

- On Ansbile machine:
  - Download the "Kubernetes" playbook from Git and: 
      - modify /kubernetes/ansible/hosts and replace /etc/ansible/hosts (Needed for Server hosts entry deploy);
      - copy /kubernetes/ansible/config on /root/.ssh/config (Disable StrictHostKeyChecking); 
  - Create ssh key and copy an all Kubernetes Server:
    - ssh-keygen
    - ssh-copy-id centos@MasterServerIP
    - ssh-copy-id centos@Edge1ServerIP
    - ssh-copy-id centos@Edge2ServerIP
- Full network connectivty between Servers and Ansible;
- Ansible can ssh into all Server and can sudo with no password prompt;

Run install.yml playbook to create Kubernetes Cluster.
