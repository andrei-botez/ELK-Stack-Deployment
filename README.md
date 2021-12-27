Automated ELK Stack Deployment
The files in this repository were used to configure the network depicted below.
Note: The following image link needs to be updated. Replace diagram_filename.png with the name of your diagram image file.

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the Ansible playbook config (.yml) file may be used to install only certain pieces of it, such as Filebeat.

playbook-dvwa.yml
playbook-elk.yml

This document contains the following details:

Description of the Topologu
Access Policies
ELK Configuration
Beats in Use
Machines Being Monitored
How to Use the Ansible Build
Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.
Load balancing ensures that the application will be highly available, in addition to restricting Distributed Denial of Service (DDoS) attacks to the network.

TODO: What aspect of security do load balancers protect? What is the advantage of a jump box?
A: a load balancer takes care of the network traffic during peak times. The advantage of having a load balancer is that during busy times the load can be evenly distributed among multiple machines resulting in faster server response and increased availability (in case one or more of the machines goes down/offline). Having a JumpBox makes managing multiple machines easy and efficient as you can configure all web servers at once. The other advantage is by having a JumpBox on the same network as the webservers, you are not exposing them directly to the internet via SSH, increasing security.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the operating system and system network.

TODO: What does Filebeat watch for?
A: Filebeat monitors specific log files and locations, collects log events, and forwards them to ELK for indexing.

TODO: What does Metricbeat record?
A: Metricbeat checks the metrics and statistics sending them over to ELK for indexing and further analysis.

The configuration details of each machine may be found below.
Note: Use the Markdown Table Generator to add/remove values from the table.

| Name    | Function               | IP Address | Operating System |
|---------|------------------------|------------|------------------|
| JumpBox | Gateway                | 10.1.0.4   | ubuntu 18.04     |
| Web 1   | Web Server             | 10.1.0.5   | ubuntu 18.04     |
| Web 2   | Web Server             | 10.1.0.6   | ubuntu 18.04     |
| ELK     | Elasticsearch & Kabana | 10.2.0.4   | ubuntu 18.04     |

Access Policies
The machines on the internal network are not exposed to the public Internet.
Only the JumpBox machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses: 173.32.145.0/24 (personal IP hidden)

Machines within the network can only be accessed from the JumpBox Ansible container via SSH (port 22) using public key authentication.

A summary of the access policies in place can be found in the table below.

| Name    | Publicly Accessible | Allowed IP Addresses |
|---------|---------------------|----------------------|
| JumpBox | No                  | 173.32.145.0/24      |
| Web 1   | No                  | 10.1.0.4             |
| Web 2   | No                  | 10.1.0.4             |
| ELK     | No                  | 10.1.0.4             |

Elk Configuration
Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because it substantially reduces deployment time and decreases the chances for human error.

TODO: What is the main advantage of automating configuration with Ansible?

The playbook implements the following tasks:

TODO: In 3-5 bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc.

-Install Docker
-Increase virtual memory
-Download and launch ELK docker container
-Enable Docker service on boot
-Install and configure filebeat and metricbeat

The following screenshot displays the result of running docker ps after successfully configuring the ELK instance.
Note: The following image link needs to be updated. Replace docker_ps_output.png with the name of your screenshot image file.

Target Machines & Beats
This ELK server is configured to monitor the following machines:

TODO: List the IP addresses of the machines you are monitoring
A: Web 1 (10.1.0.5) and Web 2 (10.1.0.6)

We have installed the following Beats on these machines:

TODO: Specify which Beats you successfully installed
A: Filebeat and Metricbeat

These Beats allow us to collect the following information from each machine:

TODO: In 1-2 sentences, explain what kind of data each beat collects, and provide 1 example of what you expect to see. E.g., Winlogbeat collects Windows logs, which we use to track user logon events, etc.
A: 
-Filebeat is used to collect audit logs, deprecation logs, gc logs, server logs (e.g. Apache servers), and slow logs, sending them to a dashboard in Kibana.
-Metricbeat is used to get more specific information on how the servers are performing, by monitoring the CPU, memory and disk utilization, (especially important during heavy loads). It is also capable of monitoring the performance of the operating system and services running on it.

Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned:

SSH into the control node and follow the steps below:

-Copy the playbook-elk.yml file to /etc/ansible. 

-Update the hosts file to include the following:
''''
[webservers]
10.1.0.5 ansible_python_interpreter=/usr/bin/python3
10.1.0.6 ansible_python_interpreter=/usr/bin/python3

[ELKServer]
10.2.0.4 ansible_python_interpreter=/usr/bin/python3
''''

Run the playbook, and navigate to http://40.122.193.196:5601/app/kibana to check that the installation worked as expected.

TODO: Answer the following questions to fill in the blanks:

Which file is the playbook? Where do you copy it?
A: Copy the filebeat-config.yml and metricbeat-config.yml file to /etc/ansible

Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?
A: you need to modify the hosts file (/etc/ansible/hosts) in order to specify the IP address of the target machine; the second piece is in the ansible .yml configuration playbooks which specifies the hosts (e.g 'hosts: ELKServer' or 'hosts: webservers').

_Which URL do you navigate to in order to check that the ELK server is running?
A: you can check http://localhost:9200/_status

As a Bonus, provide the specific commands the user will need to run to download the playbook, update the files, etc.
#NOTE: run the following commands from the Ansible container (running on the JumpBox Provisioner machine). 

-Run the following to download the ELK container config file
curl -o /etc/ansible/playbook-elk.yml https://raw.githubusercontent.com/andrei-botez/cybersecurity/main/Ansible/playbook-elk.yml

-Run the following to download the Filebeat config file
curl -o /etc/ansible/filebeat-config.yml https://raw.githubusercontent.com/andrei-botez/cybersecurity/main/Ansible/filebeat-config.yml

-Run the following to download the Metricbeat config file
curl -o /etc/ansible/metricbeat-config.yml https://raw.githubusercontent.com/andrei-botez/cybersecurity/main/Ansible/metricbeat-config.yml

-Run the following to update the hosts file
nano /etc/ansible/hosts

-Make sure it includes:
''''
[webservers]
10.1.0.5 ansible_python_interpreter=/usr/bin/python3
10.1.0.6 ansible_python_interpreter=/usr/bin/python3

[ELKServer]
10.2.0.4 ansible_python_interpreter=/usr/bin/python3
''''

-Run the following to deploy the elk server
ansible-playbook /etc/ansible/playbook-elk.yml

-Run the following to deploy Filebeat and Metricbeat
ansible-playbook /etc/ansible/filebeat-config.yml
ansible-playbook /etc/ansible/metricbeat-config.yml
