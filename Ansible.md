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
## Proof of concept: 

![image](https://github.com/user-attachments/assets/8af06654-7dd5-4443-ac18-e2bfddca3e89)

#### verifying if apache was install in our targets servers running in aws
![image](https://github.com/user-attachments/assets/0e62d45e-d41b-45d5-96fe-2155a51bcb9c)


Reference: [ansible.builtin.package – Generic OS package manager](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/package_module.html)


# TASK 2
## Scenario : Managing User Accounts
A company needs to create a new user named **deploy** on all their servers for deployment purposes. The user should have a specific home directory and should belong to the **deploy** group.
### Instructions:
+ Create the deploy user with a custom home directory.
+ Create the deploy group if it does not exist.
+ Ensure the deploy user is added to the deploy group
___________________________________________________________________________________________________________________________________________________________________

# Solution
From the instruction, we are going to create an ansible playbook to automate the creation of the **deploy user** with a custom home directory and the **deploy group** on all servers.The playbook will handle creating the group if it doesn't exist, setting up the user, and adding the user to the group.

1. Create the Playbook and save the content into a file **deploy_user_setup.yml**
```
---
- name: Manage deploy user and group
  hosts: all
  become: yes

  tasks:
    - name: Ensure the deploy group exists
      group:
        name: deploy
        state: present

    - name: Create the deploy user with a custom home directory
      user:
        name: deploy
        home: /custom/home/deploy
        shell: /bin/bash
        group: deploy
        create_home: yes
        state: present
        comment: "Deployment User"

    - name: Ensure the deploy user is part of the deploy group
      user:
        name: deploy
        groups: deploy
        append: yes
```

2. Run the playbook using the following command:

```
ansible-playbook -i inventory deploy_user_setup.yml

# where inventory is the path that list all your targets servers.
```

Reference: [ansible.builtin.user – Manage user accounts](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/user_module.html)





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
We are going create ansible playbook to automate the deployment of the website from a Git repository to a server. The playbook will handle cloning the repository, setting the correct file permissions, and ensuring the web server can serve the files as required.

1. Create the playbook and save the content into a file **deploy_static_website.yml**
```
---
- name: Deploy static website
  hosts: all
  become: yes

  vars:
    repo_url: https://github.com/example/static-website.git
    web_root: /var/www/html

  tasks:
    - name: Install Git
      package:
        name: git
        state: present

    - name: Clone the website repository
      git:
        repo: "{{ repo_url }}"
        dest: "{{ web_root }}"
        version: main
        force: yes

    - name: Set correct file permissions for web server
      file:
        path: "{{ web_root }}"
        state: directory
        owner: apache
        group: apache
        mode: '0755'
        recurse: yes

    - name: Ensure Apache is running
      service:
        name: httpd
        state: started
        enabled: yes
```
2. Run the playbook using the following command:

```
ansible-playbook -i inventory deploy_static_website.yml

# where inventory is the path that list all your targets servers.
```


# TASK 4

## Scenario : Setting Up a Basic Firewall
A small business needs to secure its web server by only allowing traffic on HTTP, HTTPS, and SSH ports. You’ll use firewalld to accomplish this task.
### Requirements:
+ Install firewalld.
+ Configure firewalld to allow traffic on ports 22, 80, and 443.
+ Ensure all other ports are blocked.
___________________________________________________________________________________________________________________________________________________________________

# Solution

To secure the web server using firewalld, you can automate the installation and configuration with an Ansible playbook. This playbook will install firewalld, configure it to allow traffic on ports 22 (SSH), 80 (HTTP), and 443 (HTTPS), and block all other ports.

## 1. Create a playbook name **firewall_setup.yml**
```
 ---
- name: Set up basic firewall using firewalld
  hosts: all
  become: yes

  tasks:
    - name: Install firewalld
      package:
        name: firewalld
        state: present

    - name: Start and enable firewalld service
      service:
        name: firewalld
        state: started
        enabled: yes

    - name: Allow SSH (port 22)
      firewalld:
        service: ssh
        state: enabled
        immediate: yes
        permanent: yes

    - name: Allow HTTP (port 80)
      firewalld:
        service: http
        state: enabled
        immediate: yes
        permanent: yes

    - name: Allow HTTPS (port 443)
      firewalld:
        service: https
        state: enabled
        immediate: yes
        permanent: yes

    - name: Block all other traffic
      firewalld:
        state: default
        immediate: yes
        permanent: yes
```

## 2. Run the playbook using the following command:
```
ansible-playbook -i inventory firewall_setup.yml

# where inventory is the path that list all your targets servers.
```

Refernece: [ansible.posix.firewalld – Manage firewalld zones and rules](https://docs.ansible.com/ansible/latest/collections/ansible/posix/firewalld_module.html)



# TASK 5

## Scenario : Creating and Managing Directories
A development team needs specific directories set up on all their servers for storing logs and temporary files. Your task is to create these directories with the correct permissions and ownership.
### Requirements:
+ Create the /var/log/app_logs directory with appropriate permissions.
+ Create the /tmp/app_temp directory with appropriate permissions.
+ Ensure these directories are owned by the appuser user and appgroup group.
____________________________________________________________________________________________________________________________________________________________________
# Solution

In this task, we will use ansible playbook to automate the creation and management of the required directories with the correct permissions and ownership. The playbook will create the directories **/var/log/app_logs** and **/tmp/app_temp**, set the appropriate permissions, and ensure the directories are owned by the appuser user and appgroup group.

1. Create the playbook name **directory_setup.yml**.
   
```
---
- name: Create and manage directories for the development team
  hosts: all
  become: yes

  tasks:
    - name: Ensure appuser and appgroup exist
      user:
        name: appuser
        state: present
        group: appgroup

    - name: Create /var/log/app_logs directory
      file:
        path: /var/log/app_logs
        state: directory
        mode: '0755'
        owner: appuser
        group: appgroup

    - name: Create /tmp/app_temp directory
      file:
        path: /tmp/app_temp
        state: directory
        mode: '0755'
        owner: appuser
        group: appgroup
```

3. Run the Playbook: Execute the playbook using the following command:

```
ansible-playbook -i inventory directory_setup.yml

# where inventory is the path that list all your targets servers.
```

Reference: [ansible.builtin.file – Manage files and file properties](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/file_module.html)


  
