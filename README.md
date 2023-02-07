# How To Deploy a Static HTML Website with Ansible on AWS Ubuntu
Ansible is an open-source configuration management and application deployment tool. It helps to reduce managerial overhead by automating the deployment of the app and managing IT infrastructure.

## Step 1: Go to EC2 in your AWS Console.
![Step 1:](./steps-images/1.png)
---
## Step 2: Create 4 instances (one for your main Ansible machine and 3 for your servers that you want to be automated).
![Step 2:](./steps-images/2.png)
![Step 2.1:](./steps-images/3.png)
![Step 2.2:](./steps-images/4.png)
---
## Step 3: Connect to your instaces.
![Step 3:](./steps-images/5.png)
- **Download your SSH key**
![Step 3.1:](./steps-images/6.png)
- **Ensure that your key is not publicly available by modifying its permissions**
```bash
chmod 400 "SSHkey".pem
```
![Step 3.2:](./steps-images/7.png)
- **Connect through your instances using SSH**
```bash
ssh -i "SSHkey"" ubuntu@ec2-13-250-103-32.ap-southeast-1.compute.amazonaws.com
```
![Step 3.3:](./steps-images/8.png)
---
## Step 4: In your main Ansible machine, update the system and install needed dependencies.
```bash
sudo apt update
```
![Step 4:](./steps-images/others-steps/1.png)
```bash
sudo apt install software-properties-common
```
![Step 4.2:](./steps-images/others-steps/2.png)
```bash
sudo apt-add-repository --yes --update ppa:ansible/ansible
```
![Step 4.3:](./steps-images/others-steps/3.png)
## Step 5: Install Ansible in your main machine and check its version for verification.
```bash
sudo apt install ansible -y
```
![Step 5:](./steps-images/others-steps/4.png)
```bash
ansible --version
```
![Step 5:](./steps-images/others-steps/5.png)
## Step 6: Create a hosts file and input the servers' IP addresses that to be automated.
- **Grab your servers' IP addresses**
```bash
sudo apt install net-tools
ifconfig
```
![Step 6.3:](./steps-images/others-steps/8.png)
- **Create a directory for your hosts file**
```bash
mkdir inventory
```
![Step 6:](./steps-images/others-steps/6.png)
- **Paste gathered IP addresses in your hosts file**
```bash
vi ihosts
```
![Step 6.2:](./steps-images/others-steps/7.png)
![Step 6.2:](./steps-images/others-steps/9.png)
