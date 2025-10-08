# LAB-02: Azure Blob Storage – Static Website, Access Control & Advanced Features

---

## Overview

This lab explores **Azure Blob Storage** and its capabilities for static website hosting, secure access control, lifecycle management, CORS configuration, and multipart uploading.  
Through this lab, Azure Blob Storage is used not just as an object store, but as a **scalable web hosting and data management platform**.

---

## Objectives

- Host a static website using Azure Blob Storage  
- Configure secure access using **Managed Identities**  
- Set up **Lifecycle Management** for automatic blob tiering  
- Enable and test **CORS rules**  
- Perform **multipart upload** using Azure CLI  

---

## Prerequisites

- Active Azure subscription  
- Access to Azure Portal  
- Azure CLI (in Cloud Shell or VM)  
- Azure Storage Explorer (optional)  
- Basic knowledge of Azure IAM and RBAC  

---

## 🌐 Step 1 — Create and Configure Static Website

### 🔹 Procedure

1. **Created a Storage Account**  
    - Name: `lab2storageacct`  
    - Type: Standard, General-purpose v2  
    - Replication: LRS  
    - Region: East US  
    - Access tier: Hot  

2. **Enabled Static Website Hosting:**  
    - Go to: *Storage Account → Settings → Static Website*  
    - Enabled it  
    - Index document name → `index.html`  
    - Error document name → `404.html`  
    - Saved configuration  

3. **Uploaded files in `$web` container:**  
    - `index.html`, `404.html`, and CSS/JS assets  
    - Verified correct **Content-Type** (`text/html`, `text/css`, etc.)  

4. **Verified website at Primary Endpoint URL:**  

---

### 📸 Screenshots

- ![Storage Account Created](<img width="1917" height="899" alt="Screenshot 2025-10-08 015807" src="https://github.com/user-attachments/assets/c70cda90-94e0-4ed2-bab9-6717e7a27bdc" />
)
- ![Static Website Enabled](<img width="1884" height="901" alt="Screenshot 2025-10-08 015828" src="https://github.com/user-attachments/assets/7204f7e1-443d-4e49-9d30-f33b1694c649" />
)
- ![Files Uploaded](<img width="1919" height="911" alt="Screenshot 2025-10-08 015842" src="https://github.com/user-attachments/assets/120683fd-c221-4c21-8101-40010dd04481" />
)
- ![Website Live](<img width="1859" height="1079" alt="Screenshot 2025-10-08 015852" src="https://github.com/user-attachments/assets/014de820-afa9-48a4-bb76-e6de7f975cf2" />
)

---

### ⚙️ Issue Faced — Blank White Page

- **Error:** Static website loaded as a blank page.  
- **Cause:** Incorrect content type and index file name mismatch.  
- **Fix:**  
    - Ensured file was uploaded to `$web` container.  
    - Renamed file to `index.html` (case-sensitive).  
    - Updated Content-Type to `text/html` under blob properties.  
- ✅ Website loaded successfully.

---

## 🔑 Step 2 — Configure Access via Managed Identity

### 🔹 Procedure

1. **Created a Windows Server VM** named `lab2vm`.  
    - Enabled **System-assigned Managed Identity** during creation.  

2. **In Storage Account → Access Control (IAM) → Added role assignment:**  
    - Role: `Storage Blob Data Reader`  
    - Assigned to: Managed Identity → `lab2vm`  

3. **Connected to the VM using RDP.**  
    - Installed **Azure CLI**:
      ```powershell
      Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi
      Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
      ```
    - Verified installation:
      ```powershell
      az --version
      ```

4. **Logged in with Managed Identity:**
    ```powershell
    az login --identity
    Listed containers:

    az storage container list --account-name lab2storageacct --auth-mode login --output table
    ```

- ✅ Successfully listed containers ($web, uploads).

---

### 📸 Screenshots
-VM Creation:
<img width="1871" height="834" alt="Screenshot 2025-10-08 020437" src="https://github.com/user-attachments/assets/1768193a-577b-4919-a8b9-0d98fe00df30" />

