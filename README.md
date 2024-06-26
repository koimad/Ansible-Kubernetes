# Install Ansible on the required host
To install Ansible using the local nexus repo follow the steps below
   1. Update the host machines CA's to include the certificate of the nexus repo
   2. Create the file /etc/pki/ca-trust/source/anchors/localca.crt with the contents of the root ca's public certificate
   3. Run the following command
        ```
            update-ca-trust
        ```
   4. Create the file /etc/pip.conf with the following settings
        ```
            [global]
            index=https://dev-dc01.dev.local:8443/repository/python-group/pypi
            index-url=https://dev-dc01.dev.local:8443/repository/python-group/simple
            trusted-host=dev-dc01.dev.local
        ```
   5. Update pip
        ```
            python3 -m pip install --upgrade pip
        ```
   6. Install  setuptools-rust
        ```
            python3 -m pip install setuptools-rust
        ```            
   7. Install wheel
        ```            
            python3 -m pip install wheel
        ```            
   8. Install Ansible
        ```
             python3 -m pip install ansible
        ```        

# Ansible-Kubernetes
This will setup a kubernetes cluster on RedHat 8.x machines using ansible. The playbook can be used in an offline environment if a suitable container repository is setup with the required container images. I have used a Nexus OSS repository for docker inmages configured as below. 
```
    docker-group  (exposed port 8444 for direct connection - (Kubernetes does not support paths in the url to the repo)
        - docker-proxy-registry-1.docker.io
        - docker-proxy-quay.io
        - docker-proxy-registry-k8s-io
```    
With using a local repository, I've used a custom config file for initialising the kubernetes cluster so that the repositories can be updated to use my local repo. 

## Platform requirements
The machines required are given below. If you can allocate more compute resources, its better :-

    1. Ansible controller - 1 vcpu / 2 gib ram
    2. Kubernetes Master  - 2 vcpu / 4 gib ram
    3. Kubernetes Node1   - 2 vcpu / 4 gib ram
    4. Kubernetes Node2   - 2 vcpu / 4 gib ram
## Setup Hosts DNS Resolution

If you are not using a DNS server it is easier to setup the /etc/ hosts file and copy to all machines with the below command

    scp /etc/hosts root@<ip of machine>:/etc/

## Setup SSH 
Create ssh keys required for ansible to use certificates for connections over ssh
```
    ssh-keygen     (accept default options)

    cd .ssh

    ls -l          (view to ensure keys are generated)

    ssh-copy-id  *<user>@<hostname>*     (repeat for all nodes)
```
## Install Ansible on the Ansible host

```
    yum install epel-release

    yum install ansible
```

Confirm the ansible installation version with
```
    ansible --version
```

Install json queries support need to use pip version to match python version from above.
```
    sudo pip3.8 install jmespath
    or
    pip3.8 install jmespath --user
``` 

## Generate GPG Certificate for using Nexus as a YUM Proxy for Kubernetes's Yum Repository for Kubernetes
If you need to use Nexus as a Proxy repository for the Kubernetes Yum Packages then the Repository needs to be setup with a PGP key as below.

1. Generate a GPG key
<pre><code>
    gpg --gen-key
</code></pre> 

2. Export the public gpg key
<pre><code>
    gpg --armor --output RPM-GPG-KEY-google --export <i>email specified at step 1</i> 
</code></pre>   

3. Export the private gpg key.
<pre><code>
    gpg --armor --output RPM-GPG-KEY-google.secret --export-secret-key <i>email specified at step 1</i> 
</code></pre>   

4. Add the priave key to the Nexus Repo

5. Add the password for the key to the nexus repo

6. Copy the public key into the install-k8s role in the files folder, update role if name of file changed.

## Root Certificate Authority
    1. Add a root ca certtificate if you are using your own ROOT CA.
          Location: pre-setup/files/rootca/
## Container Ports Considerations
The playbook configures Calico CNI to use a local Nexus container repo. The following needs to be considered when using this playbook
    
    1. Update the variables as required:-
       (a)  local_container_repo
       (b)  calico_docker_repository
       (c)  kubernetes_repository
          
    I've left the public repository urls commented out as an alternative.
    
    3. Update the enpoint firewall as required to allow the container repo network traffic.
          Lcation: calico-firewall/templates/HostNetworkPolicy.j2
## Update inverntory file

Update the variables etc as required in the inventory file

## Running the Playbook

Update the playbook hosts file as required
```yaml
  ansible_user: user
  ansible_connection: ssh
```

Command Line to run :-
```
    ansible-playbook -i inventory.yaml playbook.yaml -K 
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-K reperesents --ask-become-pass: 
ask for privilege escalation password

<br></br>
## ToDo
```
helm upgrade --install nginx-ingress helm-local/nginx-ingress --values ingress-values.yaml -n nginx-ingress --create-namespace

STEP 1: Exporting certificate file (public.crt):
OpenSSL> pkcs12 -in input.pfx -clcerts -nokeys -out public.crt

You should enter the password of the pfx file in order to export the public certificate (public.crt).

STEP 2: Exporting encrypted certificate key (private.key):
OpenSSL> pkcs12 -in input.pfx -nocerts -out private.key


```



