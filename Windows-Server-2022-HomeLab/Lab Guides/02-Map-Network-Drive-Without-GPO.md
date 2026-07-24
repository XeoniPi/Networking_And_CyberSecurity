# Map Network Drive Without Group Policy (GPO)

## Objective

Learn how to map a shared network folder from a Windows Server 2022 machine to a Windows client without using Group Policy (GPO).

---

# What is a Mapped Network Drive?

A mapped network drive is a shared folder on a network that is assigned a drive letter (such as **Z:**, **X:**, or **D:**) on a client computer. Once mapped, the shared folder behaves like a local drive, making it easy for users to access files and folders.

Example:

```
Z: → \\SERVER22\www
```

---

# Why Map a Network Drive?

Mapping a network drive provides several benefits:

* Easy access to shared files and folders.
* Centralized file storage on the server.
* Simplified file sharing between multiple users.
* Reduced duplicate copies of files.
* Easier backup and management because files remain on the server.
* Users can access shared resources using a familiar drive letter instead of typing a UNC path every time.

---

# Why Configure It Without GPO?

In small offices, test labs, or standalone environments, Active Directory and Group Policy may not be available or necessary.

Mapping drives manually is useful for:

* Home Lab environments
* Small Business Networks
* Standalone Servers
* Testing and Learning
* Troubleshooting network shares

---

# Lab Environment

| Component     | Value                   |
| ------------- | ----------------------- |
| Server        | Windows Server 2022     |
| Client        | Windows 10 / Windows 11 |
| Shared Folder | www                     |
| Network Path  | `\\SERVER22\www`        |

---

# Configuration Steps

## Step 1 — Create a Shared Folder

On the Windows Server:

* Create a folder (Example: **D:\www**).
* Right-click the folder and select **Properties**.
* Open the **Sharing** tab.
* Click **Advanced Sharing**.
* Enable **Share this folder**.
* Configure the required share permissions (Read or Change as needed).

---

## Step 2 — Configure NTFS Permissions

Open the **Security** tab.

Grant the appropriate permissions to the users or groups that need access.

Examples:

* Read
* Modify
* Full Control (Lab only)

---

## Step 3 — Verify the Network Path

The shared folder should now have a UNC path similar to:

```
\\SERVER22\www
```

---

## Step 4 — Open Map Network Drive

On the client computer:

* Open **This PC**.
* Click **Map Network Drive**.
* Select a drive letter (Example: **Z:**).
* Enter the shared folder path:

```
\\SERVER22\www
```

* Click **Finish**.

---

## Step 5 — Authenticate

When prompted:

* Enter the server username.
* Enter the server password.
* Select **Remember my credentials** if required.
* Click **OK**.

---

## Step 6 — Verification

After successful authentication:

* The mapped drive appears in **This PC**.
* The drive can be accessed like any local disk.
* Files can be copied, created, modified, and deleted according to the assigned permissions.

---

# Result

The shared folder on Windows Server 2022 has been successfully mapped to the client computer without using Group Policy.

Users can now access shared resources directly through the assigned drive letter.

---

# Screenshots

Include screenshots for the following steps:

* Creating the shared folder
* Sharing configuration
* Share permissions
* Security (NTFS) permissions
* Network path
* Map Network Drive wizard
* Drive letter selection
* Login prompt
* Successful mapped drive
* File access from the client

---

# Best Practices

* Use meaningful shared folder names.
* Assign the minimum permissions required (Principle of Least Privilege).
* Avoid granting **Everyone – Full Control** except in temporary lab environments.
* Keep NTFS permissions and Share permissions consistent.
* Use Active Directory and Group Policy for automatic drive mapping in production environments.

---

# Lessons Learned

* Created a shared folder on Windows Server 2022.
* Configured Share and NTFS permissions.
* Mapped the shared folder on a client computer.
* Verified successful communication and file sharing.
* Understood the difference between manual drive mapping and GPO-based deployment.
