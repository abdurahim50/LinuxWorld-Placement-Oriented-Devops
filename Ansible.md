# TASK 1
## Scenario : Installing and Starting a Web Server
Imagine you need to set up a basic web server on a group of servers in your environment. The company uses Apache as their web server software, and your task is to automate its installation and configuration.
### Instructions:
+ Install Apache on all target servers.
+ Start the Apache service.
+ Ensure that Apache is enabled to start on boot.
________________________________________________________________________________________________________________________________________________________________________________________________
# Solution
To automate the installation, configuration, and startup of Apache on a group of servers, you can use a configuration management tool like Ansible.
- In this task, assume our controler machine is running on virtualbox(on-premise) and the targets are running on aws.
1. Ensure that you have SSH access set up between your Ansible control machine and the target.
```
[webservers]
server1 ansible_host=192.168.1.101 ansible_user=ec2-user ansible_ssh_private_key_file=/path-to-ec2-ssh-key
server2 ansible_host=192.168.1.102 ansible_user=ec2-user ansible_ssh_private_key_file=/path-to-ec2-ssh-key
```
2. Create ansible Playbook for Apache Installation and Configuration and save the below content into a file, **apache_setup.yml**
   
```
---
- name: Install and configure Apache web server
  hosts: all
  become: yes
  
  tasks:
    - name: Install Apache
      package:
        name: httpd
        state: present

    - name: Start Apache service
      service:
        name: httpd
        state: started

    - name: Enable Apache service to start on boot
      service:
        name: httpd
        enabled: yes

    - name: Enable Apache firewalld rules
      firewalld:
        immediate: yes
        port: "80/tcp"
        permanent: yes
```
3. Execute the playbook using the following command:
```
ansible-playbook -i inventory apache_setup.yml
```
Reference: [ansible.builtin.package – Generic OS package manager](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/package_module.html)



  
