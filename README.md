
## Automated ELK Stack Deployment

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

  /etc/ansible/install-elk-playbook.yml



### Description of the Topology

This repository includes code defining the infrastructure below.
The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly efficient, in addition to restricting traffic to the network.
- Load balancers protects the system from DDoS attacks by shifting attack traffic.
- Load Balancing contributes to the Availability aspect of security in regards to the CIA Triad.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the logs and system traffic.
- Filebeat monitors the log files or locations that you specify, collects log events, and forwards them either to Elasticsearch or Logstash for indexing.
- Metricbeat helps you monitor your servers by collecting metrics from the system and services running on the server, such as: Apache. HAProxy.

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name      | Function   | IP Address | Operating System     |  
|-----------|------------|------------|----------------------|
| Jumpbox   | Gateway    | 10.0.0.4   | Linux (ubuntu 20.04) |   
| Web-1     | Server     | 10.0.0.8   | Linux (ubuntu 20.04) |   
| Web-2     | Server     | 10.0.0.9   | Linux (ubuntu 20.04  |   
| ELK-Sever | Monitoring | 10.1.0.4   | Linux (ubuntu 20.04) |   

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the jumpbox machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- My personal IP Address

Machines within the network can only be accessed by SSH.
-  The ELK Server is only accessible by SSH from the JumpBox.

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

Filebeat: Filebeat detects changes to the filesystem. Specifically, we use it to collect Apache logs.
Metricbeat: Metricbeat detects changes in system metrics, such as CPU usage. We use it to detect SSH login attempts, failed sudo escalations, and CPU/RAM statistics.

### Using the Playbook
In order to use the playbooks, you will need to have an Ansible control node already configured. We use the jump box for this purpose.
To use the playbooks, we must perform the following steps:

Copy the playbooks to the Ansible Control Node
Run each playbook on the appropriate targets

The easiest way to copy the playbooks is to use Git:
$ cd /etc/ansible
$ mkdir files
# Clone Repository + IaC Files
$ git clone https://github.com/yourusername/project-1.git
# Move Playbooks and hosts file Into `/etc/ansible`
$ cp project-1/playbooks/* .
$ cp project-1/files/* ./files
This copies the playbook files to the correct place.
Next, you must create a hosts file to specify which VMs to run each playbook on. Run the commands below:
$ cd /etc/ansible
$ cat > hosts <<EOF
[webservers]
10.0.0.8
10.0.0.9
