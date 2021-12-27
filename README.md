# Automated ELK Stack Deployment
=======

The files in this repository were used to configure the network depicted below.

![alt text](https://github.com/andrei-botez/cybersecurity/blob/main/Images/Network_Topology_ELK_readme.png "Visual Networking Diagram")

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the Ansible playbook config (.yml) file may be used to install only certain pieces of it, such as Filebeat.

- *NOTE: docker is required to deploy the containers and will be installed as part of this project. Required files have download links bellow*

File to configure the vulnerable web servers:
  - Ansible/playbook-dvwa.yml

File to configure the ELK server:
  - Ansible/playbook-elk.yml

Files to configure Filebeat and Metricbeat
  - Ansible/filebeat-config.yml
  - Ansible/metricbeat-config.yml

This document contains the following details:
  - Description of the Topologu
  - Access Policies
  - ELK Configuration
  - Beats in Use
  - Machines Being Monitored
  - How to Use the Ansible Build
  - Description of the Topology

*NOTE: The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D@mn Vulnerable Web Application. Load balancing ensures that the application will be highly available, in addition to restricting Distributed Denial of Service (DDoS) attacks to the server.*


## Advantages of using a Load Balancer and a JumpBox VM to manage the web servers:
- A load balancer takes care of the network traffic during peak times. The advantage of having a load balancer is that during busy times the load can be evenly distributed among multiple machines resulting in faster server response and increased availability (in case one or more of the machines goes down/offline). Having a JumpBox makes managing multiple machines easy and efficient as you can configure all web servers at once. The other advantage is by having a JumpBox on the same network as the webservers, you are not exposing them directly to the internet via SSH, increasing security.
Also, integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the operating system and system network.

- *NOTE: As part of this project, Filebeat and Metricbeat will be installed on the ELK server to monitor the Web VMs*

What does Filebeat watch for?
- Filebeat monitors specific log files and locations, collects log events, and forwards them to ELK for indexing.

What does Metricbeat record? 
- Metricbeat checks the metrics and statistics sending them over to ELK for indexing and further analysis.

The configuration details of each machine may be found below.

| Name    | Function               | IP Address | Operating System |
|---------|------------------------|------------|------------------|
| JumpBox | Gateway                | 10.1.0.4   | ubuntu 18.04     |
| Web 1   | Web Server             | 10.1.0.5   | ubuntu 18.04     |
| Web 2   | Web Server             | 10.1.0.6   | ubuntu 18.04     |
| ELK     | Elasticsearch & Kabana | 10.2.0.4   | ubuntu 18.04     |


## Access Policies
- The machines on the internal network are not exposed to the public Internet.
- Only the JumpBox machine can accept connections from the Internet. Access to this machine is only allowed from your personal IP address. 
  - *NOTE: You can find this by searching "what is my IP address" on Google*

- Machines within the network can only be accessed from the JumpBox Ansible container via SSH (port 22), ideally, using public key as authentication.

- A summary of the access policies in place can be found in the table below.

  | Name    | Publicly Accessible | Allowed IP Addresses |
  |---------|---------------------|----------------------|
  | JumpBox | No                  | Your own IP address  |
  | Web 1   | No                  | 10.1.0.4             |
  | Web 2   | No                  | 10.1.0.4             |
  | ELK     | No                  | 10.1.0.4             |


## Elk Configuration

The main advantage of automating configurations with Ansible:
- It is advategious to automate the process as it substantially reduces deployment time and decreases the chance for human error.


The playbook implements the following tasks:
- Install Docker
- Increase virtual memory
- Download and launch ELK docker container
- Enable Docker service on boot
- Install and configure filebeat and metricbeat


The following screenshot displays the result of running docker ps after successfully configuring the ELK instance.

![alt text](https://github.com/andrei-botez/cybersecurity/blob/main/Images/docker_ps_output.png "Docker output")


## Target Machines & Beats:

This ELK server is configured to monitor the following machines:
- Web 1 (10.1.0.5)
- Web 2 (10.1.0.6)

These Beats allow us to collect the following information from each machine: 
- Filebeat is used to collect audit logs, deprecation logs, gc logs, server logs (e.g. Apache servers), and slow logs, sending them to a dashboard in Kibana. 
- Metricbeat is used to get more specific information on how the servers are performing, by monitoring the CPU, memory and disk utilization, (especially important during heavy loads). It is also capable of monitoring the performance of the operating system and services running on it.


## Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned:

- SSH into the control node and follow the steps below:
  
  - Update the hosts file to include the following:
    
  - *NOTE: you need to modify the hosts file (/etc/ansible/hosts) in order to specify the IP address of the target machine; the second piece is in the ansible .yml configuration playbooks which specifies the hosts (e.g. "hosts: ELKServer" or "hosts: webservers")*
    ```
    nano /etc/ansible/hosts
    ```
  - Add the following to the hosts file and save:

    ```bash
    [webservers]
    10.1.0.5 ansible_python_interpreter=/usr/bin/python3
    10.1.0.6 ansible_python_interpreter=/usr/bin/python3

    [ELKServer]
    10.2.0.4 ansible_python_interpreter=/usr/bin/python3
    ```

  - Download and run the playbook to deploy ELK, then navigate to http://localhost:9200/_status to check that the installation worked as expected and server is running:
    ```
    curl -o /etc/ansible/playbook-elk.yml https://raw.githubusercontent.com/andrei-botez/cybersecurity/main/Ansible/playbook-elk.yml
    ansible-playbook /etc/ansible/playbook-elk.yml
    ```
    
  - Download filebeat-config.yml and metricbeat-config.yml files to /etc/ansible, then run both playbooks:
    ```bash
    curl -o /etc/ansible/filebeat-config.yml https://raw.githubusercontent.com/andrei-botez/cybersecurity/main/Ansible/filebeat-config.yml
    curl -o /etc/ansible/metricbeat-config.yml https://raw.githubusercontent.com/andrei-botez/cybersecurity/main/Ansible/metricbeat-config.yml
      
     nsible-playbook /etc/ansible/filebeat-config.yml
    ansible-playbook /etc/ansible/metricbeat-config.yml
    ```
  - Check http://40.122.193.196:5601/app/kibana#/home?_g=() to see if Kibana was successfully installed (change it to your own ELK VM IP address)
    
  - If you can access the link you have successfully completed this project.
