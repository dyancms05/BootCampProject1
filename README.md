# BootCampProject1
## Automated ELK Stack Deployment

[Site reference #1](https://avinetworks.com/what-is-load-balancing)
[Site reference #2](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-overview.html)
[Site reference #3](https://www.elastic.co/guide/en/beats/metricbeat/current/metricbeat-overview.html)
[Site reference #4](https://logz.io/blog/beats-tutorial/)

The files in this repository were used to configure the network depicted below.

![Elk Stack Diagram](https://github.com/dyancms05/BootCampProject1/blob/main/Diagram/Elk%20Stack.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the *config* file may be used to install only certain pieces of it, such as Filebeat.

  - [Ansible Config File](https://github.com/dyancms05/BootCampProject1/blob/main/Ansible/ansible.cfg)
  - [Hosts File](https://github.com/dyancms05/BootCampProject1/blob/main/Ansible/hosts.yml)
  - [Elk Installation File](https://github.com/dyancms05/BootCampProject1/blob/main/Ansible/install-elk.yml)
  - [Filebeat Config File](https://github.com/dyancms05/BootCampProject1/blob/main/Ansible/filebeat-config.yml)
  - [Filebeat Playbook File](https://github.com/dyancms05/BootCampProject1/blob/main/Ansible/filebeat-playbook.yml)
  - [Metricbeat Config File](https://github.com/dyancms05/BootCampProject1/blob/main/Ansible/metricbeat-config.yml)
  - [Metricbeat Playbook File](https://github.com/dyancms05/BootCampProject1/blob/main/Ansible/metricbeat-playbook.yml)

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly *reliable*, in addition to restricting *access* to the network.
	- What aspect of security do load balancers protect? *prevents DDos attacks*
	- What is the advantage of a jump box? *Security, Trafic control*

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the *data* and system *logs*.
	- What does Filebeat watch for? *Filebeat monitors the log files or locations that you specify, collects log events, and forwards them either to Elasticsearch or Logstash for indexing*
	- What does Metricbeat record? *Metricbeat collect metrics from the operating system and from services running on the server*

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump-Box-Provisioner | Gateway  | 138.91.191.123 / 10.2.0.4 | Linux |
| Web-1 | Web Server | 104.42.184.148 / 10.2.0.7 | Linux |
| Web-2 | Web Server | 104.42.184.148 / 10.2.0.6 | Linux |
| Web-3 | Web Server | 104.42.184.148 / 10.2.0.11 | Linux |
| ElkStackVM | Elk Server | 52.246.255.142 / 10.3.0.4 | Linux |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the *ElkStackVM* machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
	- Whitelisted IP addresses: 52.246.255.142:5601/

Machines within the network can only be accessed by *Jump-Box-Provisioner*.
	- Which machine did you allow to access your ELK VM? What was its IP address? *Jump-Box-Provisioner 138.91.191.123 TCP 5601*

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump-Box-Provisioner | No  | 10.2.0.7,10.2.0.6,10.2.0.11,10.3.0.4 |
| Web-1 | No | 10.2.0.4 ssh 22 |
| Web-2 | No | 10.2.0.4 ssh 22 |
| Web-3 | No | 10.2.0.4 ssh 22 |
| ElkStackVM | No | 138.91.191.123 tcp 5601 |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
	- What is the main advantage of automating configuration with Ansible? *Saves time*

The playbook implements the following tasks:
- In 3-5 bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc.
	- Specify the host machine and remote user
		```bash
		- name: Configure Elk VM with Docker
		    hosts: elk
		    remote_user: azdmin
		    become: true
		    tasks:
		```
	- Install services
		```bash
        - name: Install docker.io
          apt:
            update_cache: yes
            force_apt_get: yes
            name: docker.io
            state: present
	    
        - name: Install python3-pip
          apt:
            force_apt_get: yes
            name: python3-pip
            state: present
	    
        - name: Install Docker module
          pip:
            name: docker
            state: present
		```
	- Increase Virtual memory
		```bash
	    - name: Increase virtual memory
		  command: sysctl -w vm.max_map_count=262144

	    - name: Use more memory
          sysctl:
            name: vm.max_map_count
            value: 262144
            state: present
            reload: true
		 ```
	- Publish Ports
		```bash
        - name: download and launch a docker elk container
          docker_container:
            name: elk
            image: sebp/elk:761
            state: started
            restart_policy: always
            # Please list the ports that ELK runs on
            published_ports:
              -  5601:5601
              -  9200:9200
              -  5044:5044
	    
        - name: Enable service docker on boot
          systemd:
            name: docker
            enabled: yes
		```
		
The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![docker ps output](https://github.com/dyancms05/BootCampProject1/blob/main/Images/docker_ps.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- List the IP addresses of the machines you are monitoring
	- Web-1 10.2.0.7
	- Web-2 10.2.0.6
	- Web-3 10.2.0.11

We have installed the following Beats on these machines:
	- Filebeat
	- Metricbeat

These Beats allow us to collect the following information from each machine:
	- Filebeat is used for collecting and shipping log files.
	- Metricbeat collects and reports various system-level metrics for various systems and platforms.

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the configuration file to Ansible container.
- Update the configuration file to include the username and password and Elk VM IP address.
```bash
output.elasticsearch:
hosts: ["10.3.0.4:9200"]
username: "elastic"
password: "changeme"
```
```bash
setup.kibana:
host: "10.3.0.4:5601"
```
- Run the playbook, and navigate to *[Filebeat installation page](http://52.246.255.142:5601/app/kibana#/home/tutorial/systemLogs), scroll down to 5:Module status and click on Check data* to check that the installation worked as expected.

Answer the following questions to fill in the blanks:
- _Which file is the playbook? Where do you copy it?_
- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
- Which URL do you navigate to in order to check that the ELK server is running
	- [http://52.246.255.142:5601/app/kibana](http://52.246.255.142:5601/app/kibana)

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._