-IAM Access Control for the VM:
<img width="1895" height="832" alt="Screenshot 2025-10-08 021043" src="https://github.com/user-attachments/assets/8edfeb45-a2a6-40be-8fac-f5c955c63584" />

-Identity Verification inside VM:
<img width="1735" height="929" alt="Screenshot 2025-10-08 023926" src="https://github.com/user-attachments/assets/effbb2b7-ffd6-4a8f-a622-b902ae2a7bd5" />

---

### ⚙️ Managed Identity Access Troubleshooting

- **Error:**
    ```
    There are no credentials provided in your command and environment...
    Skip querying account key due to failure: Storage account 'lab2storageacct' not found.
    ErrorCode: NoAuthenticationInformation
    ```

- **Root Cause:**  
    Azure CLI attempted to use account keys instead of the Managed Identity token.

- **Fix:**  
    - Added `--auth-mode login` to use Azure AD-based authentication.
    - Verified VM had Storage Blob Data Reader IAM role.
    - Waited a few minutes for RBAC propagation.

- ✅ Containers listed successfully after running:
    ```
    az storage container list --account-name lab2storageacct --auth-mode login --output table
    ```

---

## ♻️ Step 3 — Lifecycle Management Configuration

### 🔹 Procedure

- In Storage Account → Data Management → Lifecycle Management
- Created rule:
    - Name: `cool-tier-rule`
    - Scope: Entire Storage Account
    - Action: Move blobs to Cool tier after 30 days of no modification.
- Saved and verified rule creation.

---

### 📸 Screenshots
<img width="1919" height="888" alt="Screenshot 2025-10-08 024328" src="https://github.com/user-attachments/assets/babe39b2-f276-492b-9ac1-60aef57b12db" />

---

## 🌍 Step 4 — Enable and Test CORS

### 🔹 Procedure

- In Storage Account → Settings → Resource Sharing (CORS)
- Added rule:
    - Allowed origins: `https://example.com`
    - Allowed methods: GET, POST
    - Allowed headers: *
    - Exposed headers: *
    - Max age (seconds): 3600
- Tested with cross-origin request in browser (AJAX/Fetch)
    - ✅ Response headers showed successful CORS preflight.

---

### 📸 Screenshots
<img width="1919" height="832" alt="Screenshot 2025-10-08 024613" src="https://github.com/user-attachments/assets/e0e75517-d57f-4e74-b358-4f9a98d94780" />


---

## 📤 Step 5 — Multipart Upload via Azure CLI (Cloud Shell)

### 🔹 Procedure

- Opened Azure Cloud Shell (Bash).
- Uploaded local file `bigfile.zip` (≈150 MB) using the Upload button.
- Confirmed presence:
    ```
    ls -lh /home/puru/bigfile.zip
    ```

- Uploaded to blob storage:
    ```bash
    az storage blob upload \
      --account-name lab2storageacct \
      --container-name uploads \
      --name bigfile.zip \
      --file /home/puru/bigfile.zip \
      --type block \
      --auth-mode login
    ```

- Verified upload in Portal under uploads container.
    - ✅ Blob successfully uploaded using Managed Identity authorization.

---

### 📸 Screenshots
<img width="1908" height="595" alt="Screenshot 2025-10-08 222608" src="https://github.com/user-attachments/assets/5a084acb-743f-40da-bc06-88d4e4446091" />

---

### ⚙️ Issues Faced

**Error 1:**
```
[Errno 2] No such file or directory: 'C:UsersbharaDownloadsbigfile.zip'
```
- **Cause:** Attempted to upload local Windows file path inside Azure Cloud Shell (Linux environment).
- **Fix:** Uploaded file to Cloud Shell using the Upload button, then referenced `/home/puru/bigfile.zip`.

**Error 2:**
```
There are no credentials provided in your command and environment...
```
- **Fix:** Added `--auth-mode login` to use Azure AD authentication.

- ✅ Upload completed successfully.

---

## 🧩 Key Learnings

- Blob Storage can host static websites using `$web` container.
- Managed Identities provide secure, keyless authentication.
- Lifecycle management enables cost-optimized data retention.
- CORS allows safe cross-domain access to blob data.
- Multipart uploads can be done easily using Azure CLI or AzCopy.

---
