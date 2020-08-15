# RICE-CyberSecurity
Rice University Cybersecurity Bootcamp Projects 
## Automated ELK Stack Deployment

#### *The files in this repository were used to configure the network depicted below.*
 
![alt text](https://github.com/sapna-n-patel/RICE-CyberSecurity/blob/master/Diagrams/Project%201%20Network%20Diagram.png "Network Diagram")

---

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the ansible playbook file may be used to install only certain pieces of it, such as Filebeat.

Listed here are the Playbooks utilized in the Deployment depicted above:

  - [ELK-Install file](https://github.com/sapna-n-patel/RICE-CyberSecurity/blob/master/Ansible/Install-ELK.yml)

  - [Metricbeat-playbook file](https://github.com/sapna-n-patel/RICE-CyberSecurity/blob/master/Ansible/Metricbeat-playbook.yml)
  
  - [Filebeat-playbook file](https://github.com/sapna-n-patel/RICE-CyberSecurity/blob/master/Ansible/Filebeat-playbook.yml)
  
 ##### *An example of the Filebeat-playbook can be seen here:*
```bash
# ---
  - name: Install Filebeat
    hosts: webservers
    remote_user: azadmin
    become: true
    tasks:
      # Download.deb
      - name: Download.filebeat.deb
        command: curl https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.4.0-amd64.deb -O

      # Install filebeat
      - name: Download filebeat.deb
        command: dpkg -i filebeat-7.4.0-amd64.deb

      # Copy filebeat config
      - name: Copy config files
        copy:
          src: /etc/ansible/files/filebeat-configuration.yml
          dest: /etc/filebeat/filebeat.yml
      # Setup filebeat
      - name: setup filebeat
        command: filebeat modules enable system
      - command: filebeat setup
      - command: sudo service filebeat start

```

---


This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available (provides redundancy for our DVWA server), in addition to restricting http/web traffic to the network.
- What aspect of security do load balancers protect?
•	Load Balancers are intelligent network security devices that are responsible for effectively distributing the incoming network traffic to various servers in the network. Load balancers conduct continuous health checks on servers to ensure they can handle requests. Thus, in terms of the CIA triad, Load balancers protect the availability of resources. Load Balancing plays important role as it adds additional layers of security to network.
      - For Example: Protection against DDoS attack:
        The load balancer can detect and drop distributed denial-        
        of-service (DDoS) traffic before it gets to your website.

- What is the advantage of a jump box?
•	Having a Jump box on a network, such as the Jump_Box_Provisoner on the XCorp's Red Team Network Diagram, is advantageous because it acts as a gateway router between VMs. In turn, forcing traffic through a single node. 
      - Securing and monitoring a single node is called fanning        
        in. This process makes it simpler rather than having to   
        secure and monitor each VM behind the gateway. 
      - The jump box is exposed to the public internet, and in the case 
  of XCorp's Red Team Network Diagram, is connected to the jump box’s  
  SSH port (22). It controls access to the other machines by allowing   
  connections from specific IP addresses and forwarding to those 
  machines.              
         
 

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the metrics and system logs.
-	What does Filebeat watch for?
Filebeat module collects/ watches for and analyzes logs created by the system logging service of common Unix/Linux based distributions. Specifically, it logs information about the file system, including when and which files have changed.
 
Filebeat helps keep things simple by offering a lightweight way (low memory footprint) to forward and centralize logs and files, making the use of SSH unnecessary when you have a number of servers, virtual machines, and containers that generate logs. 


-	What does Metricbeat record?
Metricbeat collects/records metrics and statistics from the operating system and from services running on the server. 
o	"A Lightweight Shipper for Metrics"

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

|         Name         |     Function     | IP Address |   Operating System   |
|:--------------------:|:----------------:|:----------:|:--------------------:|
| Jump-Box-Provisioner |      Gateway     |  10.0.0.4  | Linux (ubuntu 18.04) |
|       Red_Web_1      |       DVWA       |  10.0.0.5  | Linux (ubuntu 18.04) |
|       Red_Web_2      |       DVWA       |  10.0.0.6  | Linux (ubuntu 18.04) |
|        ELK_VM        | ELK Stack Server |  10.1.0.0  | Linux (ubuntu 18.04) |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump-Box-Provisioner machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
-|    Source    |   Source IP   | Source Port Ranges |   Destination   | Destination Port Ranges | Protocol | Action | Description                                                                            |
|:------------:|:-------------:|:------------------:|:---------------:|-------------------------|----------|--------|----------------------------------------------------------------------------------------|
| IP Addresses | 98.195.138.18 |         Any        | Virtual Network | 80                      | Any      | Allow  | Allow Port 80/ Internet from my public IP address of workstation into my internal Vnet |
| IP Addresses | 98.195.138.18 |         Any        | Virtual Network | 3389                    | Any      | Allow  | Allow RDP from my public IP address                                                    |
| IP Addresses |    10.0.0.4   |         Any        | Virtual Network | Any                     | Any      | Allow  | Allow SSH from Jump Box IP                                                             |
| IP Addresses | 98.195.138.18 |         Any        | Virtual Network | 22                      | TCP      | Allow  | Allow access through SSH connection                                                    |

Machines within the network can only be accessed by _____.
- _TODO: Which machine did you allow to access your ELK VM? What was its IP address?_

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes/No              | 10.0.0.1 10.0.0.2    |
|          |                     |                      |
|          |                     |                      |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because we would have had to separately configure an Elasticsearch database, a Logstash server, and a Kibana server, wire them together, and then integrate them into the existing network. Which would have required 3 more Virtual Machines. Instead we are utilizing Ansible container to streamline/ simplify configuration and installation all at once.    



The playbook implements the following tasks:
- _TODO: In 3-5 bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc._
- Install Docker
- Install Python3-pip
- Install Docker module
- Increase Virtual memory
- Use more memory
- Download and launch docker elk container 

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![TODO: Update the path with the name of your screenshot of docker ps output](Images/docker_ps_output.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- 10.0.0.5 and 10.0.0.6

We have installed the following Beats on these machines:
-  Filebeats_

These Beats allow us to collect the following information from each machine:
- _TODO: In 1-2 sentences, explain what kind of data each beat collects, and provide 1 example of what you expect to see. E.g., `Winlogbeat` collects Windows logs, which we use to track user logon events, etc._

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the _____ file to _____.
- Update the _____ file to include...
- Run the playbook, and navigate to ____ to check that the installation worked as expected.

_TODO: Answer the following questions to fill in the blanks:_
- _Which file is the playbook? Where do you copy it?_
- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
- _Which URL do you navigate to in order to check that the ELK server is running?

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._
