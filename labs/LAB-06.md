🧩 LAB-07: Implementing Azure Private DNS Zones
🔍 Overview

This lab demonstrates how to configure and verify Azure Private DNS Zones to enable private name resolution within a virtual network.
You’ll create a private DNS zone, link it to a VNet, enable auto-registration, and test name resolution using a virtual machine.

🎯 Objectives

Understand the purpose of Azure Private DNS Zones

Learn to create and link a DNS zone to a VNet

Enable auto-registration for dynamic record updates

Test internal name resolution between virtual machines

⚙️ Prerequisites

Active Azure for Students or free-tier subscription

Basic understanding of Azure networking and DNS concepts

Access to the Azure Portal or Azure CLI

🧱 Step-by-Step Implementation (Portal)
🔹 Step 1 – Create Resource Group

Navigate to Resource groups → + Create

Name → Lab7-DNS-RG

Region → East Asia (or any working region)

Review + Create → Create

📸 Screenshot 1 – <img width="1919" height="906" alt="Screenshot 2025-11-03 193445" src="https://github.com/user-attachments/assets/7f5ccf75-d3a4-4a39-bbac-5a7c0c4db483" />


🔹 Step 2 – Create a Private DNS Zone

Search → Private DNS Zones → + Create

Resource group → Lab7-DNS-RG

Name → private.contoso.com

Region → East Asia

Review + Create → Create

📸 Screenshot 2 – <img width="1919" height="896" alt="Screenshot 2025-11-03 190010" src="https://github.com/user-attachments/assets/37945357-abbb-47bd-b286-be156e661e4c" />


🔹 Step 3 – Create a Virtual Network

Search → Virtual networks → + Create

Name → MyVNet

Address space → 10.2.0.0/16

Subnet name → mySubnet (10.2.0.0/24)

Region → East Asia

Review + Create → Create

📸 Screenshot 3 – <img width="1919" height="816" alt="Screenshot 2025-11-03 190026" src="https://github.com/user-attachments/assets/a9831157-f88d-4751-9e35-0e11690425b1" />


🔹 Step 4 – Link VNet to Private DNS Zone

Go to your DNS zone → Virtual network links → + Add

Link name → MyVNetLink

Virtual network → MyVNet

✅ Enable auto-registration

Create

🔹 Step 5 – Create a Virtual Machine

Create a resource → Virtual Machine

Resource group → Lab7-DNS-RG

VM name → myVM01

Image → Windows Server 2019 Datacenter

Region → East Asia

Username → azureuser

Password → your secure password

Public inbound ports → RDP (3389)

VNet/Subnet → MyVNet / mySubnet

Disable boot diagnostics → No

Review + Create → Create

📸 Screenshot 5 – <img width="1919" height="895" alt="Screenshot 2025-11-03 192408" src="https://github.com/user-attachments/assets/61807c05-887e-4a65-85ab-468cae877218" />


🔹 Step 6 – Verify Auto-Registration

Go to Private DNS Zone → private.contoso.com

Under Record sets, confirm:

An A record named myvm01

Type → A

Auto-registered → True

📸 Screenshot 6 – <img width="1918" height="896" alt="Screenshot 2025-11-03 192445" src="https://github.com/user-attachments/assets/ad327daf-ba57-4088-8f4a-7fd8568d3c69" />


🔹 Step 7 – Add a Manual Record

Inside the DNS zone → + Add record set

Name → db

Type → A

IP address → Private IP of myVM01

OK

🔹 Step 8 – Test DNS Resolution

Go to Virtual Machines → myVM01 → Run command → RunPowerShellScript

Run:

ping myvm01.private.contoso.com
ping db.private.contoso.com


✅ Expected Output:

Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
Reply from 10.2.0.5: bytes=32 time<1ms TTL=128


📸 Screenshot 8 – <img width="1919" height="851" alt="Screenshot 2025-11-03 193319" src="https://github.com/user-attachments/assets/e80df662-61e9-4310-bca8-cbae9c9f39a0" />

<img width="1202" height="505" alt="Screenshot 2025-11-03 193330" src="https://github.com/user-attachments/assets/e90b29bb-8ecd-4f0e-adc5-0943b7334da8" />

- Fo manual recordset- <img width="1271" height="452" alt="Screenshot 2025-11-03 193421" src="https://github.com/user-attachments/assets/fb50de98-6f58-42d6-a647-6de135ed294c" />



🧰 Verification via Azure CLI (Optional)

If you prefer CLI:

# Create Resource Group
az group create -n Lab7-DNS-RG -l eastasia

# Create Private DNS Zone
az network private-dns zone create -g Lab7-DNS-RG -n private.contoso.com

# Create Virtual Network
az network vnet create -g Lab7-DNS-RG -n MyVNet --address-prefix 10.2.0.0/16 --subnet-name mySubnet --subnet-prefix 10.2.0.0/24

# Link VNet to DNS Zone with auto-registration
az network private-dns link vnet create -g Lab7-DNS-RG -n MyVNetLink -z private.contoso.com -v MyVNet --registration-enabled true

🧠 Troubleshooting
Issue	Possible Cause	Fix
Record not auto-registering	VM not on linked VNet	Verify VNet link and region
Ping fails	Windows Firewall blocking ICMP	Enable ICMP in Windows Defender Firewall
Wrong IP resolved	Cached DNS	Run ipconfig /flushdns inside VM
RDP unreachable	NSG not allowing 3389	Add inbound rule for RDP
📘 Key Learnings

Private DNS Zones enable secure name resolution within VNets.

Auto-registration dynamically updates records for VM lifecycle events.

Zones can be linked to multiple VNets for hybrid environments.

DNS queries remain private and never leave Azure’s backbone network.
