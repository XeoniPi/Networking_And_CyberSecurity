# Lab Guide: Enforcing Custom Password Policies via GPO

This guide demonstrates how to configure and modify the Default Domain Password Policy in Windows Server 2022 Active Directory Domain Services (AD DS) using Group Policy Objects (GPO). It details how to lower restrictions for a homelab environment and verify the policy changes.

---

## 1. What is an AD DS Password Policy?
In an Active Directory environment, a **Password Policy** is a set of security rules enforced globally across all domain user accounts. It determines how secure, complex, and long a password must be, and how frequently users are forced to change it. 

By default, Microsoft enforces strict corporate settings (e.g., minimum 7 characters, complex combinations, and 42-day expiration). While these rules are necessary for actual organizations, modifying them in a **HomeLab** environment allows for rapid testing without constantly typing complex passwords.

---

## 2. Key Terms inside the Password Policy Panel

When auditing or changing password parameters, you will interact with these core policy elements:

* **Enforce password history:** Remembers older passwords so users cannot cycle back to the exact same password immediately.
* **Maximum password age:** Defines how many days a password remains valid before expiring (forcing a change).
* **Minimum password age:** Prevents users from changing their password multiple times in a single day to bypass password history rules.
* **Minimum password length:** Defines the fewest number of characters allowed for a password.
* **Password must meet complexity requirements:** Requires characters from three categories: uppercase, lowercase, numbers, and special characters (`!@#$`).
* **Store passwords using reversible encryption:** Keeps passwords in an unprotected text-like readable format. This is highly dangerous and should **always** remain **Disabled** unless required by legacy application protocols.

---

## 3. Homelab Scenario & Configurations
To make user switching faster on our lab client PCs, we will change our primary domain policies from high enterprise security to a relaxed, easy-to-use testing state:

| Policy Setting | Default Windows Value | Custom Lab Value |
| :--- | :--- | :--- |
| **Minimum password length** | 7 Characters | **4 Characters** |
| **Maximum password age** | 42 Days | **999 Days** (Stops random lab expirations) |
| **Password must meet complexity** | Enabled | **Disabled** (Allows pure numeric sequences) |

---

## 4. Step-by-Step Server Configuration

1. Log into your **Windows Server 2022** Domain Controller.
2. Open **Server Manager** -> Click **Tools** -> Select **Group Policy Management**.
3. Expand your forest node structure: **Forest: xeonipi.com** -> **Domains** -> **xeonipi.com**.
4. Locate the **Default Domain Policy**, right-click it, and select **Edit...**.
5. Inside the Group Policy Management Editor window, navigate down the following pathway tree:
   ```text
   Computer Configuration
   └── Policies
       └── Windows Settings
           └── Security Settings
               └── Account Policies
                   └── Password Policy
   ```
6. Double-click and modify these three items in the right pane:
   * **Minimum password length:** Change from `7` to **`4`** characters -> Click **Apply** -> **OK**.
   * **Maximum password age:** Change from `42` to **`999`** days -> Click **Apply** -> **OK**.
   * **Password must meet complexity requirements:** Select the **Disabled** radio button -> Click **Apply** -> **OK**.

### Step 5: Force Group Policy Update
Group Policy changes do not take effect immediately across the network. To instantly push these new password rules into memory, open **Command Prompt (Admin)** on your Server and execute:
```cmd
gpupdate /force
```

---

## 5. Account Reset Testing & Client Verification

### Step A: Resetting User Credentials on Server
Now that the restrictions are completely lowered, we will update our user profiles:

1. Open **Active Directory Users and Computers** (`Tools` -> `ADUC`).
2. Click the **Users** container directory.
3. Locate **`santo`** (or `nishad`), right-click the name, and select **Reset Password...**.
4. Change the old complex string from `ABC.123` to a simple number sequence: **`1234`**.
5. Uncheck *User must change password at next logon* -> Click **OK**.
6. The system will prompt a confirmation stating: *"The password for user has been changed."*

### Step B: Client PC Authentication Verification
1. Turn on your domain-joined client machine.
2. At the login portal screen, enter your target user details:
   * **Username:** `santo`
   * **Password:** `1234`
3. **Expected Result:** The system bypasses checking for complex parameters and successfully authenticates the simple 4-digit PIN sequence.

---

## 6. Fine-Grained Password Policies (FGPP) - Advanced Enterprise Feature

In real production environments, changing the *Default Domain Policy* lowers security for the entire company, including structural admins, which is bad practice. Instead, engineers use **Fine-Grained Password Policies (FGPP)**.

* **What it does:** Allows an organization to apply multiple, independent password policies within a single domain domain layout.
* **Real-World Application:** You can configure a relaxed policy for regular warehouse employees using barcode scanner kiosks, while simultaneously enforcing a strict 15-character policy with complex symbols exclusively for the **Domain Administrators** and **Finance Teams** using **Password Settings Objects (PSOs)**.
