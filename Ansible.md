# TASK 1
## Scenario : Installing and Starting a Web Server
Imagine you need to set up a basic web server on a group of servers in your environment. The company uses Apache as their web server software, and your task is to automate its installation and configuration.
### Instructions:
+ Install Apache on all target servers.
+ Start the Apache service.
+ Ensure that Apache is enabled to start on boot.
___________________________________________________________________________________________________________________________________________________________________

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


# TASK 2
## Scenario : Managing User Accounts
A company needs to create a new user named deploy on all their servers for deployment purposes. The user should have a specific home directory and should belong to the deploy group.
### Instructions:
+ Create the deploy user with a custom home directory.
+ Create the deploy group if it does not exist.
+ Ensure the deploy user is added to the deploy group
___________________________________________________________________________________________________________________________________________________________________

# Solution



# TASK 3
## Scenario : Deploying a Static Website
Your team needs to deploy a static HTML website to a server. The website files are stored in a Git repository, and your task is to automate the deployment process.
### Instructions:
+ Clone the website repository to the /var/www/html directory on the
target server.
+ Ensure the correct file permissions are set for the web server to serve
the files.
+ Website repository https://github.com/example/static-website.git
___________________________________________________________________________________________________________________________________________________________________

# Solution



# TASK 4

## Scenario : Setting Up a Basic Firewall
A small business needs to secure its web server by only allowing traffic on HTTP, HTTPS, and SSH ports. You’ll use firewalld to accomplish this task.
### Requirements:
+ Install firewalld.
+ Configure firewalld to allow traffic on ports 22, 80, and 443.
+ Ensure all other ports are blocked.
___________________________________________________________________________________________________________________________________________________________________

# Solution


# TASK 5

## Scenario : Creating and Managing Directories
A development team needs specific directories set up on all their servers for storing logs and temporary files. Your task is to create these directories with the correct permissions and ownership.
### Requirements:
+ Create the /var/log/app_logs directory with appropriate
permissions.
+ Create the /tmp/app_temp directory with appropriate permissions.
+ Ensure these directories are owned by the appuser user and appgroup group.
____________________________________________________________________________________________________________________________________________________________________
# Solution


  
