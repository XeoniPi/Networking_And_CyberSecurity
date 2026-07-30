# Lab Guide: DHCP Server Multi-Scope Installation & Configuration

This guide demonstrates how to install and configure a multi-scope Dynamic Host Configuration Protocol (DHCP) service on Windows Server 2022. This simulates an enterprise network segmented into functional departments (IT and HR) without requiring physical switches or dedicated routers.

---

## 1. What is DHCP and Why Use It?
**Dynamic Host Configuration Protocol (DHCP)** is a network management protocol operating over UDP (Ports 67 and 68). It dynamically assigns IP addresses, subnet masks, default gateways, and DNS server strings to client devices.

### Why Corporate Networks Use DHCP:
* **Automation and Efficiency:** Manually assigning static IPs across thousands of devices is time-consuming and error-prone. DHCP automates configuration deployment.
* **Elimination of IP Conflicts:** The server tracks leases dynamically, preventing duplicate IP assignments.
* **Centralized Scope Management:** Network configuration changes (like updating a DNS target address) can be executed across a department instantly from a single server console.

---

## 2. Structural Infrastructure Design & VirtualBox Mapping

To isolate routing paths for individual Organizational Units (OUs), the server uses three independent Network Interface Cards (NICs). This mimics physical Virtual Local Area Networks (VLANs).

### A. Windows Server 2022 Virtual Interface Table

| Adapter Name | VirtualBox Type | Linked Subnet Name | Static IPv4 Address | Assigned Network Role |
| :--- | :--- | :--- | :--- | :--- |
| **Adapter 1** | NAT | WAN Internet | `10.0.2.15` (DHCP) | Windows Update / Web Access |
| **Adapter 2** | Internal Network | `IT-Network` | `192.168.56.10/24` | IT Department Directory & Services |
| **Adapter 3** | Internal Network | `HR-Network` | `192.168.20.10/24` | HR Department Directory & Services |

### B. Windows 10 Workstation Client Interface Table

| Client Target Hostname | VirtualBox Type | Connected Network | Dynamic IP Range Target | Assigned Department |
| :--- | :--- | :--- | :--- | :--- |
| **DESKTOP-0MMTTUG** (UserOne) | Internal Network | `IT-Network` | `192.168.56.50 - .200` | IT Department Desk |
| **DESKTOP-NK2QS3R** (UserTwo) | Internal Network | `HR-Network` | `192.168.20.50 - .200` | HR Department Desk |

---

## 3. Step-by-Step Server Setup: Installation & Authorization

### Step A: Install the DHCP Server Role
1. Log into your **Windows Server 2022** Domain Controller (`xeonipi.org`).
2. Open **Server Manager** -> Click **Add roles and features**.
3. Proceed to **Server Roles** -> Select **DHCP Server** -> Click **Add Features** -> **Install**.

### Step B: Complete Post-Deployment Authorization
In an Active Directory infrastructure, an unauthorized (rogue) DHCP server can hand out wrong gateway configurations and intercept user network traffic. Active Directory blocks this behavior. You must authorize your new server before it can allocate addresses.

1. Click the **Notification Flag (⚠️)** at the top right of Server Manager.
2. Select **Complete DHCP configuration**.
3. On the Authorization Wizard window, click **Next**.
4. Choose **Use the following user's credentials** (Ensure it displays your `XEONIPI\Administrator` account) -> Click **Commit** -> **Close**.

---

## 4. Configuring Departmental Scopes (IT & HR)

1. Open **Server Manager** -> **Tools** -> **DHCP**.
2. Expand your server name node (`server22.xeonipi.org`) -> Expand **IPv4**.

### Step A: Building the IT Department Scope
1. Right-click **IPv4** -> Select **New Scope...** -> Click **Next**.
2. **Name:** `IT-Dept` | **Description:** `Scope for IT Department Subnet (192.168.56.0/24)` -> Click **Next**.
3. **IP Address Range:**
   * **Start IP address:** `192.168.56.1`
   * **End IP address:** `192.168.56.254`
   * **Subnet Mask Length:** `24` (Subnet mask autofills to `255.255.255.0`). Click **Next**.
