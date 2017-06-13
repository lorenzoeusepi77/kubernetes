# kubernetes
Ansible playbook to create a kubernetes cluster latest release 1.6.4 using "kubeadm" on system(CentOS-7.x). 
Ansible version is 2.3.0.0
Git Hub Version 2.7.4

There are 8 roles define in this ansible playbook.
Ansible Role Description:
"pre" with base configuration for all Server:
- Create and insert all entry on file "hosts" for all Server (If you don't have a DNS Server for hostname resolution) 
- Disable "SELinux"
- Disable "firewalld"
- Install "chronyd" for NTP synch
- Install "wget"
- Install "bash-completion"

"master":

"edge":

"confmaster":

"confedge":


Following the below steps to create Kubernetes setup on Centos-7.
Prerequisite: 
- Ansbile 
- Git Hub
- Download the "Kubernetes" playbook and: 
    - modify /kubernetes/ansible/hosts and replace /etc/ansible/hosts;
    - copy /kubernetes/ansible/config on /root/.ssh/config
- On Kubernetes Server:
    - Create User "centos"
    - Configure User "centos" in /etc/sudoers
Run install.yml playbook to create Kubernetes Cluster.

For this installation:
- 1 "Master" Server
- 2 or more "Edge" Server
