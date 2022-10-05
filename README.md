# Ansible-Kubernetes

This will setup a kubernetes cluster on RedHat machines using ansible.
The machines required are given below. If you can allocate more compute resources, its better :-

    1. Ansible controller - 1 vcpu / 2 gib ram
    2. Kubernetes Master  - 2 vcpu / 2 gib ram
    3. Kubernetes Node1   - 2 vcpu / 2 gib ram
    4. Kubernetes Node2   - 2 vcpu / 2 gib ram

# Setup Hosts DNS Resolution

If you are not using a DNS server it is easier to setup the /etc/ hosts file and copy to all machines with the below command

    scp /etc/hosts root@<ip of machine>:/etc/

# Setup SSH 

Create ssh keys required for ansible to use certificates for connections over ssh

    ssh-keygen     (accept default options)

    cd .ssh

    ls -l          (view to ensure keys are generated)

    ssh-copy-id  <user>@<hostname>     (repeat for all nodes)

# Install Ansible on the Ansible host

    yum install epel-release

    yum install ansible

Confirm the ansible installation version with

    ansible --version

# Running the Playbook

Command Line to run :-

    ansible-playbook playbook.yaml -K 

  -K reperesents --ask-become-pass: ask for privilege escalation password


If you change your machine IP's then you have to update the scripts as required.

## ToDo
    1. Update Scripts to be aware if worker node is already a member of a cluster
    2. Install Metallb as load balancer
    3. Insall a Ingress Controller


