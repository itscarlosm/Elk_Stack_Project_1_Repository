# Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

https://github.com/itscarlosm/Elk_Stack_Project_1_Repository/blob/main/Diagrams/ElkStackProjectVNetDiagram.png

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to recreate the entire deployment pictured above. Alternatively, select portions of the playbook file may be used to install only certain pieces of it, such as Filebeat.

  - filebeat-playbook.yml, metricbeat-playbook.yml

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting access to the network.

- _What aspect of security do load balancers protect? What is the advantage of a jump box?_
    Availability, one of the cornerstones the CIA triad (confidentiality, integrity, and availability), refers to the actual availability of your data. Load balancers ensure availability by defending against distributed denial-of-service (DDoS) attacks by shifting attack traffic. 

- _What is the advantage of a jump box?_
    A JumpBox is a secure computer that all administrators first connect to before launching any administrative tasks or use as an origiantion point to connect to other servers.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the infrastructure and system logs.

- _What does Filebeat watch for?_
    Filebeat monitors the log files or locations that you specify, collects log events, and forwards them either to Elasticsearch or Logstash for indexing.

- _What does Metricbeat record?_
     Metricbeat takes the metrics and statistics that it collects and ships them to the output that you specify, such as Elasticsearch or Logstash.

The configuration details of each machine may be found below.

| Name                 | Function   | IP Address | Operating System     |
|----------------------|------------|------------|----------------------|
| Jump-Box-Provisioner | Gateway    | 10.0.0.4   | Linux (Ubuntu 20.04) |
| Web-1                | Web Server | 10.0.0.7   | Linux (Ubuntu 20.04) |
| Web-2                | Web Server | 10.0.0.6   | Linux (Ubuntu 20.04) |
| ElkVM                | ELK Server | 10.1.0.4   | Linux (Ubuntu 20.04) |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the JumpBox machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- Personal IP Address

Machines within the network can only be accessed by SSH.
- _Which machine did you allow to access your ELK VM? What was its IP address?_
    The Jump-Box-Provisioner (10.0.0.4) is the only machine allowed to access the ElkVM (10.1.0.4).

A summary of the access policies in place can be found in the table below.

| Name                | Publicly Accessible | Allowed IP Addresses  |
|---------------------|---------------------|-----------------------|
| Jump-Box-Provisioner| No                  | Personal  IP          |
| Web-1               | No                  | 10.0.0.4              |
| Web-2               | No                  | 10.0.0.4              |
| ElkVM               | No                  | 10.0.0.4 & Personal IP|

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- _What is the main advantage of automating configuration with Ansible?_
      Ansible lets you quickly and easily automate configuration for all the machines in your enterprise. You accomplish this by writing and running a playbook. When you run a playbook from your control machine, Ansible uses SSH to communicate with the other machines and run all the commands (tasks).

The playbook implements the following tasks:
- Install docker.io
 
    - name: Install docker.io
      apt:
        update_cache: yes
        force_apt_get: yes
        name: docker.io
        state: present

- Install python3-pip

    1. - name: Install python3-pip
      2. apt:
        3. force_apt_get: yes
        4. name: python3-pip
        5. state: present

- Install docker module

    1. - name: Install Docker module
      2. pip:
        3. name: docker
        4. state: present

- Increase virtual memory

       '''name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: '262144'
        state: present
        reload: yes'''

- Download, launch and expose the elk container

    '''- name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        
        published_ports:
          -  5601:5601
          -  9200:9200
          -  5044:5044'''

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

https://github.com/itscarlosm/Elk_Stack_Project_1_Repository/blob/main/Ansible/docker%20ps%20outpout.png

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- ElkVM (10.1.0.4)

We have installed the following Beats on these machines:
- Filebeat and Metricbeat

These Beats allow us to collect the following information from each machine:
- Filebeat collects log events and forwards and centralizes log data.
- Metricbeat collects metrics from the operating system and from services running on the server.  

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the <name>-playbook.yml file to /etc/ansible/roles directory.
- Update the <name>-config.yml file to include the Private IP address of your webservers or ElkVM to specify which machine(s) to install on.
- Run the playbook, and navigate to Kibana (http://<YourElkVMPublicIP>:5601/app/Kibana) to check that the installation worked as expected.


You will use the following commands to run and download the playbook:
- ssh <username>@JumpBoxPrivateIP
- sudo docker container list -a
- sudo docker start <container name>
- sudo docker attach <container name>
- cd /etc/ansible/ 
- ansible-playbook install-elk.yml (configure and provision ElkVM)
- cd /etc/ansible/roles
- ansible-playbook <playbook name>-playbook.yml (filebeat or metricbeat)
- in a web browser tab navigate to the Kibana web page using the following URL template - http://<YourElkVMPublicIP>:5601/app/Kibana
  - if successful, you should be able to view your system log data and docker metric log data.
