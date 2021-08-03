# ELK-stack_in_Azure
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

Log into your Azure portal.
Add a Resource Group, naming it something like, “RedTeam-Resource-Group.” You’ll need to select a region, such as “West US.” Remember your region.
Add a Virtual Network, naming it something like “RedTeam-VNet1.” Make a note of the IP Address space (such as 10.0.0.0/16) and the subnet default (such as 10.0.0.0/24).
Create a Network Security Group, naming it something like “RedTeam-NSG.”
From within your NSG, create an inbound rule that does not allow any inbound IP addresses on any ports to go to any destinations or any ports. Name it “Default-Deny.” When you  look at the list of rules, there will be 3 that Azure puts in automatically, plus your Default-Deny rule. There will be a triangle warning next to your rule that you can ignore.
In your Terminal, create your SSH key by running the command ssh-keygen. Cat the file where the public key is stored and save it to a text file.
Back in Azure, create a Virtual Machine (VM). 
 On the Basics tab:
Select your resource group
be sure to select the same region you selected in Step 2.
name the VM something like Jump-Box-Provisioner. This will serve as a gateway VM.
Select a size: If you are using a free Azure account, you’ll be limited to a total of 4 CPUs over all your VMs. A good one for this is B1_ms, which has 1 CPU and 2 RAM. 
Authentication section: choose “SSH public key,” input a user name you will remember, and  then paste the public key you created in your terminal.
On the Networking tab:
 Choose your Virtual Network and subnet.
In the Public IP section, select “Create new” so you can select “static.”
At NIC Network Security Group, select “Advanced” so you can choose the NSG you created in Step 4.
Select Review + create, then wait for the approval, then create.
Create two more virtual machines, the process is similar to Step 7 but not exactly. You will create them one at a time:
On the Basics tab:
Select your resource group
be sure to select the same region you selected in Steps 2 and 7.
Name the VMs Web-1 and Web-2.
Select size: B1_ms, which has 1 CPU, 2 RAM
Authentication section: choose “SSH public key,” input the user name you used for Step 7, and  then paste the public key you created in your terminal.
On the Networking tab:
 Choose your Virtual Network and subnet.
In the Public IP section, select “None.”
At NIC Network Security Group, select “Advanced” so you can choose the NSG you created in Step 4.
Set up Jump-Box Administration: 
There is definitely a step needed here, like click on this VM
Create a rule, number it 100, Allow SSH from my network. (You’ll need your host computer’s IP address, which you can get from whatsmyip.org or Settings>Wifi>Properties
Install docker.io on Jump-Box and install Ansible container.
Need instrux for these steps
Probably two steps
Make a note of the container’s name and ID. (Command to see running containers.) 
Create NSG rule to allow the Jump Box “full access” to Vnet.
Set up My Provisioner, part 1. “Launch a new VM from the Azure portal that can only be accessed using a new SSH key from the container running inside my Jump Box.” Use the following commands:
sudo docker start <container name>
sudo docker attach <container name>
ssh-keygen from within the Ansible container  (you know you are in the container by the command prompt)
Go to the Web-1 VM and the Web-2 VM and “Reset password” (lefthand menu), using the new key you just created.
Check the connections by running ssh <yourusername>@<Web-1IP> and ssh <yourusername>@<Web-2IP>
exit
nano /etc/ansible/hosts: 
uncomment the line [webservers]
Add the IP addresses for Web-1 and Web-2
Insert the line for python after each one
Save and close
Nano /etc/ansible/ansible.cfg:
Uncomment the line [webservers]
Replace “root” with your user name
Confirm the changes you just made: ansible  -m ping all
You should get ping and pong
Set up My Provisioner, Part 2.
Create a YAML file to install docker.io (we called it pentest.yml)
ssh to Web-1 and run ansible-playbook /etc/ansible/pentest.yml
Test it by running curl localhost/setup.php
Exit, ssh to Web-2 and run the same two commands.
Set up Load Balancer (LB).
Give it a basic sku, a static public IP address, and name the IP address RedTeam-LB-IP
Add a health probe (insert details)
Create a backend pool, add Web-1 and Web-2 (insert details)
Configure the LB and NSG to expose port 80 to an external IP that’s being used to test the setup:
Load balancing rule (name: Pentest LBR):
TCP/80:80/Client IP & Protocol. (This forwards port 80 traffic from the LB to the Vnet)
NSG rule (name: Port 80): Allow port 80 traffic from external IP to Vnet
Delete “Default-Deny” rule
Verify in browser with http://<LB public IP>/setup.php
Build a redundancy VM.
Name: Web-3-redundancy
Location: US West
Size: B1ms (1 CPU, 2G RAM)
No public IP
NIC, Red Team Availability set
Test the connection by running these commands: 
ping <IP address>
ssh <yourusername>@<IP address>
Exit
Add the IP to hosts file
Test with the command: ansible -m ping all
Run: ansible-playbook /etc/ansible/pentest.yml
Test one more time:
ssh <yourusername>@<IP address>
Curl localhost/setup.php

