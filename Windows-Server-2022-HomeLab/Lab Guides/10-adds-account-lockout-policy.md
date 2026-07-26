# Lab Guide: Enforcing Account Lockout Policies via GPO

This guide covers how to configure and test an **Account Lockout Policy** in Windows Server 2022 Active Directory Domain Services (AD DS). This standard enterprise defense mechanism blocks automated cyberattacks and brute-force cracking attempts.

---

## 1. What is an Account Lockout Policy and Why Use It?
An **Account Lockout Policy** is an automated infrastructure defense setting. It tracks bad login attempts against network user profiles. If a user or an attacker enters an incorrect password too many times, Active Directory freezes the account.

### Why Corporate Networks Mandate This Policy:
* **Brute-Force & Dictionary Attack Protection:** If an attacker discovers an employee's username (like `santo` or `nishad`), they can run hacking tools to test thousands of random passwords every second. This policy stops them immediately by freezing the identity layer.
* **Internal Credential Guessing Prevention:** Disables unauthorized individuals within an office building from sitting at an open workstation trying to guess a coworker's private login credentials.

---

## 2. Key Terms inside the Account Lockout Panel

When configuring these rules in Group Policy, you will manage three interconnected settings:

* **Account lockout threshold:** The exact number of failed login attempts allowed before the Domain Controller locks the profile.
* **Account lockout duration:** The total number of minutes the account remains completely frozen and unusable before it automatically resets itself.
* **Reset account lockout counter after:** The window of time the server tracks failures. If a user mistypes a password once, the timer starts. If they do not fail again within this window, the counter drops back down to zero.

---

## 3. Homelab Scenario & Threshold Parameters
To test this defensive architecture quickly in our lab, we will use highly aggressive constraints:

* **Account Lockout Threshold:** **2 Attempts** *(Locked out on the 3rd wrong attempt)*
* **Account Lockout Duration:** **30 Minutes**
* **Reset Lockout Counter After:** **30 Minutes** *(Automatically changes to match duration rules)*

---

## 4. Step-by-Step Server Configuration

1. Log into your **Windows Server 2022** Domain Controller.
2. Open **Server Manager** -> Click **Tools** -> Select **Group Policy Management**.
3. Expand your structural tree node layout: **Forest: xeonipi.com** -> **Domains** -> **xeonipi.com**.
4. Locate the **Default Domain Policy**, right-click it, and select **Edit...**.
5. Inside the Group Policy Management Editor windows pane, navigate down the exact following tree trail:
   ```text
   Computer Configuration
   └── Policies
       └── Windows Settings
           └── Security Settings
               └── Account Policies
                   └── Account Lockout Policy
   ```
6. Double-click and modify the three available policies in the right pane:
   * **Account lockout threshold:** Change the setting value to **`2` invalid logon attempts** -> Click **Apply**.
   * *A confirmation popup box will appear notifying you that Windows will automatically adjust the duration and counter values to 30 minutes.* Click **OK**.
   * **Account lockout duration:** Ensure it shows **`30` minutes**.
   * **Reset account lockout counter after:** Ensure it shows **`30` minutes**.
7. Click **OK** and close the policy editor window.

### Step 7: Push Policies to the Network
To force Active Directory to register these brute-force restrictions instantly, open your Server's **Command Prompt (Admin)** and execute:
```cmd
gpupdate /force
```

---

## 5. Client-Side Authentication Testing & Validation

### Step A: Simulating the Attack Failure
1. Go to your domain-joined client PC.
2. Attempt to log in as **`santo`** (or `nishad`), whose actual active password is set to `1234`.
3. Intentional Failure 1: Type `abcd` -> Press Enter. *(Windows drops a bad password warning)*.
4. Intentional Failure 2: Type `9999` -> Press Enter. *(Windows drops a second warning)*.
5. Intentional Failure 3: Type anything wrong again. 
6. **Expected Result:** The security system triggers. The login screen locks and displays this explicit security banner statement:
   > *"The referenced account is currently locked out and may not be logged on to."*

### Step B: Verification of System Integrity
1. While Santo's account is locked out, log in with his correct password (`1234`). Windows will continue to reject it.
2. Move to Nishad's user account input box on the same PC. Enter his correct password (`1234`).
3. **Expected Result:** Nishad logs in with zero issues. This proves the lockout triggers individually per identity layer without crashing network accessibility for the rest of the company.

---

## 6. Crucial Enterprise Security Insights (What Every Admin Should Know)

* **The Administrator Account Exception:** By default, local and domain administrator profiles are protected against lockout duration policies to prevent malicious actors from locking corporate engineers out of their own systems.
* **Helpdesk Intervention (Manual Unlock):** If a real corporate user locks themselves out on a Monday morning, they do not have to sit idle for 30 minutes. An IT administrator can open **Active Directory Users and Computers**, go to the user's properties, click the **Account** tab, select the checkbox labeled **"Unlock account. This account is currently locked out on this Active Directory Domain Controller"**, and hit Apply. This lets them log back in immediately.
* **Denial of Service (DoS) Risk:** Setting the lockout threshold too low (like 1 or 2 attempts) can be risky in public organizations. Attackers can intentionally cycle through usernames entering bad passwords to trigger massive lockouts, freezing operations for entire office environments.
