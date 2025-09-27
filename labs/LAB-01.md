# LAB-01: Deploying and Managing Virtual Servers on Azure

---

## Overview

This lab focuses on deploying virtual servers on Azure, configuring them with web servers, and verifying their functionality.  
In this step, we set up a **Windows Server 2022 VM (`xyz-server`)** with IIS, customized its homepage, and validated the deployment.

---

## Objectives

- Create a Windows Server 2022 virtual machine
- Install and configure IIS web server
- Customize the IIS default site
- Verify deployment with browser access

---

## Prerequisites

- Active Azure subscription (Free Tier or Paid)
- Access to Azure Portal
- Basic knowledge of Windows Server administration

---

## Lab Steps

### Step 1: VM with Windows Server

#### 1.1 Create Virtual Machine

1. Logged into [Azure Portal](https://portal.azure.com)
2. Navigated to **Virtual Machines → + Create**
3. Configured VM with:
    - **Name**: `xyz-server`
    - **Image**: *Windows Server 2022 Datacenter*
    - **Size**: *Standard_D2s_v3*
    - **Authentication**: Password
    - **Inbound Ports**: RDP (3389)
4. Clicked **Review + Create** → Deployed successfully
5. Screenshot:  
   <img width="1919" height="906" alt="Screenshot 2025-09-22 005735" src="https://github.com/user-attachments/assets/378da164-dd25-4ee1-b28c-d181d995a796" />

---

#### 1.2 Install IIS Web Server

- Connected via RDP and ran:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

- Screenshot: Installing IIS  
  <img width="1919" height="1003" alt="Screenshot 2025-09-22 010506" src="https://github.com/user-attachments/assets/6e25fb12-a166-42e3-9896-56c3ec1da366" />

- Screenshot: Server Manager  
  <img width="1919" height="970" alt="Screenshot 2025-09-22 011831" src="https://github.com/user-attachments/assets/12efc964-7115-4545-9848-e734653a8f58" />

- Screenshot: IIS Server Default page  
  <img width="1919" height="1013" alt="Screenshot 2025-09-22 012310" src="https://github.com/user-attachments/assets/3350014d-b595-4e16-8045-0037d53890b5" />

---

#### 1.3 Edit IIS Default Page

- Edited file at `C:\inetpub\wwwroot\index.html` with content:
    - Welcome to Azure IIS on your xyz-server 🚀 to  
    - "This is my Lab 01 proof-of-work"

- Screenshot: Edited IIS HTML page  
  <img width="1919" height="1010" alt="Screenshot 2025-09-22 020301" src="https://github.com/user-attachments/assets/078373f4-c181-4118-9b25-dc67a1b7629c" />

---

#### 1.4 Verify Deployment

- Opened browser → `http://<Public-IP>`
- Confirmed IIS custom page displayed

---

### Step 2: Ubuntu Server with Apache

#### 2.1 Create VM

- **Name**: Ubuntu-Lab1
- **Image**: Ubuntu 22.04 LTS
- **Size**: Standard_B1s
- **Authentication**: SSH Key or Password
- **Inbound Ports**: SSH (22), HTTP (80)

- Screenshot:  
  <img width="1919" height="901" alt="Screenshot 2025-09-24 020936" src="https://github.com/user-attachments/assets/fd4e0b78-2615-497a-8934-6cad5f7e90c1" />

---

#### 2.2 Connect via SSH

- `ssh azureuser@<Public-IP>`

---

#### 2.3 Install Apache

- `sudo apt update`
- `sudo apt install apache2 -y`

- Screenshot:  
  <img width="1382" height="919" alt="Screenshot 2025-09-24 022322" src="https://github.com/user-attachments/assets/ad49ff5b-8629-43e0-8ad3-899da6582a44" />

---

#### 2.4 Create Custom Site Directory and Index File

- Screenshot: Default site  
  <img width="1919" height="1019" alt="Screenshot 2025-09-24 022343" src="https://github.com/user-attachments/assets/1a1e5cf2-c3ba-43c9-9ded-bd74bdb933e5" />

- Commands:
    - `sudo mkdir /var/www/gci`
    - `cd /var/www/gci`
    - `sudo nano index.html`

- Example Content:
    ```html
    <h1>Ubuntu Rocks! 🚀</h1>
    <p>Running on Apache Web Server inside Azure</p>
    ```

- Screenshot: Updated  
  <img width="1919" height="858" alt="Screenshot 2025-09-24 023012" src="https://github.com/user-attachments/assets/3fb6c605-757b-4fa2-917f-2248dce2296d" />

---

#### 2.5 Configure VirtualHost

- `sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/gci.conf`
- `sudo nano /etc/apache2/sites-available/gci.conf`

---

#### 2.6 Key Config Changes

- `DocumentRoot /var/www/gci`
- `ServerName gci.example.com`

---

#### 2.7 Disable Default Site & Enable Custom Site

- `sudo a2dissite 000-default.conf`
- `sudo a2ensite gci.conf`
- `sudo systemctl reload apache2`

- Verified in browser → `http://<Public-IP>` → Custom page displayed

---

### Step 3: Create VM Images (Snapshots)

#### Procedure

1. Navigated to **Azure Portal → Virtual Machines**
2. Selected each VM (`Wmserver-Lab1` and `Ubuntu-Lab1`)
3. From the top menu, clicked **Capture**
4. Entered image names:
    - `WinServerLab01-Image`
    - `UbuntuLab01-Image`
5. Saved images to the chosen resource group for future redeployment

---

#### Screenshots

- Windows Server:  
  <img width="1897" height="873" alt="Screenshot 2025-09-27 011422" src="https://github.com/user-attachments/assets/5e97914b-ec4f-47de-acd1-22977eb7a5fa" />

- Ubuntu Server:  
  <img width="1915" height="801" alt="Screenshot 2025-09-27 010954" src="https://github.com/user-attachments/assets/66a0e3a2-68c8-4400-8d7e-35d5bd82c1ab" />

---

### Step 4: Delete Original VMs

#### Procedure

1. After creating images, went back to **Azure Portal → Virtual Machines**
2. Selected both:
    - `Wmserver-Lab1`
    - `Ubuntu-Lab1`
3. Chose **Delete** to remove the original instances
4. Confirmed deletion → Ensured that images remained safe in the resource group

---

#### Screenshots

- Windows VM Deleted & Ubuntu VM Deleted  
  <img width="1893" height="871" alt="Screenshot 2025-09-27 012320" src="https://github.com/user-attachments/assets/eca86e33-5447-4d02-b33a-dcf001679f48" />

---

### Step 5: Recreate VMs from Images

1. Successfully recreated Ubuntu VM from UbuntuLab01-Image → Apache configuration persisted
    - Screenshot:  
      <img width="1736" height="823" alt="Screenshot 2025-09-27 104734" src="https://github.com/user-attachments/assets/96ea901c-f8f4-4375-a7d1-cef6d410af5a" />

2. Tested the new Restored VM with same config
    - Screenshot (a):  
      <img width="912" height="621" alt="Screenshot 2025-09-27 111028" src="https://github.com/user-attachments/assets/c4c760f0-aa4b-427e-88c1-5238315ed018" />

    - Screenshot (b):  
      <img width="1310" height="462" alt="Screenshot 2025-09-27 112236" src="https://github.com/user-attachments/assets/35417630-b901-4c87-975f-a74b0388185d" />

3. Windows Server redeployment failed with error:  
   **OSProvisioningClientError:** OS Provisioning did not finish in the allotted time.

    - Screenshot:  
      <img width="1223" height="865" alt="Screenshot 2025-09-27 105738" src="https://github.com/user-attachments/assets/6a7b16b9-e240-4c94-812a-b2b93e7119a2" />

    - This suggests the guest OS has not been properly prepared for use as a VM image.  
      **Cause:** Windows VM was captured without generalization (Sysprep).  
      **Fix:** Prepare VM with Sysprep before capturing → then recreate from image.

---

### Step 6: Attach & Configure Extra 10 GiB Disk (Ubuntu-Lab1)

- Attached a 10 GiB disk from Azure Portal → VM → Disks

    - Screenshot:  
      <img width="1919" height="899" alt="Screenshot 2025-09-27 122425" src="https://github.com/user-attachments/assets/433d01cf-4182-4c7d-87f3-1293164afd64" />

- Verified with: `sudo fdisk -l`
- Output confirmed `/dev/sdc`
- Partitioned the disk:
    - `sudo fdisk /dev/sdc` → Created `/dev/sdc1`
- Formatted the partition:
    - `sudo mkfs.ext4 /dev/sdc1`
- Mounted it:
    - `sudo mkdir /mnt/newdisk`
    - `sudo mount /dev/sdc1 /mnt/newdisk`
- Verified: `df -h`
- Output showed `/dev/sdc1` mounted at `/mnt/newdisk`

    - Screenshot:  
      <img width="1217" height="848" alt="Screenshot 2025-09-27 122117" src="https://github.com/user-attachments/assets/820d63e4-0ae9-4bad-888b-6d1a772df2fa" />

---
