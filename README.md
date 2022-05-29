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

