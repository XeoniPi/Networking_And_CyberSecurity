# Lab Guide: Configuring DNS Alias Name (CNAME) Records

This guide explains what a Canonical Name (CNAME) record is, why it is used in corporate environments, and how to configure it on Windows Server 2022 to point multiple names to your web server.

---

## 1. What is an Alias Name (CNAME) Record?
A **Canonical Name (CNAME)** record is a type of DNS record that maps an **alias name** to a true or **canonical domain name (A Record)**. 

Instead of pointing a name directly to an IP address, a CNAME record points a name to another name. 

### Why Use CNAME?
* **Hide the Primary Hostname:** It masks the real identity/name of your actual server (`://xeonipi.com`) from public users.
* **Multiple Names, One IP:** You can have multiple services (`://xeonipi.com`, `://xeonipi.com`, `://xeonipi.com`) all redirecting to the same main server record without creating separate A records for each.
* **Easy Maintenance:** If your server's IP address changes, you only need to update the single **A Record**. All associated **CNAME records** will automatically point to the new IP.

---

## 2. Real-World Scenario
In our homelab setup, we already have a Host (A) record mapping:
* **True Hostname (A Record):** `://xeonipi.com` ➔ `192.168.56.103`

We want users to access our company website using the standard prefix `://xeonipi.com` instead of typing `server`. We will create a CNAME record to achieve this.

---

## 3. Step-by-Step CNAME Configuration

1. Open **Server Manager** -> Click **Tools** -> Select **DNS**.
2. Expand your server tree and expand the **Forward Lookup Zones** folder.
3. Click on your primary domain zone (e.g., `xeonipi.com`).
4. Right-click inside an empty space in the right pane and select **New Alias (CNAME)...**.
5. Configure the following settings in the dialog box:
   * **Alias name (uses parent domain if blank):** Type `www`
   * **Fully qualified domain name (FQDN) for target host:** Type `://xeonipi.com` *(or click **Browse** to navigate to your existing A record)*.

   *The interface will automatically combine the fields to show that `://xeonipi.com` points directly to `://xeonipi.com`.*

6. Click **OK** to save the record.

---

## 4. Client-Side Verification

To ensure the client computer can resolve your new alias name, perform the following validation steps.

### Step A: Clear Client DNS Cache
Before testing, flush the old DNS resolver cache on your **Client VM** by opening the **Command Prompt** and typing:
```cmd
ipconfig /flushdns
```

### Step B: Run Diagnostic Tests
Run the lookups directly against your new alias:

1. **CNAME Resolution Check:**
   ```cmd
   nslookup ://xeonipi.com
   ```
   *Expected Result:* The output will display an **Alias** line showing `://xeonipi.com` pointing to `://xeonipi.com`, along with your primary server IP (`192.168.56.103`).

2. **Ping Test:**
   ```cmd
   ping www.xeonipi.com
   ```
   *Expected Result:* The client should successfully receive reply packets from `192.168.56.103`.

### Step C: Open Website via Alias
Open your web browser on the client machine and enter the alias URL:


---
##*(Remember to use explicit **http** instead of **https** since SSL certificates are not configured yet).*