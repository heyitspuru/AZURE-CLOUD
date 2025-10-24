# LAB-04: Azure Load Balancing & Traffic Management

## 🧭 Overview
This lab demonstrates how to distribute, manage, and optimize network traffic in Azure.  
You will configure and test multiple Azure Load Balancer types, deploy backend VMs, and implement higher-level traffic routing solutions such as **Traffic Manager** and **Application Gateway**.

## 🎯 Objectives
- Configure **Public**, **Private**, and **Zone-Redundant** Azure Load Balancers  
- Create **Backend Pools**, **Health Probes**, and **Load-Balancing Rules**  
- Deploy and test **Traffic Manager** profiles  
- Implement **Azure Application Gateway** with backend web servers  
- Understand **Gateway Load Balancer** and integration with network virtual appliances  

## ⚙️ Prerequisites
- Active Azure subscription  
- Two running Windows Server VMs (`myVM1`, `myVM2`) in an Availability Set  
- VNet named `myVNet`  
- PowerShell or Azure CLI access  

---

## 🧩 Task 1 — Create a Public Azure Load Balancer

### Steps
1. Navigate to **Azure Portal → Load Balancer → Create**  
2. Configure:
   - **Resource Group:** `LBresourcegroup`  
   - **Name:** `myLoadBalancer`  
   - **Region:** East US  
   - **Type:** Public  
   - **SKU:** Basic  
   - **Public IP Address:** create new → `myPublicIP`
3. Click **Review + Create → Create**

📷 *<img width="1919" height="873" alt="Screenshot 2025-10-24 174201" src="https://github.com/user-attachments/assets/cba7898c-0b5b-4030-b358-882d413b089b" />
* Load Balancer overview & Public IP association.

---

## 🧩 Task 2 — Create a Virtual Network
1. Go to **Create a resource → Networking → Virtual Network**  
2. Configure:
   - **Name:** `myVNet`
   - **Region:** East US  
   - **Address space:** `10.0.0.0/16`
   - **Subnet:** `mySubnet` → `10.0.0.0/24`
3. Review + Create → Confirm deployment.
📷 *<img width="1919" height="873" alt="Screenshot 2025-10-24 174201" src="https://github.com/user-attachments/assets/807f9348-9738-4868-af2e-076de9d18a7b" />
*
---

## 🧩 Task 3 — Create a Backend Pool
1. Open **myLoadBalancer → Settings → Backend Pools → Add**  
2. Name: `myBackendPool`  
3. Associate to `myVNet`, choose *Virtual machines*, and leave VMs unassigned for now.  
4. Click **Add**.
📷 *<img width="1919" height="873" alt="Screenshot 2025-10-24 174201" src="https://github.com/user-attachments/assets/eee6172a-6970-4fbf-89be-492e92bc2177" />
*
---

## 🧩 Task 4 — Create a Health Probe
1. Inside **myLoadBalancer → Settings → Health Probes → Add**  
2. Configure:
   - **Name:** `myHealthProbe`  
   - **Protocol:** TCP  
   - **Port:** 80  
   - **Interval:** 5 seconds  
   - **Unhealthy threshold:** 2  
3. Save changes.
📷 *<img width="1919" height="873" alt="Screenshot 2025-10-24 174201" src="https://github.com/user-attachments/assets/82f5eaa5-d6e9-4b3a-ba63-1c5d33a37aff" />
*
---

## 🧩 Task 5 — Create a Load Balancer Rule
1. **myLoadBalancer → Settings → Load Balancing Rules → Add**  
2. Parameters:
   - **Name:** `HTTP-Rule`  
   - **Frontend IP:** `myPublicIP`  
   - **Backend pool:** `myBackendPool`  
   - **Protocol:** TCP  
   - **Port:** 80  
   - **Backend port:** 80  
   - **Health probe:** `myHealthProbe`  
3. Click **OK / Save**.
📷 *<img width="1919" height="873" alt="Screenshot 2025-10-24 174201" src="https://github.com/user-attachments/assets/18b65cf2-59ec-449a-823b-35ef5438a478" />
*
---

## 🧩 Task 6 — Create Two Windows VMs
1. Create `myVM1` and `myVM2` in an **Availability Set** under `myVNet`.  
2. OS: Windows Server 2019 Datacenter  
3. Disable public IP; RDP via Bastion if needed.  
4. Confirm both appear under your resource group.

