# Ansible-Playbooks


---
- name: installing httpd
  hosts: webservers
  become: true
  tasks: 
  - name: installing httpd package 
    yum: 
      name: httpd
      state: installed
    notify: start httpd service

  handlers:   
  - name: start httpd service
    service:
      name: httpd
      state: started


-------------------

---
- name: playbook to create directory
  hosts: webservers
  become: true
  tasks:
  - name: creating a file
    file: 
      path: /opt/test.txt
      state: touch 
      mode: 0777
      owner: ansadm
-----------------------------

---
#- name: Playbook to copy a file
- hosts: all
  tasks: 
#  - name: copy a file 
  - copy: src=/opt/playbooks/demo.txt dest=/home/ansadmin 
---------------------------------------------

---
- hosts: all
  become: true
  vars_files:
    - user.yml
  tasks:
  - name: creating user
    user: 
      name: "{{ name }}"
-----------------------------------------------



Tomcat installation on EC2 instance
Prerequisites
EC2 instance
Install Apache Tomcat
Install Java

   yum install java
Download tomcat packages from https://tomcat.apache.org/download-80.cgi onto /opt on EC2 instance

  # create tomcat directory
  cd /opt
  wget http://mirrors.fibergrid.in/apache/tomcat/tomcat-8/v8.5.35/bin/apache-tomcat-8.5.35.tar.gz
  tar -xvzf /opt/apache-tomcat-8.5.35.tar.gz
give executing permissions to startup.sh and shutdown.sh which are under bin.

   chmod +x /opt/apache-tomcat-8.5.35/bin/startup.sh shutdown.sh
start the tomcat services

  cd /opt/apache-tomcat-8.5.35/bin
  ./startup.sh

check point :
access tomcat application from browser on prot 8080 http://<Public_IP>:8080

-----------------------

[webservers]
172.31.22.214
172.31.30.15
172.31.16.198

[appservers]
172.31.16.198
172.31.30.15

[dbservers]
172.31.22.217

----------------------------------------

On RHEL 8.x server
Install Python latest version (on Control node and Managed host)

yum install python3 -y
By default, python3 is the command to run python commands. to use just python, use "alternatives" command. (on Control node and Managed host)

alternatives --set python /usr/bin/python3
Check for Python version

python --version
Install python-pip package manager (on Control node)

yum -y install python3-pip
Create a new user for ansible administration & grant admin access to the user (on Control node and Managed host)

useradd ansadmin
passwd ansadmin
Below command adds ansadmin to sudoers file. But we strongly recommended using "visudo" command if you are aware vi or nano editor. (on Control node and Managed host)

echo "ansadmin ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
Using key-based authentication is advised. If you are still at the learning stage use password-based authentication (on Control node and Managed host)

# sed command replaces "PasswordAuthentication no to yes" without editing file 
 sed -ie 's/PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config
 sudo service sshd reload
Install Ansible as a ansadmin user (on Control node)
su - ansadmin
pip3 install ansible --user
Note: Ansible must be installed as a user (here ansadmin)

check for ansible version

ansible --version
Log in as a ansadmin user on master and generate ssh key (on Control node)

ssh-keygen
Copy keys onto all ansible managed hosts (on Control node)

ssh-copy-id ansadmin@<target-server>
Validation test
Create a directory /etc/ansible and create an inventory file called "hosts" add control node IP address in it.

Run ansible command as ansadmin user it should be successful (Master)

ansible all -m ping

---------------------------------

---
- name: installing httpd
  hosts: webservers
  become: true
  tasks: 
  - name: stopping httpd service
    service:
      name: httpd
      state: stopped

  - name: removing httpd package
    yum:
      name: httpd
      state: removed 
