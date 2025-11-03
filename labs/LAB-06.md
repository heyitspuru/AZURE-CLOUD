# LAB-06: Implementing Azure Private DNS Zones

## Overview
This lab demonstrates how to configure and verify Azure Private DNS Zones to enable private name resolution within a virtual network.  
You will create a private DNS zone, link it to a VNet, enable auto-registration, and test name resolution using a virtual machine.

## Objectives
- Understand the purpose of Azure Private DNS Zones  
- Create and link a DNS zone to a Virtual Network (VNet)  
- Enable auto-registration for dynamic record updates  
- Test internal name resolution between virtual machines

## Prerequisites
- Active Azure for Students or free-tier subscription  
- Basic understanding of Azure networking and DNS concepts  
- Access to the Azure Portal or Azure CLI

---

## Step-by-step Implementation (Azure Portal)

### Step 1 — Create a Resource Group
1. Navigate to Resource groups → + Create  
2. Name: `Lab7-DNS-RG`  
3. Region: `East Asia` (or any supported region)  
4. Review + Create → Create

Screenshot:
![Screenshot 1](https://github.com/user-attachments/assets/7f5ccf75-d3a4-4a39-bbac-5a7c0c4db483)

---

### Step 2 — Create a Private DNS Zone
1. Search → Private DNS Zones → + Create  
2. Resource group: `Lab7-DNS-RG`  
3. Name: `private.contoso.com`  
4. Region: `East Asia`  
5. Review + Create → Create

Screenshot:
![Screenshot 2](https://github.com/user-attachments/assets/37945357-abbb-47bd-b286-be156e661e4c)

---

### Step 3 — Create a Virtual Network
1. Search → Virtual networks → + Create  
2. Name: `MyVNet`  
3. Address space: `10.2.0.0/16`  
4. Subnet name: `mySubnet` (10.2.0.0/24)  
5. Region: `East Asia`  
6. Review + Create → Create

Screenshot:
![Screenshot 3](https://github.com/user-attachments/assets/a9831157-f88d-4751-9e35-0e11690425b1)

---

### Step 4 — Link VNet to Private DNS Zone
1. Open the DNS zone `private.contoso.com` → Virtual network links → + Add  
2. Link name: `MyVNetLink`  
3. Virtual network: `MyVNet`  
4. Enable auto-registration: ✅ (Enable)  
5. Create

---

### Step 5 — Create a Virtual Machine
1. Create a resource → Virtual Machine  
2. Resource group: `Lab7-DNS-RG`  
3. VM name: `myVM01`  
4. Image: `Windows Server 2019 Datacenter`  
5. Region: `East Asia`  
6. Username: `azureuser`  
7. Password: (your secure password)  
8. Public inbound ports: RDP (3389)  
9. VNet/Subnet: `MyVNet` / `mySubnet`  
10. Disable boot diagnostics: No  
11. Review + Create → Create

Screenshot:
![Screenshot 5](https://github.com/user-attachments/assets/61807c05-887e-4a65-85ab-468cae877218)

---

### Step 6 — Verify Auto-Registration
1. Go to Private DNS Zone → `private.contoso.com` → Record sets  
2. Confirm an A record named `myvm01` exists with:
   - Type: `A`  
   - Auto-registered: `True`

Screenshot:
![Screenshot 6](https://github.com/user-attachments/assets/ad327daf-ba57-4088-8f4a-7fd8568d3c69)

---

### Step 7 — Add a Manual Record
1. Inside the DNS zone → + Add record set  
2. Name: `db`  
3. Type: `A`  
4. IP address: Private IP of `myVM01`  
5. OK

Manual record screenshot:
![Manual record screenshot](https://github.com/user-attachments/assets/fb50de98-6f58-42d6-a647-6de135ed294c)

---

### Step 8 — Test DNS Resolution
1. Open Virtual Machines → `myVM01` → Run command → RunPowerShellScript  
2. Run the following commands:
   - ping myvm01.private.contoso.com  
   - ping db.private.contoso.com

Expected output:
Reply from 10.2.0.4: bytes=32 time<1ms TTL=128  
Reply from 10.2.0.5: bytes=32 time<1ms TTL=128

Screenshots:
![Ping screenshot 1](https://github.com/user-attachments/assets/e80df662-61e9-4310-bca8-cbae9c9f39a0)  
![Ping screenshot 2](https://github.com/user-attachments/assets/e90b29bb-8ecd-4f0e-adc5-0943b7334da8)

---

## Verification via Azure CLI (Optional)
Run the following commands in Azure CLI to perform the same steps:

# Create Resource Group
az group create -n Lab7-DNS-RG -l eastasia

# Create Private DNS Zone
az network private-dns zone create -g Lab7-DNS-RG -n private.contoso.com

# Create Virtual Network
az network vnet create -g Lab7-DNS-RG -n MyVNet --address-prefix 10.2.0.0/16 --subnet-name mySubnet --subnet-prefix 10.2.0.0/24

# Link VNet to DNS Zone with auto-registration
az network private-dns link vnet create -g Lab7-DNS-RG -n MyVNetLink -z private.contoso.com -v MyVNet --registration-enabled true

---

## Troubleshooting

- Issue: Record not auto-registering  
  - Possible cause: VM not on linked VNet  
  - Fix: Verify VNet link and region

- Issue: Ping fails  
  - Possible cause: Windows Firewall blocking ICMP  
  - Fix: Enable ICMP in Windows Defender Firewall

- Issue: Wrong IP resolved  
  - Possible cause: Cached DNS  
  - Fix: Run `ipconfig /flushdns` inside the VM

- Issue: RDP unreachable  
  - Possible cause: NSG not allowing 3389  
  - Fix: Add inbound rule for RDP

---

## Key Learnings
- Private DNS Zones enable secure name resolution within VNets.  
- Auto-registration dynamically updates records for VM lifecycle events.  
- Zones can be linked to multiple VNets for hybrid environments.  
- DNS queries remain private and never leave Azure’s backbone network.
