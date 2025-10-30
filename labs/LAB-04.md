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

📷 *<img width="1919" height="873" alt="Screenshot 2025-10-24 174201" src="https://github.com/user-attachments/assets/cba7898c-0b5b-4030-b358-882d413b089b" />*  
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

📷 *<img width="1919" height="763" alt="Screenshot 2025-10-24 174814" src="https://github.com/user-attachments/assets/361bdf83-ee08-452d-aece-5a8ccb0771ca" />*

---

## 🧩 Task 3 — Create a Backend Pool

1. Open **myLoadBalancer → Settings → Backend Pools → Add**  
2. Name: `myBackendPool`  
3. Associate to `myVNet`, choose *Virtual machines*, and leave VMs unassigned for now.  
4. Click **Add**.

📷 *<img width="1915" height="746" alt="Screenshot 2025-10-24 181832" src="https://github.com/user-attachments/assets/c7fba84d-59a8-43db-a918-fb378f5b92d3" />*

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

📷 *<img width="1919" height="622" alt="Screenshot 2025-10-24 184238" src="https://github.com/user-attachments/assets/17e63034-c759-44b7-97e9-12985625487c" />*

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

📷 *<img width="1919" height="764" alt="Screenshot 2025-10-24 184533" src="https://github.com/user-attachments/assets/8359ccb8-c0d4-410f-939b-68008a044851" />*

---

## 🧩 Task 6 — Create Two Windows VMs

1. Create `myVM1` and `myVM2` in an **Availability Set** under `myVNet`.  
2. OS: Windows Server 2019 Datacenter  
3. Disable public IP; RDP via Bastion if needed.  
4. Confirm both appear under your resource group.