4. **Add Exclusions and Delay:** Exclusions block the server from leasing out designated IPs, preserving them for infrastructure endpoints (like printers, routers, and static servers).
   * **Start IP:** `192.168.56.1` | **End IP:** `192.168.56.99`
   * Click **Add** -> Click **Next**. *(This forces the server to start dynamic deployment at `.100` or above).*
5. **Lease Duration:** Leave at the default **8 Days** -> Click **Next**.

> 💡 **Understanding Lease Durations:** Lease duration defines how long a client device retains an IP address before it must request a renewal from the server. For stable environments (like desktop office clusters), an 8-day lease is efficient. For high-turnover public Wi-Fi networks, a lower lease duration (e.g., 2 to 8 hours) prevents the server from running out of available addresses.

6. Select **Yes, I want to configure these options now** -> Click **Next**.
7. **003 Router (Default Gateway):** Type `192.168.56.1` -> Click **Add** -> Click **Next**.
8. **006 DNS Servers:** Verify your host DNS IP `192.168.56.10` is listed -> Click **Next**.
9. **WINS Servers:** Leave blank -> Click **Next**.
10. Select **Yes, I want to activate this scope now** -> Click **Finish**.

### Step B: Building the HR Department Scope
Repeat the New Scope process using the following settings:
* **Name:** `HR-Dept`
* **Address Range:** `192.168.20.1` to `192.168.20.254`
* **Exclusions:** `192.168.20.1` to `192.168.20.99`
* **003 Router (Gateway):** `192.168.20.1`
* **006 DNS Server:** `192.168.20.10`

---

## 5. Network Optimization & Interface Binding

To prevent the DHCP server from routing requests down the wrong interfaces, verify its underlying binding configurations.

1. In the DHCP console tree, right-click **IPv4** -> Select **Properties** -> Switch to the **Advanced** tab.
2. Click the **Bindings...** button.
3. Ensure the check-boxes match your physical routing cards correctly:
   * `192.168.56.10` (**Active**)
   * `192.168.20.10` (**Active**)
4. Click **OK**. Open an administrative **Command Prompt** on the server and run `gpupdate /force` to refresh policy layers.

---

## 6. Client Verification & Testing Commands

To test assignment accuracy across the virtual switches, run the following verification routines on your client workstations.

### Step A: Validating the IT Department Client Machine
1. Open the virtual hardware settings for your **IT PC** (`UserOne`). Ensure the network adapter is set to **Internal Network** with the name **`IT-Network`**.
2. Log into the machine and launch **Command Prompt**.
3. Flush old network parameters and request a new address assignment from the domain controller:
   ```cmd
   ipconfig /release
   ipconfig /renew
   ipconfig /flushdns
   ```
4. Check the results:
   ```cmd
   ipconfig /all
   ```
   *Expected Result:* The adapter interface should reflect an IP address in the `192.168.56.100+` range, complete with a Default Gateway of `192.168.56.1` and Preferred DNS of `192.168.56.10`.

### Step B: Validating the HR Department Client Machine
1. Open the virtual hardware settings for your **HR PC** (`UserTwo`). Confirm its network adapter points to **Internal Network** under the name **`HR-Network`**.
2. Execute the verification commands inside the terminal:
   ```cmd
   ipconfig /release
   ipconfig /renew
   ipconfig
   ```
   *Expected Result:* The machine should obtain an IP matching the `192.168.20.100+` range.

---

## 7. Advanced Inter-VLAN Routing (Fixing Communication Gaps)

### Architectural Problem:
If you attempt to ping the HR client (`192.168.20.x`) from the IT client (`192.168.56.x`), the connection will drop. Devices on separate subnets cannot talk to each other without a router to bridge the broadcast boundaries.

### Solution: Configure Windows Server as a LAN Router
1. On your Windows Server 2022, open **Server Manager** -> **Add roles and features**.
2. Select the **Remote Access** role -> Click **Next**.
3. Under Role Services, check the box for **Routing** (This automatically installs *Routing and Remote Access*). Click **Install**.
4. Once installation completes, open **Tools** -> **Routing and Remote Access**.
5. Right-click your server name -> Select **Configure and Enable Routing and Remote Access**.
6. Choose **Custom Configuration** -> Click **Next**.
7. Check the box for **LAN routing** -> Click **Next** -> **Finish** -> **Start service**.

The server will now route traffic between your subnets. IT and HR clients can ping each other and access shared company assets across network boundaries.
