# Lab Guide: Structuring Active Directory with Organizational Units (OUs) and Departmental GPOs

This guide covers how to design an enterprise organizational structure in Windows Server 2022 using **Organizational Units (OUs)**. It details how to isolate users by departments, build custom Group Policy Objects (GPOs), and link them manually to apply targeted restrictions.

---

## 1. What are OUs and Why use them over Default Containers?
An **Organizational Unit (OU)** is a sub-container within Active Directory where you can place users, groups, computers, and other OUs. 

### Why Corporate Networks Use OUs:
* **Targeted Policy Application:** The default `Users` and `Computers` containers in Active Directory cannot have unique corporate policies linked to them directly. OUs allow you to split the business into logical departments and give each department its own rules.
* **Delegation of Administrative Rights:** Instead of making someone a full Domain Admin, you can grant a Helpdesk team control *only* over the IT OU to reset employee passwords, leaving the HR and Executive OUs protected.
* **Logical Structure:** It mirrors the real-world company departments, making infrastructure monitoring and automation cleaner.

---

## 2. Lab Enterprise Directory Design
To simulate a real-world enterprise infrastructure under our domain (`xeonipi.org`), we will map out two distinct operational departments with customized user credentials:

### Department A: HR Dept
* **Operational Goal:** Absolute desktop security and data leakage prevention.
* **Provisioned Personnel Profiles:**
  * User: `HR` (Password: `1234`)
  * User: `JrHR` (Password: `1234`)
  * User: `SrHR` (Password: `1234`)

### Department B: IT Dept
* **Operational Goal:** Advanced functional operational access with strict credential defense.
* **Provisioned Personnel Profiles:**
  * User: `CIO` (Password: `12345`)
  * User: `CTO` (Password: `12345`)
  * User: `SrIT` (Password: `12345`)

---

## 3. Step-by-Step Server Setup: Creating OUs and Users

1. Log into your **Windows Server 2022** Domain Controller.
2. Open **Server Manager** -> **Tools** -> **Active Directory Users and Computers** (`ADUC`).
3. Right-click your root domain name (`xeonipi.org`) -> Select **New** -> **Organizational Unit**.
4. **Name:** Type `HR Dept` -> Click **OK**.
5. Repeat the step to create a second OU named `IT Dept`.

### Provisioning Accounts:
1. Click your newly created **HR Dept** OU. Right-click inside the empty right pane -> Select **New** -> **User**.
2. Create the profiles (`HR`, `JrHR`, `SrHR`) and assign their passwords (`1234`).
3. Switch over to the **IT Dept** OU. Right-click inside the pane -> Select **New** -> **User**.
4. Create the engineering profiles (`CIO`, `CTO`, `SrIT`) and assign their passwords (`12345`).

---

## 4. Group Policy Management: Creating and Linking GPOs

Best practice dictates that GPO names should match their corresponding OUs exactly. This prevents configuration errors and keeps maintenance clean.

### Step A: Creating the New Objects
1. Open **Server Manager** -> **Tools** -> **Group Policy Management**.
2. Expand your forest, down to **Domains** -> **xeonipi.org**.
3. Right-click the folder labeled **Group Policy Objects** -> Select **New**.
4. **Name:** Type `HR Dept GPO` -> Click **OK**.
5. Right-click **Group Policy Objects** again -> Select **New**.
6. **Name:** Type `IT Dept GPO` -> Click **OK**.

### Step B: Linking GPOs to OUs Manually
A GPO does absolutely nothing until it is attached directly to an organizational container.

1. Right-click your physical **HR Dept** OU folder on the left pane -> Select **Link an Existing GPO...**.
2. A selection box showing all directory GPOs will appear. Select **`HR Dept GPO`** -> Click **OK**.
3. Right-click your physical **IT Dept** OU folder -> Select **Link an Existing GPO...**.
4. Select **`IT Dept GPO`** -> Click **OK**.

*You will now see a small shortcut link icon overlaying the organizational folders, showing the policy is live.*

---

## 5. Departmental Policy Requirements

Now that the pipelines are built, we can edit the configurations to push completely different security realities down to our target users:

### HR Dept GPO Enforcement (Desktop Hardening)
Right-click your `HR Dept GPO` under the Group Policy Objects directory -> Click **Edit...**. Configure:
* **Control Panel Access:** Navigate to `User Configuration` -> `Policies` -> `Administrative Templates` -> `Control Panel`. Enable the rule: **"Prohibit access to Control Panel and PC settings"**.
* **USB Block (Storage Removable Media):** Navigate to `Computer Configuration` -> `Policies` -> `Administrative Templates` -> `System` -> `Removable Storage Access`. Enable the rule: **"All Removable Storage classes: Deny all access"**. This blocks malicious file extractions via physical thumb drives.

### IT Dept GPO Enforcement (Targeted Lockout Restrictions)
Right-click your `IT Dept GPO` -> Click **Edit...**. Configure:
* **Strict Lockout Thresholds:** Navigate to `Computer Configuration` -> `Policies` -> `Windows Settings` -> `Security Settings` -> `Account Policies` -> `Account Lockout Policy`. Set:
  * **Account lockout threshold:** `2` invalid login attempts.
  * **Account lockout duration:** `10` minutes.
  * **Reset account lockout counter after:** `10` minutes.

---

## 6. Enterprise Architecture: OUs vs. Security Groups

Understanding the structural difference between these two Active Directory items is vital for systems engineering.

| Structural Element | Organizational Unit (OU) | Security Group |
| :--- | :--- | :--- |
| **Primary Function** | A folder container used to organize objects and link **Group Policy Objects (GPOs)**. | A collection of user accounts used to grant **permissions to assets** (Folders, Files, Printers). |
| **GPO Linking** | **Yes.** You can link GPOs directly to them. | **No.** You can never deploy a GPO directly onto a Security Group. |
| **Nesting Capabilities** | Can hold users, computers, printers, and sub-OUs. | Can only hold user accounts, computer objects, or other groups. |
| **Nesting Behavior** | Objects can only reside physically inside **one single OU** at a time. | A single user can simultaneously belong to **hundreds of different groups** at once. |


### Final Verification Step:
Open **Command Prompt (Admin)** on your server and client machines and run:
```cmd
gpupdate /force
```

---

## 6. Architectural Best Practices & Advanced OU Insights

* **The Security Group Distinctions:** OUs are strictly for organizing objects and assigning GPOs. They are **not** security permissions tokens. If you need to give a team access to a shared network drive folder, you must build a **Security Group Object** inside that OU container and assign it NTFS permissions.
* **Inheritance Block Rules:** By default, sub-folders inherit rules from the top domain level. If a rule from the *Default Domain Policy* overrides your department goals, you can right-click an individual OU and select **Block Inheritance** to separate its configuration completely.
* **The "Protect Container from Accidental Deletion" Rule:** When creating custom enterprise OUs, always verify that the accidental deletion protection flag checkbox is checked in Active Directory. This prevents users or admins from accidentally wiping out an entire corporate department division with a simple misclick.
