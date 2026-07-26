# Lab Guide: Active Directory Workstation Restrictions (Logon To For User)

This guide demonstrates how to configure the **Logon To For User** restriction policy in Windows Server 2022 Active Directory Domain Services (AD DS). This security standard controls exactly which physical computers a domain user is authorized to sit down at and log into.

---

## 1. What is the "Logon To" Feature and Why Use It?
The **Logon To** (Workstation Restriction) feature allows domain administrators to bind a user identity to specific computer hostnames within the network infrastructure. 

By default, an Active Directory user account can log into *any* domain-joined client workstation. Restricting this access hardens your security posture.

### Why Corporate Networks Use Workstation Restrictions:
* **Credential Theft Isolation:** If a threat actor steals an employee's password, they cannot use it to lateral-move or log into random reception desks, executive laptops, or server consoles. The stolen account remains useless anywhere except the user's specific assigned desk.
* **Internal Accountability & Privacy:** Prevents coworkers within the same office from using someone else's credentials to log into a random machine and perform malicious actions under a fake identity.
* **Role-Based Device Binding:** Ensures specialized personnel (like HR or Finance) can only access domain accounts on heavily secured hardware endpoint profiles assigned to their departments.

---

## 2. Lab Infrastructure Layout
In our environment, we are testing with two corporate user accounts and two separate Windows 10 Pro domain-joined virtual clients:

* **Active Directory Users:**
  * `santo` (Password: `ABC.123`)
  * `nishad` (Password: `ABC.123`)
* **Domain Client Computes (Hostnames):**
  * **Win10Pro UserOne:** Hostname `DESKTOP-0MMTTUG`
  * **Win10Pro UserTwo:** Hostname `DESKTOP-NK2QS3R`

### Security Policy To Enforce:
The user **`nishad`** must be strictly tied to **Win10Pro UserOne (`DESKTOP-0MMTTUG`)**. If he attempts to touch or log into UserTwo, the domain controller must reject his request.

---

## 3. Step-by-Step Server Configuration

1. Log into your **Windows Server 2022** Domain Controller.
2. Open **Server Manager** -> **Tools** -> **Active Directory Users and Computers**.
3. Expand your domain root (`xeonipi.org`) and click the **Users** container.
4. Locate the user **`nishad`**, right-click his name, and select **Properties**.
5. Switch to the **Account** tab.
6. Click the **Logon To...** button located right next to the Logon Hours options.
7. Inside the Logon Workstations dialog box:
   * Switch the radio button choice from *All computers* to **The following computers**.
   * In the **Computer name:** text input box, type the exact hostname of the allowed workstation: 
     ```text
     DESKTOP-0MMTTUG
     ```
   * Click the **Add** button. *(The name will populate into the restricted machine list lower pane)*.
8. Click **OK** to close the window, then click **Apply** and **OK** on the primary user properties panel to commit the rules.

---

## 4. Client-Side Verification & Verification Tests

### Test Step A: Authorized Login Attempt (Win10Pro UserOne)
1. Turn on the workstation **Win10Pro UserOne** (`DESKTOP-0MMTTUG`).
2. Log in using Nishad's credentials:
   * **Username:** `nishad` | **Password:** `ABC.123`
3. **Expected Result:** Nishad successfully logs in. The system builds his user desktop path environment normally because the hostname matches the AD controller white-list profile rules.

### Test Step B: Unauthorized Login Attempt (Win10Pro UserTwo)
1. Move over to the second workstation **Win10Pro UserTwo** (`DESKTOP-NK2QS3R`).
2. Try logging in using the exact same credentials:
   * **Username:** `nishad` | **Password:** `ABC.123`
3. **Expected Result:** Windows blocking security takes over. The login interface immediately displays this explicit error banner:
   > *"Your account is configured to prevent you from using this computer. Please try another computer."*

### Test Step C: Control Group Check (Santo Account)
1. While still standing at **Win10Pro UserTwo** (`DESKTOP-NK2QS3R`), log in using Santo's profile credentials.
2. **Expected Result:** Santo signs in with zero errors because his profile account settings remain untouched under the default *All computers* lookup permission state.

---

## 5. Enterprise Architectural Advantages & Operational Notes

* **Multi-Computer Whitelisting:** Active Directory allows administrators to add multiple computer hostnames to a single user's list. For example, a roamer technician can be granted access to `DESKTOP-1`, `DESKTOP-2`, and `LAPTOP-TEST` simultaneously while being banned from everything else.
* **Server Infrastructure Defense:** This feature is excellent for preventing regular helpdesk staff or basic users from ever authenticating on highly sensitive database servers or edge domain endpoints by restricting their logon rights strictly to standard office workstation pools.
* **Important Automation Note:** Hostnames typed into the *Logon To* array are evaluated as literal strings matching the NetBIOS names of the computer objects. Ensure spelling matches exactly what is registered under your AD DS *Computers* structural tree layout.
