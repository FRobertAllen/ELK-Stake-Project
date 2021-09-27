### Automated ELK Stack Deployment

This document contains the following details:
- Description of the Topology 
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build
- Access Policies

The files in this repository were used to configure the network depicted below.

C:\Users\Allen\Pictures\Networkdiagram.png

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the playbook file may be used to install only certain pieces of it, such as Filebeat.

  `/etc/ansible/install-elk-playbook.yml`



### Description of the Topology

This repository includes code defining the infrastructure below.
The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the "D*mn Vulnerable Web Application"

Load balancing ensures that the application will be highly available, in addition to restricting inbound access to the network. The load balancer ensures that work to process incoming traffic will be shared by both vulnerable web servers. Access controls will ensure that only authorized users will be able to connect in the first place.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the file systems of the VMs on the network, as well as watch system metrics, such as CPU usage; attempted SSH logins; sudo escalation failures; etc.

The configuration details of each machine may be found below.


| Name      | Function   | IP Address | Operating System     |  
|-----------|------------|------------|----------------------|
| Jumpbox   | Gateway    | 10.0.0.4   | Linux (ubuntu 20.04) |   
| DVWA 1    | Web Server | 10.0.0.8   | Linux (ubuntu 20.04) |   
| DVWA 2    | Web Server | 10.0.0.9   | Linux (ubuntu 20.04  |   
| ELK-Sever | Monitoring | 10.1.0.4   | Linux (ubuntu 20.04) |   

In addition to the above, Azure has provisioned a load balancer in front of all machines except for the jump box. The load balancer's targets are organized into the following availability zones:


- **Availability Zone 1**: DVWA 1 + DVWA 2

- **Availability Zone 2**: ELK

### ELK Server Configuration
The ELK VM exposes an Elastic Stack instance. **Docker** is used to download and manage an ELK container.

Rather than configure ELK manually, we opted to develop a reusable Ansible Playbook to accomplish the task. This playbook is duplicated below.

To use this playbook, one must log into the Jump Box, then issue: `ansible-playbook install_elk.yml` elk. This runs the `install_elk.yml` playbook on the **elk host**.



### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the jumpbox machine can accept connections from the Internet. Access to this machine is only allowed from the host IP address.

Machines within the network can only be accessed by each other. The DVWA 1 and DVWA 2 virtual machines to send traffic to the ELK server.


A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
|Jump Box  |     Yes/No          | 10.0.0.4             |
|Web-1     |     No              | 10.0.0.8             |
|Web-2     |     No              | 10.0.0.9             |
|ELK-Server|     No              | 10.1.0.4             |         


### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- This allows you to deploy to multiple servers using a single playbook

The playbook implements the following tasks:

- ... Configures the machine with Docker.
- ... Install docker.io
- ... Downloads and configures ELK docker container
- ... Activates ports 5601, 9200

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

C:\Users\Allen\Pictures\container.png

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- Web-1 10.0.0.8
- Web-2 10.0.0.9

We have installed the following Beats on these machines:
- Filebeat
- Metricbeat

These Beats allow us to collect the following information from each machine:

- **Filebeat:** Filebeat detects changes to the filesystem. Specifically, we use it to collect Apache logs.
- **Metricbeat:** Metricbeat detects changes in system metrics, such as CPU usage. We use it to detect SSH login attempts, failed `sudo` escalations, and CPU/RAM statistics.

### Using the Playbook
In order to use the playbooks, you will need to have an Ansible control node already configured. We use the **jump-box** for this purpose.
To use the playbooks, we must perform the following steps:

Copy the playbooks to the Ansible Control Node
Run each playbook on the appropriate targets

The easiest way to copy the playbooks is to use Git:
```
$ cd /etc/ansible
$ mkdir files
# Clone Repository + IaC Files
$ git clone https://github.com/yourusername/project-1.git
# Move Playbooks and hosts file Into `/etc/ansible`
$ cp project-1/playbooks/* .
$ cp project-1/files/* ./files
```
This copies the playbook files to the correct place.
Next, you must create a hosts file to specify which VMs to run each playbook on. Run the commands below:
```
$ cd /etc/ansible
$ cat > hosts <<EOF
[webservers]
10.0.0.8
10.0.0.9

[elk]
10.1.0.4
```
After this, the commands below run the playbook:
```
$ cd /etc/ansible
$ ansible-playbook install_elk.yml elk
$ ansible-playbook install_filebeat.yml webservers
$ ansible-playbook install_metricbeat.yml webservers
```
To verify success, wait five minutes to give ELK time to start up.

Then, run: `curl http://135.59.7.85:5601`. This is the address of Kibana. If the installation succeeded, this command should print HTML to the console.

As a Bonus, provide the specific commands the user will need to run to download the playbook, update the files, etc.
```
  -------Filebeat---------

- To create the filebeat-configuration.yml file: nano filebeat-configuration.yml. For this, I used the filebeat configuration file template.

- To create the playbook: nano filebeat-playbook.yml

  ---
 - name: installing and launching filebeat
	   hosts: webservers
       become: true
       tasks:

	   - name: download filebeat deb
  	     command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.7.1-amd64.deb

	   - name: install filebeat deb
  	     command: dpkg -i filebeat-7.7.1-amd64.deb

	   - name: drop in filebeat.yml
  	     copy:
   	       src: ./files/filebeat-configuration.yml
   	       dest: /etc/filebeat/filebeat.yml

	   - name: enable and configure system module
  	     command: filebeat modules enable system

	   - name: setup filebeat
  	     command: filebeat setup

	   - name: start filebeat service
  	    command: service filebeat start
---
-To run the playbook: ansible-playbook filebeat-playbook.yml

* In order to run the playbook, you have to be in the directory the playbook is at, or give the path to it (ansible-playbook /etc/ansible/roles/filebeat-playbook.yml


-------Metricbeat-------

- To create the metricbeat-configuration.yml file: nano metricbeat-configuration.yml. For this, I used the metricbeat configuration file template.

- To create the playbool: nano metricbeat-playbook.yml

---
  - name: installing and lunching metricbeat
    hosts: webservers
    become: true
    tasks:
    
  - name: download metricbeat deb
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.7.1-amd64.deb
    
  - name: install metricbeat deb
    command: sudo dpkg -i metricbeat-7.7.1-amd64.deb
    
  - name: drop in metricbeat.yml
    copy:
      src: /etc/ansible/roles/files/metricbeat-configuration.yml
      dest: /etc/metricbeat/metricbeat.yml
      
   - name: enable and configure system module
     command: metricbeat modules enable system
     
   - name: setup metricbeat
     command: metricbeat setup
     
   - name: start metricbeat service
     command: service metricbeat start
     
   ---
   
   - To run the playbook: ansible-playbook metricbeat-playbook.yml
   
   * To order to run the playbook, you have to be in the directory the playbook is at, or give the path to it (ansible-playbook /etc/ansible/roles/metricbeat-playbook.yml
```
