# LAB-02: Azure Blob Storage – Static Website, Access Control & Advanced Features

## Overview
This lab explores **Azure Blob Storage** and its capabilities for static website hosting, secure access control, lifecycle management, CORS configuration, and multipart uploading.  
Through this lab, Azure Blob Storage is used not just as an object store, but as a **scalable web hosting and data management platform**.

## Objectives
- Host a static website using Azure Blob Storage  
- Configure secure access using **Managed Identities**  
- Set up **Lifecycle Management** for automatic blob tiering  
- Enable and test **CORS rules**  
- Perform **multipart upload** using Azure CLI  

## Prerequisites
- Active Azure subscription  
- Access to Azure Portal  
- Azure CLI (in Cloud Shell or VM)  
- Azure Storage Explorer (optional)  
- Basic knowledge of Azure IAM and RBAC  

---

## 🌐 Step 1 — Create and Configure Static Website

### 🔹 Procedure
1. Created a **Storage Account**  
   - Name: `lab2storageacct`  
   - Type: Standard, General-purpose v2  
   - Replication: LRS  
   - Region: East US  
   - Access tier: Hot  

2. Enabled **Static Website Hosting**:  
   - Go to: *Storage Account → Settings → Static Website*  
   - Enabled it  
   - Index document name → `index.html`  
   - Error document name → `404.html`  
   - Saved configuration  

3. Uploaded files in `$web` container:  
   - `index.html`, `404.html`, and CSS/JS assets  
   - Verified correct **Content-Type** (`text/html`, `text/css`, etc.)  

4. Verified website at **Primary Endpoint URL**:  


---

### 📸 Screenshots
- ![Storage Account Created](screenshots/lab2-storage-created.png)
- ![Static Website Enabled](screenshots/lab2-static-website-enabled.png)
- ![Files Uploaded](screenshots/lab2-files-uploaded.png)
- ![Website Live](screenshots/lab2-site-live.png)

---

### ⚙️ Issue Faced — Blank White Page
**Error:** Static website loaded as a blank page.  
**Cause:** Incorrect content type and index file name mismatch.  
**Fix:**  
- Ensured file was uploaded to `$web` container.  
- Renamed file to `index.html` (case-sensitive).  
- Updated Content-Type to `text/html` under blob properties.  
✅ Website loaded successfully.

---

## 🔑 Step 2 — Configure Access via Managed Identity

### 🔹 Procedure
1. Created a **Windows Server VM** named `lab2vm`.  
- Enabled **System-assigned Managed Identity** during creation.  

2. In Storage Account → **Access Control (IAM)** → Added role assignment:  
- Role: `Storage Blob Data Reader`  
- Assigned to: Managed Identity → `lab2vm`

3. Connected to the VM using **RDP**.  
- Installed **Azure CLI**:
  ```powershell
  Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi
  Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
  ```
- Verified installation:
  ```powershell
  az --version
  ```

4. Logged in with Managed Identity:
```powershell
az login --identity
Listed containers:

az storage container list --account-name lab2storageacct --auth-mode login --output table


✅ Successfully listed containers ($web, uploads).

📸 Screenshots

⚙️ Managed Identity Access Troubleshooting

Error:

There are no credentials provided in your command and environment...
Skip querying account key due to failure: Storage account 'lab2storageacct' not found.
ErrorCode: NoAuthenticationInformation


Root Cause:
Azure CLI attempted to use account keys instead of the Managed Identity token.

Fix:

Added --auth-mode login to use Azure AD-based authentication.

Verified VM had Storage Blob Data Reader IAM role.

Waited a few minutes for RBAC propagation.

✅ Containers listed successfully after running:

az storage container list --account-name lab2storageacct --auth-mode login --output table

♻️ Step 3 — Lifecycle Management Configuration
🔹 Procedure

In Storage Account → Data Management → Lifecycle Management

Created rule:

Name: cool-tier-rule

Scope: Entire Storage Account

Action: Move blobs to Cool tier after 30 days of no modification.

Saved and verified rule creation.

📸 Screenshots

🌍 Step 4 — Enable and Test CORS
🔹 Procedure

In Storage Account → Settings → Resource Sharing (CORS)

Added rule:

Allowed origins: https://example.com

Allowed methods: GET, POST

Allowed headers: *

Exposed headers: *

Max age (seconds): 3600

Tested with cross-origin request in browser (AJAX/Fetch)
✅ Response headers showed successful CORS preflight.

📸 Screenshots

📤 Step 5 — Multipart Upload via Azure CLI (Cloud Shell)
🔹 Procedure

Opened Azure Cloud Shell (Bash).

Uploaded local file bigfile.zip (≈150 MB) using the Upload button.

Confirmed presence:

ls -lh /home/puru/bigfile.zip


Uploaded to blob storage:

az storage blob upload \
  --account-name lab2storageacct \
  --container-name uploads \
  --name bigfile.zip \
  --file /home/puru/bigfile.zip \
  --type block \
  --auth-mode login


Verified upload in Portal under uploads container.
✅ Blob successfully uploaded using Managed Identity authorization.

📸 Screenshots

⚙️ Issues Faced

Error 1:

[Errno 2] No such file or directory: 'C:UsersbharaDownloadsbigfile.zip'


Cause: Attempted to upload local Windows file path inside Azure Cloud Shell (Linux environment).
Fix: Uploaded file to Cloud Shell using the Upload button, then referenced /home/puru/bigfile.zip.

Error 2:

There are no credentials provided in your command and environment...


Fix: Added --auth-mode login to use Azure AD authentication.

✅ Upload completed successfully.

🧩 Key Learnings

Blob Storage can host static websites using $web container.

Managed Identities provide secure, keyless authentication.

Lifecycle management enables cost-optimized data retention.

CORS allows safe cross-domain access to blob data.

Multipart uploads can be done easily using Azure CLI or AzCopy.
