# cyber-repo
This repository is designed to share my Automated ELK Stack Deployment, 
created as a project for cyber boot camp.
The files in this repository were used to configure the network depicted below.

Network Diagram:
![image](https://github.com/JaniceW777/cyber-repo/blob/main/Diagrams/Azure_NetworkDiagram_Spring2021Project.png)

The Ansible files have been tested and used to generate a live ELK deployment on Azure. 
They can be used to recreate the entire deployment, or portions of it can be used, such as to install Filebeat only.
Because the VMs on which Filebeat needed to be installed were not able to access the internet, 
the source file for Filebeat install was secure copied (scp command) into each VM.
The Filebeat configuration file is quite lengthy and I only changed two minor portions, so they are all I have included here.
The lines changed are 1106 and 1806, and the changes made were replacing the IP address in the file
with the IP address of my ELK-server-VM.


This document contains the following details:

--Description of the Topology


--ELK Configuration:

   - Target Machines

   - Beats in Use

   - How to Use the Playbooks


--Walkthrough of the Steps Taken to Create the Network




DESCRIPTION OF THE TOPOLOGY

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.
Load balancing ensures that the application will be highly available, in addition to providing an additional layer of protection for network access.
The first layer of access protection is provided by a "jump box" virtual machine, which provides an outward-facing IP address through which traffic flows to other, internal-IP-only VMs.

Integrating an ELK server (ELK=Elastic Stack-Logstash-Kibana) allows users to easily monitor the vulnerable (internal) virtual machines (VMs) for changes to the expected logs -- time, traffic volume, location, and more -- and system performance.

The configuration details of each machine may be found below. 
The naming convention of "RedTeam" was used to indicate the scenario in which a company was preparing an environment for new pentesters to use.
The size of each VM is included because the project was built within the parameters of using a free Azure account, which allows for up to 4 CPUs per region.


**Name**|**Function**|**External IP Address**|**Internal IP Address**|**Operating System**|**Size**
:-----:|:-----:|:-----:|:-----:|:-----:|:-----:
Jump-Box|Front door|104.42.6.112|10.0.0.4|Linux|1 cpu, 2 ram
Web-1|VM|none|10.0.0.5|Linux|1 cpu, 2 ram
Web-2|VM|none|10.0.0.6|Linux|1 cpu, 2 ram
Web-3|redundancy|none|10.0.0.7|Linux|1 cpu, 2 ram
Load Balancer|availability, access control|104.210.37.86|n/a|Linux|n/a
Elk-Server-VM|monitoring|168.61.214.10|10.1.0.4|Linux|2 cpu, 8 ram




ELK CONFIGURATION

Ansible was used to automate configuration of the ELK machine. 
The configuration was automated in order to simplify future installations.
The use of Ansible YAML file, known as a playbook, also offers a responsive look at which part of the process may be breaking down.

The playbook implements the following tasks:

-Install Docker
(from the Docker website: Docker is an open platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly.)

- Download the "image" of the ELK stack, meaning the set of instructions for running the package of monitoring software from Elasticstack consisting of Logstash and Kibana.

- Install those instructions and configure (as much or as little as you want to) how the logs are sent and stored.

- Ensure the monitoring process will run continuously (or on the timeframe you choose).


-- Target Machines & Beats -- 
This ELK server is configured to monitor the Web-1 and Web-2 virtual machines. A conscious choice was made to leave off the "Web-3-redundancy" machine for the time being, as I thought it might add more data than is needed at this point, when the machines are being used only minimally.

"Beats" are specialized data shippers that Elastic offers for various uses. While there are currently 8 Beats in all that Elasticstack currently offers, only 2 were chosen for this project: Filebeat and Metricbeat.

At the moment only Filebeat has been installed. Filebeat collects and organizes log files, and is most often used in tandem with Logstash. 
Log files provide information about what is going on within a machine (real or virtual) -- 
including processes, events, communications with other applications and/or the operating system.


-- How to Use the Playbooks --
In order to use the playbooks, you will need to have an Ansible control node already configured. (The pentest.yml file can help.)
Once you have such a control node provisioned, SSH into the control node and follow the steps below:

Copy the latest filebeat and/or metricbeat configuration file from the website https://www.elastic.co/elastic-stack to /etc/ansible/roles.

Update the sections of the config file that tell the software where to send the files it collects: the internal IP of the ELK-Server VM. (Refer to the text file Changes-to-Filebeat-config-yml.txt for examples.)

Run the playbook, and SSH into your ELK server to see if the installation worked: run sudo docker ps -a. Is a container called filebeat / metricbeat listed? It worked.

Navigate to http://<Public-IP of Your ELK Server>:5601/app/kibana to check that the Elk server is running and connected to the web. If Kibana loads, you are connected. The containers still need to be started on the machines in order for data to flow, and ports between the target machines and the ELK server need to be opened. (Details below).



WALKTHROUGH OF THE STEPS TO CREATE THE NETWORK

--coming soon!--
