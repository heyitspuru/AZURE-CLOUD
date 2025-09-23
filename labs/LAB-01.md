  # LAB-01: Deploying and Managing Virtual Servers on Azure

## Overview
This lab focuses on deploying virtual servers on Azure, configuring them with web servers, and verifying their functionality.  
In this step, we set up a **Windows Server 2022 VM (`xyz-server`)** with IIS, customized its homepage, and validated the deployment.

## Objectives
- Create a Windows Server 2022 virtual machine
- Install and configure IIS web server
- Customize the IIS default site
- Verify deployment with browser access

## Prerequisites
- Active Azure subscription (Free Tier or Paid)
- Access to Azure Portal
- Basic knowledge of Windows Server administration

### Lab Steps
### 1 VM with Windows Server
### 1.1 Create Virtual Machine
1. Logged into [Azure Portal](https://portal.azure.com)  
2. Navigated to **Virtual Machines → + Create**  
3. Configured VM with:  
   - **Name**: `xyz-server`  
   - **Image**: *Windows Server 2022 Datacenter*  
   - **Size**: *Standard_D2s_v3*  
   - **Authentication**: Password  
   - **Inbound Ports**: RDP (3389)  
4. Clicked **Review + Create** → Deployed successfully
5. Screen Shot-<img width="1919" height="906" alt="Screenshot 2025-09-22 005735" src="https://github.com/user-attachments/assets/378da164-dd25-4ee1-b28c-d181d995a796" />

### 1.2 Install IIS Web Server
Connected via RDP and ran:  
Screenshot- Installing IIS
<img width="1919" height="1003" alt="Screenshot 2025-09-22 010506" src="https://github.com/user-attachments/assets/6e25fb12-a166-42e3-9896-56c3ec1da366" />

ScreenShot- Server Manager 
<img width="1919" height="970" alt="Screenshot 2025-09-22 011831" src="https://github.com/user-attachments/assets/12efc964-7115-4545-9848-e734653a8f58" />

Screenshot-IIS Server Default page
<img width="1919" height="1013" alt="Screenshot 2025-09-22 012310" src="https://github.com/user-attachments/assets/3350014d-b595-4e16-8045-0037d53890b5" />

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```
### 1.3.Edited file at C:\inetpub\wwwroot\index.html with content:
Welcome to Azure IIS on your xyz-server 🚀 to
"This is my Lab 01 proof-of-work"

Screenshot- Edited IIS html page
<img width="1919" height="1010" alt="Screenshot 2025-09-22 020301" src="https://github.com/user-attachments/assets/078373f4-c181-4118-9b25-dc67a1b7629c" />

### 1.4. Verify Deployment
Opened browser → http://<Public-IP>
Confirmed IIS custom page displayed


### 2. Ubuntu Server with Apache
### 2.1 Created a VM with:
  - **Name**: Ubuntu-Lab1
  - **Image**: Ubuntu 22.04 LTS
  - **Size**: Standard_B1s
  - **Authentication**: SSH Key or Password
  - **Inbound Ports**: SSH (22), HTTP (80)

ScreenShot-<img width="1919" height="901" alt="Screenshot 2025-09-24 020936" src="https://github.com/user-attachments/assets/fd4e0b78-2615-497a-8934-6cad5f7e90c1" />

### 2.2 Connected via SSH:
  - ssh azureuser@<Public-IP>

### 2.3 Installed Apache:
  - sudo apt update
  - sudo apt install apache2 -y

Screenshot-<img width="1382" height="919" alt="Screenshot 2025-09-24 022322" src="https://github.com/user-attachments/assets/ad49ff5b-8629-43e0-8ad3-899da6582a44" />

### 2.4 Created custom site directory and index file:
Screenshot - default site- <img width="1919" height="1019" alt="Screenshot 2025-09-24 022343" src="https://github.com/user-attachments/assets/1a1e5cf2-c3ba-43c9-9ded-bd74bdb933e5" /> 

  - sudo mkdir /var/www/gci
  - cd /var/www/gci
  - sudo nano index.html

Example content:
<h1>Ubuntu Rocks! 🚀</h1>
<p>Running on Apache Web Server inside Azure</p>

Screenshot- Updated- <img width="1919" height="858" alt="Screenshot 2025-09-24 023012" src="https://github.com/user-attachments/assets/3fb6c605-757b-4fa2-917f-2248dce2296d" />

### 2.5 Configured VirtualHost:
  - sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/gci.conf
  - sudo nano /etc/apache2/sites-available/gci.conf

### 2.6 Key config changes:
  - DocumentRoot /var/www/gci
  - ServerName gci.example.com

### 2.7 Disabled default site & enabled custom site:
  - sudo a2dissite 000-default.conf
  - sudo a2ensite gci.conf
  - sudo systemctl reload apache2

Verified in browser → http://<Public-IP> → Custom page displayed




