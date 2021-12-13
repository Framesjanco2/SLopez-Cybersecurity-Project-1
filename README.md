Stephen Lopez

###Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.



These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the _____ file may be used to install only certain pieces of it, such as Filebeat.

-Elk Instaall
-Metricbeat Playbook
-Filebeat Playbook

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting certain access to the network.
Load balancers help ensure availability by filtering incoming data and sending them to the necessary server. Jump boxes allow ease of access and administration accross multiple servers and systems. They can also provide an additional layer from inside and outside your environment.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the event logs and system data.
- Filebeat watches for log directories and specific log files
- Metricbeat collects metrics from your system and services running and can help your monitor system health by doing so

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.0.0.4 | Linux(Ubuntu       |
| Web 1      |  Server    |  10.0.0.5 | Linux(Ubuntu)      |
| Web 2      | Server     | 10.0.0.6  | Linux(Ubuntu)      |
| Elk Server | Log Server | 10.1.0.4 | Linux(Ubuntu)    |

## Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump Box provisioner machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- Personal IP address

Machines within the network can only be accessed by the Jump Box. The Elk Machine can have access from
Personal IP address through port 5601.

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|--------------|------------|
| Jump Box | Yes           | Personal   |
| Load Balancer | Yes     | Open   |
|  Web 1        | No         | 10.0.0.5    |
| Web 2         | No         |  10.0.0.6    |
| Elk Server    | Yes        | Personal    |
### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because We can utilize our environment with the least amount of services running as possible. The update process and installation is easier, as it becomes easier to replicate. 


The playbook implements the following tasks:
- Installs docker.io, pip3 and the docker module 
    # Use apt module
    - name: Install docker.io
      apt:
        update_cache: yes
        name: docker.io
        state: present

      # Use apt module
    - name: Install pip3
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

      # Use pip module
    - name: Install Docker python module
      pip:
        name: docker
        state: present

- Uses sysctl model and allows it to use more memory

  # Use sysctl module
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
        reload: yes

- Downloads and launches the docker container for our Elk server

      # Use docker_container module
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        published_ports:
          - 5601:5601
          - 9200:9200
          - 5044:5044

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.
Insert Screenshot here

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- Web 1 (10.0.0.5)
- Web 2 (10.0.0.6)
We have installed the following Beats on these machines:
- Filebeat
- Metricbeat

These Beats allow us to collect the following information from each machine:
- Filebeat is essentially a log data mover for local files found on a system. It is an agent installed on your server Filebeat will monitor log files or locations and collect logs and move, or forward them to Elasticsearch or logstash. The MYSQL database is one such example of logs it will produce
- Metricbeat collects metric data which can be used to monitor system health. One such example of this is monitoring CPU or RAM usage.

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the config file from your Ansible container to your Web VM's.
- Update the Ansible hosts file found in /etc/ansible/hosts to include the IP address of the ELk server VM and web VM's
- Run the playbook, and navigate to http://[Elk_VM_Public_IP]:5601/app/kibana to check that the installation worked as expected.

_TODO: Answer the following questions to fill in the blanks:_
- _Which file is the playbook? - The Filebeat-configuration
- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on? - You need to speicify which machine by editing the hosts file with the IP address of the web and ELK servers and selecting the group so Ansible knows which one it is part of 
- _Which URL do you navigate to in order to check that the ELK server is running?  http://[your.ELK-VM.External.IP]:5601/app/kibana.
