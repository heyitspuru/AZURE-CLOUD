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
📷 *<img <img width="1919" height="763" alt="Screenshot 2025-10-24 174814" src="https://github.com/user-attachments/assets/361bdf83-ee08-452d-aece-5a8ccb0771ca" />
 />
*
---

## 🧩 Task 3 — Create a Backend Pool
1. Open **myLoadBalancer → Settings → Backend Pools → Add**  
2. Name: `myBackendPool`  
3. Associate to `myVNet`, choose *Virtual machines*, and leave VMs unassigned for now.  
4. Click **Add**.
📷 *<img <img width="1915" height="746" alt="Screenshot 2025-10-24 181832" src="https://github.com/user-attachments/assets/c7fba84d-59a8-43db-a918-fb378f5b92d3" />
 />
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
📷 *<img <img width="1919" height="622" alt="Screenshot 2025-10-24 184238" src="https://github.com/user-attachments/assets/17e63034-c759-44b7-97e9-12985625487c" />
 />
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
📷 *<img <img width="1919" height="764" alt="Screenshot 2025-10-24 184533" src="https://github.com/user-attachments/assets/8359ccb8-c0d4-410f-939b-68008a044851" />
 />
*
---

## 🧩 Task 6 — Create Two Windows VMs
1. Create `myVM1` and `myVM2` in an **Availability Set** under `myVNet`.  
2. OS: Windows Server 2019 Datacenter  
3. Disable public IP; RDP via Bastion if needed.  
4. Confirm both appear under your resource group.

📷 *Screenshot:* Availability Set & VM overview.
<img width="1912" height="909" alt="Screenshot 2025-10-29 113550" src="https://github.com/user-attachments/assets/729bba7b-33e8-44a8-bbdd-a78be817d7fe" />

<img width="1919" height="782" alt="Screenshot 2025-10-29 113719" src="https://github.com/user-attachments/assets/ba37b110-ca4c-406f-86b8-1b2fa52aca2f" />

<img width="1919" height="827" alt="Screenshot 2025-10-29 113738" src="https://github.com/user-attachments/assets/5d9eb78e-03f1-4275-ac4b-34a8b8ba6773" />

---

### 🧩 Step 7 — IIS Installation & Verification

**Objective:**  
Install IIS on both backend VMs (`myVM1`, `myVM2`) and verify they respond correctly for Load Balancer testing.

---

#### 🔹 IIS Installation
IIS installed successfully on both VMs using the **Custom Script Extension**:
```
az vm extension set \
  --resource-group LBresourcegroup \
  --vm-name myVM1 \
  --name CustomScriptExtension \
  --publisher Microsoft.Compute \
  --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; Add-Content -Path C:\\inetpub\\wwwroot\\Default.htm -Value $env:computername"}
```
✅ Output:

json 
```
"provisioningState": "Succeeded"
```
*Screenshot*- VM-1 <img width="1919" height="665" alt="Screenshot 2025-10-29 115413" src="https://github.com/user-attachments/assets/5fec31f1-2d5b-44f6-93a7-6c9fa6ac777b" />
VM-2- <img width="1919" height="470" alt="Screenshot 2025-10-29 115432" src="https://github.com/user-attachments/assets/676d7f5f-5afb-411c-a560-b4068d8e8b49" />


🔹 Verification via Azure Bastion
Since both VMs were created without public IPs, direct RDP access was unavailable.

Fix Implemented:
Used Azure Bastion for secure in-browser RDP access.

Steps:

Created Bastion host:

Resource Group: LBresourcegroup

Name: MyBastionHost

Virtual Network: myVNet

Public IP: MyBastionIP

Navigated to myVM1 → Connect → Bastion → Login

Verified IIS by browsing:

arduino
Copy code
http://localhost
The web page displayed the hostname:

nginx
Copy code
myVM1
confirming successful IIS setup.

📸 Screenshots:

Bastion connection window

IIS “myVM1” page in browser
<img width="1911" height="916" alt="Screenshot 2025-10-29 120059" src="https://github.com/user-attachments/assets/85bdfd66-9e99-47f5-8df2-977087243e94" />

---

### 🧩 Task 8 — Add VMs to Backend Pool
Go to myLoadBalancer → Backend Pools → myBackendPool

Select:

- Virtual Network: myVNet
- Associated to: Virtual Machines

Click + Add → Select myVM1 and myVM2 → Save.
*Screenshot* <img width="1919" height="850" alt="Screenshot 2025-10-29 120713" src="https://github.com/user-attachments/assets/13d472b6-03ba-4d14-aa61-240a724b9ed2" />

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
