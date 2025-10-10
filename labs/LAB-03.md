# LAB-03: Azure Virtual Networks, Peering & Secure Connectivity

## Overview
This lab explores **Azure Virtual Networks (VNets)** — the foundational component of Azure networking.  
Students will learn to create VNets, configure subnets, establish **VNet-to-VNet peering** (both regional and global), and understand various networking terminologies and services such as **Network Security Groups (NSGs)**, **Load Balancers**, **Application Gateways**, and **Traffic Managers**.

## Objectives
- Understand Azure Virtual Network components and configuration  
- Create VNets and subnets  
- Implement VNet peering within the same region and across regions (Global VNet Peering)  
- Explore Azure networking services and security controls  
- Configure Private Tunnel (VNet-to-VNet Gateway connection)

## Prerequisites
- Active Azure Subscription  
- Azure Portal or Azure CLI access  
- Basic understanding of IP addressing and subnets  
- Two different Azure regions available for deployment (for global peering)

---

## 🧩 Step 1 — Create a Virtual Network (VNet)

### 🔹 Procedure
1. Navigate to **Azure Portal → Create a resource → Networking → Virtual Network**  
2. Set configuration:  
   - **Name:** `VNet-East`  
   - **Region:** East US  
   - **Address space:** `10.1.0.0/16`  
   - **Subnet name:** `Subnet-EastUS` → `10.1.0.0/24`  
   - **DNS servers:** Default (Azure-provided)  
   - **Resource group:** `Lab3-Networking`  
3. Create the VNet and verify deployment.

📷 **Screenshots**
- VNet creation form
- <img width="1919" height="887" alt="Screenshot 2025-10-10 105405" src="https://github.com/user-attachments/assets/3a99ecf0-2b79-4ec3-9230-b7ca21efa940" />


---

## 🧩 Step 2 — Create a Second VNet in Another Region

### 🔹 Procedure
1. Repeat Step 1, but create another VNet:  
   - **Name:** `VNet-West`  
   - **Region:** West Europe  
   - **Address space:** `10.2.0.0/16`  
   - **Subnet:** `Subnet-WestEurope` → `10.2.0.0/24`  
2. Verify both VNets exist under your resource group.

📷 **Screenshots**
- VNet-West configuration page
- <img width="1919" height="900" alt="Screenshot 2025-10-10 110346" src="https://github.com/user-attachments/assets/466389d1-ef96-4430-920c-aa113b384fe2" />
 

---

## 🔄 Step 3 — Create Regional VNet-to-VNet Peering (Same Region)

### 🔹 Procedure
1. Go to `VNet-EastUS` → **Peerings → + Add**  
2. Make another Vnet in the Vnet-East Region with Diffrent Address space
3. Configure peering to another VNet (within same region):
   - **Peering link name:** `witinRegion`  
   - **Allow virtual network access:** ✅  
   - **Allow forwarded traffic:** ✅ (optional)  
4. Confirm **Connected** status in both VNets.

📷 **Screenshots**
- Vnet Creation of another Vnet in same region-<img width="1919" height="815" alt="Screenshot 2025-10-10 120405" src="https://github.com/user-attachments/assets/34d51958-0a5d-446e-9596-eaca5675a1ff" />

- Peering status “Connected” - <img width="1916" height="860" alt="Screenshot 2025-10-10 115311" src="https://github.com/user-attachments/assets/c61836b2-6e75-4e5c-a12b-8379923b2fd9" />


---

## 🌎 Step 4 — Configure Global VNet Peering (Cross-Region)

### 🔹 Procedure
1. In `VNet-East`, go to **Peerings → Add → Remote virtual network**  
2. Select `VNet-WestEurope` (created earlier).  
3. Enable the following options:  
   - Allow traffic between VNets  
   - Allow forwarded traffic  
   - Use remote gateways (if testing VPN connectivity)  
4. Validate successful global peering: both VNets show **Connected**.

📷 **Screenshots**
- Peering creation wizard
  <img width="1919" height="755" alt="Screenshot 2025-10-10 115524" src="https://github.com/user-attachments/assets/578619c9-eee3-47d2-888a-3b3f6401da0b" />


---

## 🔐 Step 5 — Network Security Groups (NSG)

### 🔹 Procedure
1. Create a new **Network Security Group**:  
   - **Name:** `NSG-East`  
   - Add inbound rule:  
     - Allow HTTP (80) / HTTPS (443)  
     - Allow RDP (3389) for testing  
2. Associate NSG with `Subnet-East`.  
3. Validate using effective security rules.

📷 **Screenshots**
- NSG rule list
<img width="1919" height="901" alt="Screenshot 2025-10-10 234834" src="https://github.com/user-attachments/assets/947af7a5-74aa-456d-9571-909a6b553ac4" />
 
- Subnet association page  
<img width="1905" height="686" alt="Screenshot 2025-10-10 234740" src="https://github.com/user-attachments/assets/e6e419a1-3dcd-4b31-a550-946b2f44e6f1" />

---