📷 *Screenshot:* Availability Set & VM overview.

---

## 🧩 Task 7 — Install IIS for Testing
Run in **Azure Cloud Shell (PowerShell):**
```powershell
Set-AzVMExtension `
  -ResourceGroupName LBresourcegroup `
  -ExtensionName IIS `
  -VMName myVM1 `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.4 `
  -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
  -Location EastUS
```
Repeat for myVM2.

📷 Screenshot: IIS installed successfully on both VMs.

---

## 🧩 Task 8 — Add VMs to Backend Pool
Go to myLoadBalancer → Backend Pools → myBackendPool

Select:

- Virtual Network: myVNet
- Associated to: Virtual Machines

Click + Add → Select myVM1 and myVM2 → Save.

---

## 🧩 Task 9 — Test the Load Balancer
- Copy the Public IP of myLoadBalancer.
- Open a browser and enter the IP.
- Refresh repeatedly — responses should alternate between myVM1 and myVM2.

📷 Screenshot: Browser responses showing both VM names.

---

## 🧩 Task 10 — Create an Internal Load Balancer
Repeat creation, but choose:

- Type: Private
- Frontend IP Configuration: Private (from myVNet)

Add internal backend VMs and rules on port 80.

---

## 🧩 Task 11 — Create a Zone-Redundant Load Balancer
- Select SKU: Standard and Region supporting Availability Zones (e.g., East US 2).
- Assign frontend IPs across zones 1–3.
- Add backend pool and rules as in Task 5.

📷 Screenshot: Zone redundancy diagram.

---

## 🧩 Task 12 — Implement Azure Traffic Manager
- Search Traffic Manager Profiles → Create
- Configure:
  - Name: myTrafficManager
  - Routing Method: Performance (or Geographic)
  - Endpoints: Add Public IPs of regional load balancers
- Save and test routing by accessing the DNS name.

📷 Screenshot: Endpoint status = Online.

---

## 🧩 Task 13 — Configure Azure Application Gateway

Overview

Application Gateway is a Layer 7 load balancer for HTTP/S traffic with routing rules, listeners, and backend pools.

Steps

- Create → Networking → Application Gateway
- Basics:
  - Resource Group: myResourceGroupAG
  - Name: myAppGateway
  - SKU: Standard v2
  - Virtual Network: Create new myVNet with:
    - myAGSubnet (10.0.0.0/24) for gateway
    - myBackendSubnet (10.0.1.0/24) for VMs
- Frontends: Public IP → myAGPublicIPAddress
- Backends: Add empty pool myBackendPool
- Configuration: Add routing rule:
  - Listener → myListener (Port 80)
  - Backend pool → myBackendPool
  - Backend setting → myBackendSetting (Port 80)
- Create and wait for deployment.
- Create two backend VMs (myVM, myVM2) in myBackendSubnet, install IIS.
- Associate them to myBackendPool.
- Test by browsing the Application Gateway’s Public IP.

📷 Screenshot: IIS responses from both backend VMs.

---

## 🧩 Task 14 — Gateway Load Balancer (Concept)
Integrates with Network Virtual Appliances (NVAs) for deep packet inspection.

Uses “bump-in-the-wire” topology between a Frontend Load Balancer and an NVA backend.

Ensures high-availability & transparent traffic redirection.

📷 Optional diagram: Gateway LB architecture.

---

## 🧠 Key Learnings

Concept | Summary
--- | ---
Public LB | Distributes Internet traffic to VMs
Private LB | Balances internal traffic within a VNet
Zone-Redundant LB | Ensures high availability across zones
Traffic Manager | Routes global traffic using DNS policies
Application Gateway | Provides L7 routing and SSL termination
Gateway LB | Integrates NVAs for secure inline processing

---

## 🩵 Troubleshooting Notes

Issue | Cause | Resolution
--- | --- | ---
Blank browser page | IIS not installed | Re-run Set-AzVMExtension
VMs not receiving traffic | Backend pool empty | Re-associate VMs
Health Probe unhealthy | Port blocked | Allow inbound 80 in NSG
Traffic Manager inactive | Endpoint disabled | Enable & verify DNS name

---

## ✅ Conclusion
Azure Load Balancers and Traffic Managers enable scalable, fault-tolerant application delivery.  
This lab reinforces understanding of multi-tier architectures, redundancy, and intelligent routing in Azure networking.
