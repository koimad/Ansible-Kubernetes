# ansible-k8s-setup

This will setup a kubernetes cluster on RedHat machines using ansible.
You need these machines:

1. Ansible controller - 1 vcpu / 2 gib ram
2. Kubernetes Master - 2 vcpu / 2 gib ram
3. Kubernetes Node1 - 2 vcpu / 2 gib ram
4. Kubernetes Node2 - 2 vcpu / 2 gib ram

Command Line to run

ansible-playbook playbook.yaml -K  #  -K, --ask-become-pass: ask for privilege escalation password




If you can allocate more compute resources, its better
If you change your machine IP's then you have to change those whereever
those were referred.