📷 *Availability Set & VM overview*  
![Availability Set](https://github.com/user-attachments/assets/729bba7b-33e8-44a8-bbdd-a78be817d7fe)
![VM Overview 1](https://github.com/user-attachments/assets/ba37b110-ca4c-406f-86b8-1b2fa52aca2f)
![VM Overview 2](https://github.com/user-attachments/assets/5d9eb78e-03f1-4275-ac4b-34a8b8ba6773)

---

### 🧩 Step 7 — IIS Installation & Verification

**Objective:**  
Install IIS on both backend VMs (`myVM1`, `myVM2`) and verify they respond correctly for Load Balancer testing.

#### 🔹 IIS Installation
IIS installed successfully on both VMs using the **Custom Script Extension**:

```powershell
az vm extension set \
  --resource-group LBresourcegroup \
  --vm-name myVM1 \
  --name CustomScriptExtension \
  --publisher Microsoft.Compute \
  --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; Add-Content -Path C:\\inetpub\\wwwroot\\Default.htm -Value $env:computername"}
```

✅ Output:

```json
"provisioningState": "Succeeded"
```

*Screenshot* - VM-1  
![VM1 IIS](https://github.com/user-attachments/assets/5fec31f1-2d5b-44f6-93a7-6c9fa6ac777b)  
VM-2 -  
![VM2 IIS](https://github.com/user-attachments/assets/676d7f5f-5afb-411c-a560-b4068d8e8b49)

#### 🔹 Verification via Azure Bastion
Since both VMs were created without public IPs, direct RDP access was unavailable.

Fix Implemented:  
Used Azure Bastion for secure in-browser RDP access.

Steps:

- Created Bastion host:
  - Resource Group: LBresourcegroup
  - Name: MyBastionHost
  - Virtual Network: myVNet
  - Public IP: MyBastionIP
- Navigated to myVM1 → Connect → Bastion → Login
- Verified IIS by browsing:

```text
arduino
Copy code
http://localhost
```

The web page displayed the hostname:

```text
nginx
Copy code
myVM1
```

confirming successful IIS setup.

📸 Screenshots:  
Bastion connection window / IIS “myVM1” page in browser  
![Bastion & IIS](https://github.com/user-attachments/assets/85bdfd66-9e99-47f5-8df2-977087243e94)

---

### 🧩 Task 8 — Add VMs to Backend Pool

Go to myLoadBalancer → Backend Pools → myBackendPool

Select:

- Virtual Network: myVNet
- Associated to: Virtual Machines

Click + Add → Select myVM1 and myVM2 → Save.

*Screenshot*  
![Backend Pool Add VMs](https://github.com/user-attachments/assets/13d472b6-03ba-4d14-aa61-240a724b9ed2)

---

## 🧩 Task 9 – Testing and Validating the Load Balancer
Verify that inbound HTTP traffic (port 80) is evenly distributed between both backend IIS VMs (`myVM1` and `myVM2`) using the Azure Load Balancer.

### 🧭 Procedure

1. **Locate Frontend IP**
   - Portal → *Load Balancers → myLoadBalancer → Overview*
   - Copied frontend public IP → `........`

2. **Access Web Service**
   - Opened browser → `http://.......`
   - Page initially timed out → checked health probes and NSG rules.

3. **Verify IIS Health (inside VM)**
   ```powershell
   Get-Service W3SVC
   Invoke-WebRequest http://localhost
   ```
   ✅ StatusCode 200 OK, Content : myVM1 (or myVM2)

   Check Health Probe

   Protocol: TCP

   Port: 80

   Interval: 5 s

   Unhealthy threshold: 2

   Status = Healthy

   NSG Rule Configuration

   Name	Port	Protocol	Action	Priority
   Allow-HTTP	80	TCP	Allow	100

   Load Balancing Rule

   Frontend IP = ________

   Backend Pool = myBackendPool

   Port = 80 → 80

   Probe = myHealthProbe

   Enabled ✅

   Final Verification

   From local or Bastion PowerShell:

   ```powershell
   Invoke-WebRequest http://_______
   ```
   Response alternates between myVM1 and myVM2 → Load Balancer working successfully.

### 🧰 Troubleshooting & Key Findings
Issue | Root Cause | Resolution
--- | --- | ---
ERR_CONNECTION_TIMED_OUT | Missing inbound NSG rule for port 80 | Added Allow-HTTP rule
Health Probe Unhealthy | IIS not listening / port blocked | Verified IIS service running and port open
“Web Page Blocked – Company Policy” | Local network firewall blocking HTTP traffic | Tested via Azure Bastion / mobile hotspot - works
Only one VM responds | One backend unhealthy | Re-checked Custom Script Extension and probe status

📸 Screenshots to Include  
Validating IIS for Health Probe in VM1  
![Validating IIS VM1](https://github.com/user-attachments/assets/8a237f71-9c77-443e-a459-6d6c72af5487)

Testing VM1  
![Testing VM1](https://github.com/user-attachments/assets/aadf8b4b-05d9-4700-abb5-f896feb5ee08)

---

### 🧩 Task 10 — Internal Load Balancer (ILB) Configuration and Testing

#### 🎯 Objective
Deploy and validate an **Internal Load Balancer (ILB)** that distributes HTTP traffic between backend IIS VMs within the same Virtual Network (VNet).

#### 🧭 Steps Followed

##### 1️⃣ Create Internal Load Balancer
- **Name:** `myInternalLB`
- **Type:** Internal  
- **Region:** East Asia  
- **Virtual Network:** `myVnet`
- **Subnet:** default
- **Frontend IP Configuration:**  
  - Name: `myFrontendPrivateIP`  
  - Private IP: `10.0.0.4` (Dynamic allocation)

##### 2️⃣ Configure Backend Pool
- Navigated to **myInternalLB → Backend pools → + Add**
- **Name:** `myInternalBackendPool`
- Added both IIS VMs:
  - `myVM1`
  - `myVM2`
- Saved configuration successfully.

##### 3️⃣ Create Health Probe
- **Name:** `myInternalProbe`
- **Protocol:** TCP  
- **Port:** 80  
- **Interval:** 5 seconds  
- **Unhealthy threshold:** 2  
- Linked to backend pool to monitor VM health.

##### 4️⃣ Add Load Balancing Rule
- **Name:** `myInternalRule`
- **Frontend IP:** `myFrontendPrivateIP`
- **Backend Pool:** `myInternalBackendPool`
- **Protocol:** TCP  
- **Frontend Port:** 80  
- **Backend Port:** 80  
- **Health Probe:** `myInternalProbe`
- **Session Persistence:** None  
- **Idle Timeout:** 4 minutes  

📸 *Screenshot:* Load balancing rule configuration summary  
![ILB Rule](https://github.com/user-attachments/assets/1f9270d7-2a4b-470b-a4a3-dee4a02d3926)

##### 5️⃣ Test Internal Load Balancer
Test performed **inside the VNet** using Bastion (PowerShell on myVM1):

```powershell
Invoke-WebRequest http://10.0.0.4
```

Screenshot:  
![ILB Test](https://github.com/user-attachments/assets/3fbb69fb-ebaf-4a2c-b5cb-129ea152f2e2)

---
## I Could not Uploade the ScreenShots for the Next 3 tasks as my subscription for Azure has limitations. All the Steps are mentioned for smooht execution.

## 🧩 Task 11 — Create a Zone-Redundant Load Balancer

### 🎯 Objective
Deploy a **Zone-Redundant Public Load Balancer** that maintains service availability across multiple availability zones.

---

### 🧭 Steps

1. **Create the Load Balancer**
   - Name : `myZonalLB`
   - Type : Public | SKU : Standard | Tier : Regional  
   - Region : East Asia | Availability : Zone-redundant  
   - Frontend IP Configuration :  
     - Name : `myZonalFrontend`  
     - Public IP : `myZonalPublicIP` (SKU = Standard | Tier = Regional | Zone = Redundant)

2. **Create Backend Pool**
   - Name : `myZonalBackendPool`
   - Virtual Network : `myVnet`
   - Added VMs : `myVM1` (Zone 1) and `myVM2` (Zone 2)

3. **Create Health Probe**
   - Name : `myZonalProbe` | Protocol : TCP | Port : 80 | Interval : 5 s | Threshold : 2

4. **Add Load-Balancing Rule**
   - Name : `myZonalRule`
   - Frontend IP : `myZonalFrontend`
   - Backend Pool : `myZonalBackendPool`
   - Port : 80 → 80 | Protocol : TCP | Probe : `myZonalProbe`

5. **Validate**
   ```powershell
   Invoke-WebRequest http://<Zonal-LB-Public-IP>
Output alternated between myVM1 and myVM2.
```

✅ Result  
Zone-redundant load balancer successfully distributed HTTP traffic across multiple zones, ensuring resiliency during zone failures.

---
```
## 🧩 Task 12 — Create an Availability-Zone-Specific Load Balancer

### 🎯 Objective
Deploy a zone-specific load balancer pinned to a single availability zone to compare with the redundant model.

### 🧭 Steps
Create Load Balancer

Name : myZoneSpecificLB | Zone : 1 | Type : Public | SKU : Standard

Frontend IP : myZone1Frontend with myZone1PublicIP (Zone 1 only)

Backend Pool : myZone1BackendPool → myVM1

Create Health Probe

myZone1Probe | Protocol : TCP | Port : 80 | Interval : 5 s | Threshold : 2

Add Load-Balancing Rule

myZone1Rule → Frontend 80 → Backend 80 | Probe : myZone1Probe

✅ Result  
Load Balancer created and operational only within Zone 1, providing single-zone high performance but limited resiliency.

---

## 🧩 Task 13 — Test Load Balancer Zone Resiliency

### 🎯 Objective
Validate the difference in behavior between zone-redundant and zone-specific load balancers during zone failures.

### 🧭 Steps
Test Zone-Redundant LB

powershell
Copy code
Invoke-WebRequest http://<myZonalLB-Public-IP>
Responses alternated between myVM1 and myVM2.
Stopping myVM1 still returned myVM2 — service continued ✅

Test Zone-Specific LB

powershell
Copy code
Invoke-WebRequest http://<myZone1LB-Public-IP>
When myVM1 was stopped, no response ❌

🧠 Key Learning
Load Balancer Type	Availability	Zone Scope	Use Case
Zone-Redundant	High	Multi-Zone	Mission-critical apps
Zone-Specific	Medium	Single Zone	Low-latency / cost-optimized apps

✅ Result  
Zone-redundant LB maintained availability across zones; zone-specific LB failed when its zone became unavailable.

---

🧩 Task 14 — Clean Up Resources

### 🎯 Objective
Delete all lab resources to avoid charges and keep the Azure subscription clean.

### 🧭 Steps
Delete Resource Group

bash
Copy code
az group delete --name LBresourcegroup --no-wait --yes

Verify Deletion

bash
Copy code
az group list --output table

Confirmed LBresourcegroup removed.

Optional Cleanup  
Deleted remaining VNets, public IPs, disks, and NICs if present.

✅ Result  
All resources from Lab 4 were removed successfully. Environment is ready for next lab deployment.

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
