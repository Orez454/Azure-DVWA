# Azure-DVWA
Secure Azure Environment with load balanced DVWA servers with a jump box and ELK stack.

## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![Network Diagram]('Images/Network Diagram.jpeg')

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. The Filebeat and Metricbeat configuration files must be downloaded locally and updated with appropriate configurations prior to running the playbooks.

  - Install and start ELK container: Ansible/install-elk.yml
  - Install, configure, and start Filebeats: Ansible/filebeat-playbook.yml
  - Install, configure, and start Metricbeats: Ansible/metricbeat-playbook.yml


This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting access to the each of the individual servers. The load balancer helps reduce the load on the two DVWA web servers maintaining a highly available system with fault tolerance. A jump box provisioner is configured for public key SSH authentication for any direct configuration of the servers, while only HTTP is allowed to the load balancer through the network security group. 

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the system and system performance.
- Filebeat is installed on the web servers to directly monitor the system logs for any unauthorized or unexpected changes.
- Metricbeat is installed on the web servers to monitor the performance and system resources. 

The configuration details of each machine may be found below.

| Name     | Function        | IP Address | Operating System |
|----------|-----------------|------------|------------------|
| Jump Box | Gateway         | 10.1.0.4   | Ubuntu 20.04     |
| Web-1    | DVWA Web Server | 10.1.0.5   | Ubuntu 20.04     |
| Web-2    | DVWA Web Server | 10.1.0.6   | Ubuntu 20.04     |
| ELK      | ELK Monitor     | 10.0.0.4   | Ubuntu 20.04     |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump Box machine and load balancer can accept connections from the Internet. Access to this machine is only allowed from the my single IP Address 73.78.252.32. The Security Group rules are configured to only allow external traffic on Port 22 from 73.78.252.32 to the Jump Box server, and on Port 80 from 73.78.252.32 to the virtual network. The security group rules only allow a single IP address to allow only myself to connect and test around the environment.

Machines within the network can only be accessed by SSH from the Jump Box machine.
- Security Group rule allows port 22 traffic from the Jump Box internal private IP to the virtual network but does not allow any other external traffic to the internal IP addresses.

A summary of the access policies in place can be found in the table below.

| Name              | Publicly Accessible | Allowed IP Addresses   |
|-------------------|---------------------|------------------------|
| Jump Box          | Yes                 | 73.78.252.32           |
| Web-1             | No                  | 10.1.0.4               |
| Web-2             | No                  | 10.1.0.4               |
| ELK               | Yes                 | 73.78.252.32, 10.1.0.4 |
| Web Load Balancer | Yes                 | 73.78.252.32           |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because the configuration can be automated and deployed in different environments and will act identically. Ansible playbooks provide a robust solution to mass installation and deployment of common resources without risking manual entry errors, such as installing Filebeat or Metricbeat on any server in our network we need to monitor.

The playbook implements the following tasks:
- Installs the Docker and Python3-pip packages and downloads the ELK docker image
- Creates and starts the docker container for ELK with published ports 5601:5601, 9200:9200, and 5044:5044
- Enables the docker service to start on reboot.

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![ELK Docker Container](Images/ELK_container.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- Web-1 VM: 10.1.0.5
- Web-2 VM: 10.1.0.6

We have installed the following Beats on these machines:
- Filebeats
- Metricbeats

These Beats allow us to collect the following information from each machine:
- Filebeats: The filebeat agent enables the monitoring of system log files on the web servers. The agent forwards any log changes to the ELK server for Elasticsearch or Logstash.
- Metricbeats: The metricbeat agent monitors the performance on each of the web servers by collecting and forward metrics from the operating system and running services to the ELK machine.

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the install-elk.yml, filebeat-playbook.yml, metricbeat-playbook.yml, filebeat-config.yml, metricbeat-config.yml files to your Ansible control node. The install-elk.yml, filebeat-playbook.yml, and metricbeat-playbook.yml are Ansible playbooks to install the ELK stack, filebeats agent, and metricbeats agent respectively. filebeat-config.yml and metricbeat-config.yml are configuration files required by Filebeat and Metricbeat to operate correctly and are needed for the playbooks to correctly install and configure the agents.
- Update the hosts file to include the appropriate server groupings for Ansible control, in this case the group [webservers]  is used for control of web-1 and web-2, and [elk] for control of the elk server. Include all required server IP addresses.
- Run the install-elk.yml playbook, and navigate to 'http://<ELK Server External IP>:5601/app/kibana' to check that the installation worked as expected.
	'sudo ansible-playbook '
- Once the ELK server is installed, update the filebeat-config.yml file to include the appropriate internal IP address for your elk server. 
- Run the filebeat-playbook.yml playbook, and navigate to your Kibana Filebeat dashboard and verify incoming data.
- Update the metricbeat-config.yml file to include the appropriate internal IP address for your elk server.
- Run the metricbeat-playbook.yml playbook, and navigate to your Kibana Metricbeat dashboard to verify incoming data.
