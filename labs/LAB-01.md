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

## Lab Steps
### 1. Create Virtual Machine
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

### 2. Install IIS Web Server
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
### 3.Edited file at C:\inetpub\wwwroot\index.html with content:
Welcome to Azure IIS on your xyz-server 🚀 to
"This is my Lab 01 proof-of-work"

Screenshot- Edited IIS html page
<img width="1919" height="1010" alt="Screenshot 2025-09-22 020301" src="https://github.com/user-attachments/assets/078373f4-c181-4118-9b25-dc67a1b7629c" />


### 4. Verify Deployment
Opened browser → http://<Public-IP>
Confirmed IIS custom page displayed




