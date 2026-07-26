# Lab Guide: Active Directory Logon Hours Configuration

This guide demonstrates how to configure Logon Hours in Windows Server 2022 Active Directory Domain Services (AD DS). This policy restricts when specific domain users can log into network client workstations, drastically improving organization security during off-hours.

---

## 1. What are Logon Hours and Why Use Them?
**Logon Hours** is a built-in security feature in Active Directory that lets administrators define exact days and specific hours a user is permitted to authenticate against the Domain Controller. 

### Why Corporate Networks Use Logon Hours:
* **Prevent Off-Hour Breaches:** Even if an attacker or unauthorized person steals an employee’s credentials, they cannot log into the corporate network during the weekend or middle of the night.
* **Compliance and Insider Threat Mitigation:** Restricts employees from entering systems outside their scheduled shifts, reducing unauthorized data changes or data theft.
* **Work-Life Balance Enforcements:** Some organizations use this to discourage employees from working past official hours by blocking system access.

---

## 2. Lab Scenario
In our homelab, we want to enforce an enterprise security policy on our user account:
* **User Account:** `santo`
* **Security Rule:** **Fully Denied** access on **Sunday** and **Monday**. 
* **Allowed Access:** Allowed to log in normally from Tuesday to Saturday.

---

## 3. Step-by-Step Server Configuration

1. Log into your **Windows Server 2022** Domain Controller.
2. Open **Server Manager** -> **Tools** -> **Active Directory Users and Computers**.
3. Expand your domain (`xeonipi.org`) and click the **Users** container.
4. Locate the user **`santo`**, right-click his name, and select **Properties**.
5. Switch to the **Account** tab.
6. Click the **Logon Hours...** button.
7. A blue grid matrix representing 7 days a week and 24 hours a day will appear:
   * Click and drag your mouse cursor to select the entire rows for **Sunday** and **Monday**.
   * With those days highlighted, select the **Logon Denied** radio button on the right side. *(The selected blocks will turn white, indicating access is restricted)*.
8. Click **OK** to close the grid interface, then click **Apply** and **OK** on the User Properties menu to save the changes.

---

## 4. Client-Side Verification & Testing

To confirm the policy works successfully, perform the following validation test on your lab client machine.

### Step A: Test Attempt during Restrictive Days (Sunday/Monday)
1. Turn on your domain-joined **Client PC**.
2. Try logging in using Santo's credentials:
   * **Username:** `santo`
   * **Password:** `ABC.123`
3. **Expected Result:** Windows will block the login attempt and display an explicit corporate security error message:
   > *"Your account has time restrictions that prevent you from signing in at this time. Please try again later or contact your network administrator."*

### Step B: Verification check for Nishad
1. Try logging in using Nishad's credentials on the same client machine:
   * **Username:** `nishad`
   * **Password:** `ABC.123`
2. **Expected Result:** Nishad will log in successfully because no time constraints were configured on his profile.

---

## 5. Advanced Features & Business Benefits of Logon Hours

Beyond blocking whole days, Active Directory offers advanced granular controls for enterprise management:

* **Shift Work Configuration:** You can split day and night shifts. For example, Customer Support agents can be restricted to log in *only* between 9:00 AM and 5:00 PM. If they attempt to log in at 5:01 PM, access is instantly denied.
* **Automatic Disconnection (Group Policy Pair):** By default, if a user is already logged in when their allowed hours end, Windows lets them stay active but blocks new actions. However, you can pair Logon Hours with a **Group Policy Object (GPO)** called *"Network security: Force logoff when logon hours expire"*. This will automatically and forcefully kick the user off the PC the exact minute their shift ends.
* **Maintenance Windows:** You can block all regular corporate staff from logging in during standard weekend maintenance windows (e.g., Sunday 12:00 AM to 4:00 AM) so IT teams can deploy patches safely without user interference.
