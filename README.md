# How To Deploy a Static HTML Website with Ansible on AWS Ubuntu
Ansible is an open-source configuration management and application deployment tool. It helps to reduce managerial overhead by automating the deployment of the app and managing IT infrastructure.

## Step 1: Go to EC2 in your AWS Console.
![Step 1:](./steps-images/1.png)
---
## Step 2: Create 4 instances (one for your main Ansible machine and 3 for your servers that you want to be automated).
![Step 2:](./steps-images/2.png)
![Step 2.1:](./steps-images/3.png)
![Step 2.2:](./steps-images/4.png)
![Step 2.3:](./steps-images/0.png)
---
## Step 3: Connect to your instaces.
![Step 3:](./steps-images/5.png)
- **Download your SSH key**
![Step 3.1:](./steps-images/6.png)
- **Ensure that your key is not publicly available by modifying its permissions**
```bash
> chmod 400 "SSHkey".pem
```
![Step 3.2:](./steps-images/7.png)
- **Connect through your instances using SSH**
```bash
> ssh -i "SSHkey"" ubuntu@ec2-13-250-103-32.ap-southeast-1.compute.amazonaws.com
```
![Step 3.3:](./steps-images/8.png)
---
## Step 4: In your main Ansible machine, update the system and install needed dependencies.
```bash
> sudo apt update
```
![Step 4:](./steps-images/others-steps/1.png)
```bash
> sudo apt install software-properties-common
```
![Step 4.2:](./steps-images/others-steps/2.png)
```bash
> sudo apt-add-repository --yes --update ppa:ansible/ansible
```
![Step 4.3:](./steps-images/others-steps/3.png)
---
## Step 5: Install Ansible in your main machine and check its version for verification.
```bash
> sudo apt install ansible -y
```
![Step 5:](./steps-images/others-steps/4.png)
```bash
> ansible --version
```
![Step 5:](./steps-images/others-steps/5.png)
---
## Step 6: Create a hosts file and input the servers' IP addresses that to be automated.
- **Grab your servers' IP addresses**
```bash
sudo apt install net-tools
> ifconfig
```
![Step 6.3:](./steps-images/others-steps/8.png)
- **Create a directory for your hosts file**
```bash
> mkdir inventory
```
![Step 6:](./steps-images/others-steps/6.png)
- **Paste gathered IP addresses in your hosts file**
```bash
> vi ihosts
```
![Step 6.2:](./steps-images/others-steps/7.png)
![Step 6.2:](./steps-images/others-steps/9.png)
---
## Step 7: Authorize the servers' by generating SSH key.
- **From your main machine, generate SSH key**
```bash
> ssh-keygen -t rsa -b 2046
```
![Step 7:](./steps-images/others-steps/10.png)
- **Concatinate and copy the generated key**
```bash
> cd /home/ubuntu/.ssh/
> cat id_rsa.pub
```
![Step 7.2:](./steps-images/others-steps/11.png)
- **Paste copied generated key to authorized_keys in your server machines**
```bash
> cd .ssh
> vi authorized_keys
```
![Step 7.3:](./steps-images/others-steps/12.png)
![Step 7.4:](./steps-images/others-steps/13.png)
---
## Step 8: Test the servers by passing the desired group name and using ping module.
```bash
> ansible -i ./inventory/hosts ubuntu-server -m ping
```
![Step 8:](./steps-images/others-steps/14.png)
---
## Step 9: Create a playbook (.yml).
- **Create directory for your playbook**
```bash
> mkdir playbooks/
> cd playbooks/
> nvim apt.yml
```
![Step 9:](./steps-images/others-steps/15.png)
- **Paste the commands below or you can write your own**
```yml
---
- hosts: all
  become: yes
  vars:
    server_name: "{{ ansible_default_ipv4.address }}"
    document_root: /var/www
    app_root: /var/www/
  tasks:
    - name: Update apt cache and install Nginx
      apt:
        name: nginx
        state: latest
        update_cache: yes

    - name: Copy website files to the server's document root
      copy:
        src: "{{ app_root }}"
        dest: "{{ document_root }}"
        mode: preserve

    - name: Apply Nginx template
      template:
        src: /var/www/nginx.conf.j2
        dest: /etc/nginx/sites-available/default
      notify: Restart Nginx

    - name: Enable new site
      file:
        src: /etc/nginx/sites-available/default
        dest: /etc/nginx/sites-enabled/default
        state: link
      notify: Restart Nginx

    - name: Allow all access to tcp port 80
      ufw:
        rule: allow
        port: '80'
        proto: tcp

  handlers:
    - name: Restart Nginx
      service:
        name: nginx
        state: restarted
```
![Step 9.2:](./steps-images/others-steps/16.png)
---
## Step 10: Obtain and unzip demo website.
- **Download the demo website**
```bash
> cd /var/www/
> sudo curl -L https://github.com/do-community/html_demo_site/archive/refs/heads/main.zip -o html_demo.zip
```
![Step 10:](./steps-images/others-steps/others-others/1.png)
- **Download the unzip app to extract zip content**
```bash
> sudo apt install unzip -y
> unzip html_demo.zip
```
![Step 10.2:](./steps-images/others-steps/others-others/2.png)
---
## Step 11: Create a template for nginx's configuration.
```bash
> cd /var/www
> sudo vi nginx.conf.j2
```
![Step 11:](./steps-images/others-steps/others-others/3.png)
- **Paste nginx's configuration below**
```j2
server {
  listen 80;

  root {{ document_root }}/{{ app_root }};
  index index.html index.htm;

  server_name {{ server_name }};
  
  location / {
   default_type "text/html";
   try_files $uri.html $uri $uri/ =404;
  }
}
```
![Step 11.2:](./steps-images/others-steps/20.png)
---
## Step 12: Execute playbook.
```bash
> ansible-playbook -i ./inventory/hosts ./playbooks/apt.yml -u ubuntu
```
![Step 12:](./steps-images/others-steps/21.png)
*Note: ideally, your output should look the same as the image above*
---
## Step 13: Test your sites by using the public IPv4 addresses of your server machines.
- **For ansible-server1**
![Step 13:](./steps-images/others-steps/22.png)
- **For ansible-server2**
![Step 13.2:](./steps-images/others-steps/23.png)
- **For ansible-server3**
![Step 13.3:](./steps-images/others-steps/24.png)
