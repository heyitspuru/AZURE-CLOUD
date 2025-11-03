LAB-07: Managing Tenants, MFA, and Role-Based Access Control (RBAC) in Microsoft Azure
Overview

This lab focuses on managing tenants and subscriptions, implementing Multi-Factor Authentication (MFA), creating groups, and assigning Role-Based Access Control (RBAC) roles in Microsoft Azure.

Objectives

Understand Azure tenants and directory management.

Implement and verify Multi-Factor Authentication (MFA).

Manage users, groups, and assign RBAC roles effectively.

Prerequisites

Active Azure account (Student subscription preferred)

Global Administrator or Owner access to Azure AD

Lab Steps
1. Create a New Tenant

Sign in to the Azure Portal
.

Navigate to Azure Active Directory → Manage tenants → + Create.

Choose Azure Active Directory and provide:

Organization name

Initial domain name

Country/Region

Click Create to complete the process.

📸 Add Screenshot: <img width="1919" height="751" alt="Screenshot 2025-11-04 005856" src="https://github.com/user-attachments/assets/9c205ae8-ff5b-4f66-ac2b-3ca1bdcc51da" />


2. Switch to the New Tenant

Click on your profile icon (top-right corner).

Select Switch directory.

Choose the newly created tenant.

📸 Add Screenshot: <img width="1886" height="760" alt="Screenshot 2025-11-04 010230" src="https://github.com/user-attachments/assets/c995e5f5-ad46-46b6-8a0e-32cf2132bc07" />


3. Transfer Subscription to the New Tenant

Navigate to Subscriptions.

Select your subscription (e.g., Azure for Students).

Click Change directory → select the new tenant.

Follow the on-screen steps to confirm.

⚠️ Ensure you have admin permissions on both tenants.

📸 Add Screenshot: <img width="1919" height="900" alt="Screenshot 2025-11-04 010648" src="https://github.com/user-attachments/assets/d8f0cf5a-30c2-40dd-b10e-a8371408de19" />

<img width="1919" height="854" alt="Screenshot 2025-11-04 010917" src="https://github.com/user-attachments/assets/8adf8cb3-634b-4d89-bb9b-bf49f8191639" />

4. Create a User Account

Navigate to Azure Active Directory → Users → + New user.

Choose Create new user or Invite external user.

Enter:

Username

Name

Role (e.g., User)

Set an initial password → Create.

📸 Add Screenshot: <img width="1919" height="853" alt="Screenshot 2025-11-04 011209" src="https://github.com/user-attachments/assets/3dddbd44-6545-49cc-bd72-3c9119dda646" />


5. Assign Role-Based Access (RBAC) to the User

Go to Subscriptions → Access control (IAM).

Click + Add → Add role assignment.

Choose Role = Owner.

Under Assign access to, select User, group, or service principal.

Search and select the created user → Save.

📸 Add Screenshot: <img width="1908" height="634" alt="Screenshot 2025-11-04 011626" src="https://github.com/user-attachments/assets/6ed0ca67-2ff8-4720-a38f-3864f5f92a87" />


6. Log In with the New User Account

Sign out of the portal.

Sign in again using the new user’s credentials.

Verify that the user has Owner access to the subscription and resources.


7. Implement Multi-Factor Authentication (MFA)

Go to Azure Active Directory → Users.

Select the specific user.

Navigate to Authentication methods or Multi-Factor Authentication.

Enable MFA for the user.

The user will be prompted to configure MFA (e.g., Authenticator app, SMS, etc.) on next login.

📸 Add Screenshot: <img width="1911" height="684" alt="Screenshot 2025-11-04 011925" src="https://github.com/user-attachments/assets/134b8851-7aae-47bd-be18-65b95563d200" />


8. Create a Group and Assign Roles
Step 1: Create Group

Go to Azure Active Directory → Groups → + New group.

Choose:

Group type: Security

Group name and description

Click Create.

📸 Add Screenshot: <img width="1919" height="831" alt="Screenshot 2025-11-04 012200" src="https://github.com/user-attachments/assets/c88c6671-5f04-4d36-82ed-3d0737515cd8" />


Step 2: Add Members & Owners

Open the newly created group.

Click Members → + Add members → select users.

Click Owners → + Add owners → select admin.


Step 3: Assign RBAC Role to Group

Navigate to Subscriptions → Access control (IAM).

Click + Add → Add role assignment.

Choose Role = Owner.

In Assign access to, select Group.

Choose your group → Save.
