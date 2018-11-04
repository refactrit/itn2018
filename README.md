## Azure ARM + Ansible Solution Blueprint Sample

This project combines Microsoft Azure's Resource Manager with Red Hat Ansible to automate the provisioning of an IIS web server on a single Windows Server 2016 virtual machine. After running, a message is posted to a Slack channel and a ticket is created in ConnectWise Manage.


### How it Works

Ansible is used to drive the process: 

1. A playbook launches the virtual machine ARM template deployment using the `azure_rm_deployment` module.
2. A bootstrap script VM extension configures WinRM by starting an HTTPS listener, enabling Basic auth, and installing a self-signed certificate.
2. The Azure dynamic inventory (`azure_rm.py`) is used to retrieve the public IP address. 
3. The playbook waits for the VM to come online
4. The playbook remotes into the VM, enforces Windows Firewall rules, installs IIS, and installs the web server ZIP file.
5. The playbook runs a second ARM template deployment to create the Recovery Vault and to add the virtual machine to the backup items list.
6. The playbook sends a Slack notification and creates a ticket in ConnectWise.

The following Azure resources are created:

* 1  Resource Group
* 1  Virtual Network + Subnet
* 1  Security Group
* 1  Public IP Address
* 1  Network Interface
* 1  Virtual Machine
* 1  Recovery Vault


### How to Run

1. Install Ansible and the required dependencies for Azure modules. Ansible requires a Linux-based OS to run. This project was tested on CentOS 7.3.1611
    * Read more here: https://docs.ansible.com/ansible/2.7/scenario_guides/guide_azure.html
2. Clone this repository
3. cd to the project directory
4. Set any variables in `inventories/vars.yaml`
5. If the VM username or password is changed, also update these in `inventories/hosts`
6. Set the following environment variables for the required credentials:
    * AZURE_CLIENT_ID
    * AZURE_SECRET
    * AZURE_SUBSCRIPTION_ID
    * AZURE_TENANT
    * SLACK_TOKEN
    * CONNECTWISE_COMPANY_ID
    * CONNECTWISE_PUBLIC_KEY
    * CONNECTWISE_PRIVATE_KEY
7. Run the shell script: `./run.sh`
