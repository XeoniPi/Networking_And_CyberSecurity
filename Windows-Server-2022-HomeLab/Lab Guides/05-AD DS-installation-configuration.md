# Lab Guide: Active Directory Domain Services (AD DS) Installation & Configuration

This guide covers the full setup of Active Directory Domain Services (AD DS) on Windows Server 2022, user provisioning, and joining a client workstation to the domain.

---

## 1. What is AD DS and Why is it Used?
**Active Directory Domain Services (AD DS)** is Microsoft's directory service system. It serves as a centralized management platform for a corporate network infrastructure. 

### Core Uses:
* **Identity Management:** Centralizes user accounts, credentials, and access rights.
* **Security & Authentication:** Validates network logins securely using the Kerberos protocol.
* **Resource Optimization:** Organizes computers, servers, printers, and shares logically.
* **Group Policy Enforcement:** Pushes software updates, security settings, and desktop restrictions across thousands of machines from a single dashboard.

---

## 2. Active Directory Installation in Server 2022

### Prerequisites Checked:
* **Static Server IP:** `192.168.56.10`
* **Preferred DNS:** `192.168.56.10` (points to itself)

### Installation Steps:
1. Open **Server Manager** -> Click **Add roles and features**.
2. Select **Role-based or feature-based installation** -> Select your server pool.
3. Check the box for **Active Directory Domain Services**.
4. In the pop-up window, click **Add Features** to include necessary management tools.
5. Click **Next** through the features window, click **Next** on the AD DS info screen, and click **Install**.
6. Wait for the feature installation to complete. **Do not close the window yet.**

---

## 3. AD DS Configuration: A to Z (Promoting to Domain Controller)

Once the files are installed, the server must be promoted to a Domain Controller (DC).

1. Click the **Notification Flag (⚠️)** at the top right of Server Manager.
2. Click **Promote this server to a domain controller**.
3. **Deployment Configuration:** Select **Add a new forest**.
4. **Root domain name:** Enter your custom corporate domain (e.g., `xeonipi.org`) -> Click **Next**.
5. **Domain Controller Options:** 
   * Leave Forest and Domain functional levels at *Windows Server 2016*.
   * Ensure **DNS Server** and **Global Catalog (GC)** are checked.
   * Type a secure **Directory Services Restore Mode (DSRM)** password (e.g., `ABC.123`) -> Click **Next**.
6. **DNS Options:** Ignore any delegation warnings -> Click **Next**.
7. **Additional Options:** Let the NetBIOS name autofill (e.g., `XEONIPI`) -> Click **Next**.
8. **Paths:** Leave default paths for Database, Log files, and SYSVOL -> Click **Next**.
9. **Review Options:** Review your choices -> Click **Next**.
10. **Prerequisites Check:** Ensure all validation checks pass. Click **Install**.
11. The server will automatically restart to complete the Active Directory promotion.

---

## 4. Creating Lab Users

To simulate an enterprise environment, we will provision two sample corporate user accounts.

1. Log back into the server as Administrator.
2. Open **Server Manager** -> **Tools** -> **Active Directory Users and Computers**.
3. Expand your domain name (`xeonipi.org`) -> Right-click the **Users** folder -> Select **New** -> **User**.

### User Account 1: Santo
* **First Name:** Santo
* **User logon name (UPN):** `santo`
* **Password:** `ABC.123`
* **Settings:** Uncheck *User must change password at next logon* (for lab environment ease) -> Check *Password never expires*.

### User Account 2: Nishad
* **First Name:** Nishad
* **User logon name (UPN):** `nishad`
* **Password:** `ABC.123`
* **Settings:** Uncheck *User must change password at next logon* -> Check *Password never expires*.

---

## 5. Client PC Setup: Joining the Domain & Access Testing

### Step A: Configure Client DNS
Your client VM must look up your domain through the Domain Controller.
1. On the **Client PC**, go to Network Properties.
2. Change **Preferred DNS Server** explicitly to **`192.168.56.10`**.

### Step B: Join the Domain
1. On the Client PC, open **Settings** -> **System** -> **About** -> **Advanced system settings**.
2. Go to the **Computer Name** tab -> Click **Change...**.
3. Change Member of from *Workgroup* to **Domain**.
4. Type your domain: `xeonipi.org` -> Click **OK**.
5. A prompt will appear. Enter the Server's **Administrator credentials** to authorize the join.
6. Restart the Client PC when prompted.

### Step C: Logging in as Domain Users
1. At the client Windows lock screen, select **Other User**.
2. Ensure it says *Sign in to: XEONIPI*.
3. Log in using the created user accounts:
   * **Username:** `santo` | **Password:** `ABC.123`
   * **Username:** `nishad` | **Password:** `ABC.123`
4. The system will build a localized corporate user profile dynamically.

---

## 6. Business Benefits of AD DS Deployment

| Feature | Standalone Workgroup | Active Directory (AD DS) |
| :--- | :--- | :--- |
| **Account Creation** | Must be created manually on every single PC | Created once on DC; works across all network PCs |
| **Password Policies** | Hard to enforce across independent machines | Universal policy (e.g., minimum 8 chars, complexity) |
| **Security Control** | No centralized tracking of user logins | Centralized auditing and instant account termination |
| **Resource Sharing** | Complex folder permissions per device | Simple, identity-based access control lists (ACL) |
| **Automation** | Scripts deployed manually per system | Group Policy Objects deploy configurations instantly |
