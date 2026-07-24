# Lab Guide: DNS Server Installation, Configuration, and Client Testing

This guide covers the end-to-end process of setting up a Domain Name System (DNS) on Windows Server 2022, hosting a local company website via IIS, and configuring a client machine to access it.

---

## 1. What is DNS in Windows Server 2022?
The **Domain Name System (DNS)** is the phonebook of the internet/intranet. It resolves human-readable hostnames (like `server22.xeonipi.com`) into computer-readable IP addresses (like `192.168.56.103`). In Windows Server, DNS is foundational for Active Directory Domain Services (AD DS) locating domain controllers and network services.

---

## 2. DNS Installation in Server 2022

1. Open **Server Manager** -> Click **Add roles and features**.
2. Select **Role-based or feature-based installation** -> Select your server from the pool.
3. Check the box for **DNS Server**. (Click **Add Features** if a popup appears).
4. Click **Next** until you reach the confirmation screen, then click **Install**.
5. Once complete, click **Close**.

---

## 3. DNS Configuration (Forward Lookup Zone)

To map names to IPs, you must create a zone database.

1. Go to **Server Manager** -> **Tools** -> **DNS**.
2. Expand your server name, right-click **Forward Lookup Zones**, and select **New Zone...**.
3. Choose **Primary zone** -> Click **Next**.
4. **Zone name**: Enter your domain name (e.g., `xeonipi.com`) -> Click **Next**.
5. Choose **Create a new file with this file name** -> Click **Next**.
6. Select **Do not allow dynamic updates** (or allow them if configuring AD DS later) -> Click **Next** -> **Finish**.

---

## 4. Manage Domain (Zone) Name & 5. Add a Company to the Database

To point a specific address like `server22.xeonipi.com` to your server's IP:

1. In the DNS Manager, click your newly created zone (`xeonipi.com`).
2. Right-click an empty space in the right pane and select **New Host (A or AAAA)...**.
3. **Name**: Type `server22` (The full domain will preview as `server22.xeonipi.com`).
4. **IP address**: Enter your static server IP `192.168.56.103`.
5. Click **Add Host**, then click **OK**.

---

## 6. How to Create a Company Website (IIS Setup)

To test if your DNS record works, host a basic web page using Internet Information Services (IIS).

### Step A: Install IIS Role
1. Go to **Server Manager** -> **Add roles and features**.
2. Select **Web Server (IIS)** and install it using default options.

### Step B: Open Firewall Port
Run this command in the Server's **Command Prompt (Admin)** to allow web traffic past the firewall:
```cmd
netsh advfirewall firewall add rule name="IIS HTTP" dir=in action=allow protocol=TCP localport=80
```

### Step C: Verify Local Directory
IIS automatically hosts a default page at `C:\inetpub\wwwroot`. You can edit `iisstart.htm` or place your own custom index file here.

---

## 7. Client-Side DNS Configuration

If your client computer cannot resolve your website name, it is because it doesn't know your DNS server exists. You must change the client's network adapter settings.

1. On the **Client VM**, open **Control Panel** -> **Network and Internet** -> **Network Connections**.
2. Right-click your network adapter (Host-Only) and select **Properties**.
3. Select **Internet Protocol Version 4 (TCP/IPv4)** -> Click **Properties**.
4. Select **Use the following DNS server addresses**:
   * **Preferred DNS server**: `192.168.56.103` *(Your Windows Server IP)*
   * **Alternate DNS server**: *Leave Blank*
5. Click **OK** -> **Close**.

---

## 8. Troubleshooting & Opening the Website on Client Side

### Step A: Run Diagnostic Tests
Open **Command Prompt** on the **Client VM** and execute the following checks:

1. **DNS Resolution Check:**
   ```cmd
   nslookup server22.xeonipi.com
   ```
   *Expected Result:* It must return your Server IP (`192.168.56.103`). If it fails, your Client DNS settings are incorrect.

2. **Network Connectivity Check:**
   ```cmd
   ping server22.xeonipi.com
   ```
   *Expected Result:* You should see successful replies from the server IP.

### Step B: Access the Site via Browser
Open your browser on the client machine and type:

```cmd
http://server22.xeonipi.com
```

###⚠️ **Crucial Pitfalls to Avoid:**
* **Do NOT use HTTPS:** Since you haven't installed an SSL certificate yet, using `https://server22.xeonipi.com` will result in a `ERR_CONNECTION_REFUSED` error. Make sure to use **`http://`** explicitely.