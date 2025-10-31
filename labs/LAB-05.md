# LAB-05: Implementing Global Load Balancing using Azure Traffic Manager

## 🌐 Overview
This lab demonstrates how to implement global load balancing in Microsoft Azure using **Traffic Manager**.  
It involves deploying two web applications across different regions, configuring Traffic Manager for DNS-based routing, and validating regional failover behavior.

---

## 🎯 Objectives
- Deploy two Azure Web Apps in different regions
- Configure Traffic Manager with Priority routing
- Validate traffic failover and performance routing
- Understand multi-region web hosting architecture

---

## 🧩 Prerequisites
- Azure Subscription (Student or Free Tier)
- Basic knowledge of Web Apps and Azure Networking
- Azure CLI / Cloud Shell Access

---

## 🧠 Concept Summary
Azure **Traffic Manager** provides DNS-based global load balancing.  
It distributes user traffic across multiple endpoints based on the configured routing method (Priority, Performance, Weighted, or Geographic).  
In this lab, the **Priority method** is used to achieve high availability through regional failover.

---

## 🧭 Step-by-Step Implementation

### **Step 1: Create Resource Group**
```bash
az group create --name Lab5-AppService-RG --location eastasia
```
Step 2: Create App Service Plans
Create App Service Plans for each region:

bash
Copy code
```
az appservice plan create \
  --name AppServicePlanEA \
  --resource-group Lab5-AppService-RG \
  --location eastasia \
  --sku B1
```
```
az appservice plan create \
  --name AppServicePlanSEA \
  --resource-group Lab5-AppService-RG \
  --location southeastasia \
  --sku B1
```
Step 3: Create Web Apps
bash
Copy code
# East Asia App
az webapp create \
  --resource-group Lab5-AppService-RG \
  --plan AppServicePlanEA \
  --name myWebApp01

# Southeast Asia App
az webapp create \
  --resource-group Lab5-AppService-RG \
  --plan AppServicePlanSEA \
  --name myWebAppSEA
💡 Screenshot: <img width="1918" height="907" alt="Screenshot 2025-10-31 004207" src="https://github.com/user-attachments/assets/43f01911-cf00-4467-8b38-b0bb61e4073d" />


Step 4: Deploy Simple HTML Pages
Use basic HTML content to distinguish both apps:

bash
Copy code
# Web App 1
echo "<h1>This is East Asia Web App #1</h1>" > index.html
zip site1.zip index.html
az webapp deploy \
  --resource-group Lab5-AppService-RG \
  --name myWebApp01 \
  --src-path site1.zip \
  --type zip

# Web App 2
echo "<h1>This is Southeast Asia Web App #2</h1>" > index.html
zip site2.zip index.html
az webapp deploy \
  --resource-group Lab5-AppService-RG \
  --name myWebAppSEA \
  --src-path site2.zip \
  --type zip
✅ Verify both apps:

arduino
Copy code
https://mywebapp01.azurewebsites.net
https://mywebappsea.azurewebsites.net
💡 Screenshot: <img width="1919" height="903" alt="Screenshot 2025-10-31 004229" src="https://github.com/user-attachments/assets/aeea47d7-898b-44dc-9e64-a10dd6ad9d18" />

<img width="1919" height="969" alt="Screenshot 2025-10-31 173112" src="https://github.com/user-attachments/assets/dd39ecc6-3dbc-4346-b46c-42bc556ef696" />

<img width="1917" height="811" alt="Screenshot 2025-10-31 182853" src="https://github.com/user-attachments/assets/88ab3ac7-ddbb-4f96-a276-cf03ab58a809" />


Step 5: Create Traffic Manager Profile (Portal)
Go to Traffic Manager Profiles → Create

Name: MyTrafficProfile

Routing Method: Priority

DNS Name: rajtrafficlab5 (e.g., rajtrafficlab5.trafficmanager.net)

Monitor Path: /

Port: 80

Protocol: HTTP

Click Create

💡 Screenshot: <img width="1919" height="827" alt="Screenshot 2025-10-31 173459" src="https://github.com/user-attachments/assets/c6945455-3f8b-4d48-b7db-7d788285b99d" />


Step 6: Add Endpoints
Add both web apps:

Open the Traffic Manager profile

Go to Settings → Endpoints → + Add

Type: Azure endpoint

Target: myWebApp01

Priority: 1

Repeat for myWebAppSEA with Priority: 2

💡 Screenshot: <img width="1919" height="750" alt="Screenshot 2025-10-31 182440" src="https://github.com/user-attachments/assets/852476b6-907c-4507-8b39-387a49998de0" />

Step 7: Verify and Test
Copy the DNS name from overview:
rajtrafficlab5.trafficmanager.net

Open it in the browser → should load myWebApp01

Stop the East Asia app (myWebApp01) in the portal

Refresh Traffic Manager URL after ~30 seconds → should load myWebAppSEA

Start the primary app again after validation


⚙️ Troubleshooting & Notes
Issue	Root Cause	Fix
RequestDisallowedByAzure	Region restriction policy under Student subscription	Deploy both apps in allowed regions like East Asia & Southeast Asia
GeomasterClientException	Both endpoints were in the same region	Use two distinct regions
Runtime not supported	Region doesn’t support .NET runtime	Use Node (`NODE
You do not have permission to view this directory	Missing index.html in /site/wwwroot/	Upload via Kudu or deploy a zipped site
