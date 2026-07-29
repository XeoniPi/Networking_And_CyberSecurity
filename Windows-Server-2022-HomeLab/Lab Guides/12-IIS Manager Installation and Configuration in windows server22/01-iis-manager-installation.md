# Lab Guide: Internet Information Services (IIS) Installation & Verification

This guide explains how to install and manage the **Internet Information Services (IIS) Manager** on Windows Server 2022. It covers the Graphical User Interface (GUI) installation process, verification via PowerShell, and accessing the default website.

---

## 1. What is IIS Manager?
**Internet Information Services (IIS) Manager** is a Graphical User Interface (GUI) tool used to control and administer the Microsoft web server software. 

### Core Management Capabilities:
* **Website Provisioning:** Easily host, configure, and manage multiple websites on a single server.
* **Application Configuration:** Set up web applications, manage application pools, and configure execution environments.
* **Server Monitoring & Security:** Monitor web server performance, audit access logs, configure SSL certificates, and set access permissions.

---

## 2. Step-by-Step IIS Installation via Server Manager

Follow these steps to deploy the web server role using the standard Windows interface:

1. Open **Server Manager** -> Click **Add roles and features**.
2. Select **Role-based or feature-based installation** -> Choose your target server from the pool.
3. Scroll down the roles list and check the box for **Web Server (IIS)**.
4. A popup window will appear for required management tools. Click **Add Features** to confirm.
5. Click **Next** through the features window.
6. Click **Next** through the default Web Server Role (IIS) services window.
7. Click **Install** on the final confirmation screen and wait for the wizard to finish.

---

## 3. PowerShell Verification (Checking Installed Web Services)

To verify the installation and audit exactly which sub-components and web modules are active on the operating system, open **PowerShell (Admin)** and execute the following query command:

```powershell
Get-WindowsFeature web* | Where-Object {$_.InstallState -eq "Installed"}
```

### Expected Output Structure:
The console will display a clean list of all running web sub-features, confirming that the base server framework and management GUI consoles are properly installed in the environment:

```text
Display Name                                            Name                       Install State
------------                                            ----                       -------------
Web Server (IIS)                                        Web-Server                     Installed
[X] Web Server                                          Web-WebServer                  Installed
[X] Common HTTP Features                                Web-Common-Http                Installed
[X] Default Document                                    Web-Default-Doc                Installed
...
[X] IIS Management Console                              Web-Mgmt-Console               Installed
```

---

## 4. Opening and Testing the Default Web Interface

Once the files are fully installed, you can use the graphical dashboard to browse the automated placeholder page:

1. In Server Manager, click **Tools** at the top right -> Select **Internet Information Services (IIS) Manager**.
2. In the left-hand **Connections** navigation pane, expand your Server Name node.
3. Expand the **Sites** folder directory container.
4. Click on the pre-built **Default Web Site** item.
5. Look at the far right **Actions** menu pane under the **Browse Website** section:
   * Click **Browse *:80 (http)**.
6. **Expected Result:** A web browser will open automatically and display the default Microsoft IIS blueprint screen. This confirms that your host network adapter is actively listening to and processing local web requests on port 80.
