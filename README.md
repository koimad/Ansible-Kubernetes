# Ansible-Kubernetes

This will setup a kubernetes cluster on RedHat machines using ansible.
The machines required are given below. If you can allocate more compute resources, its better :-

    1. Ansible controller - 1 vcpu / 2 gib ram
    2. Kubernetes Master  - 2 vcpu / 2 gib ram
    3. Kubernetes Node1   - 2 vcpu / 2 gib ram
    4. Kubernetes Node2   - 2 vcpu / 2 gib ram

Command Line to run :-

ansible-playbook playbook.yaml -K &emsp; #  -K = --ask-become-pass: ask for privilege escalation password

If you change your machine IP's then you have to update the scripts as required.

## ToDo
    1. Update Scripts to be aware of if worker node is already amember of a cluster
    2. Update to use Calico for the firewall instaead of Firewalld.



