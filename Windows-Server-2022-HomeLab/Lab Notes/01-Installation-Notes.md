# Windows Server 2022 Installation Notes

## Purpose

This lab establishes the base infrastructure for all future Windows Server administration exercises.

---

## Key Concepts

### Windows Server

Windows Server is Microsoft's enterprise operating system designed to provide centralized management, authentication, networking, storage, and security services.

---

### Client Computer

The client computer represents an end-user workstation that will later join the Active Directory domain.

---

## Why Use a Static IP on the Server?

A server should maintain a consistent IP address because services such as Active Directory, DNS, and DHCP depend on predictable network communication.

---

## Why Rename the Server?

A meaningful computer name improves administration, identification, and troubleshooting in enterprise environments.

Example:

* SRV-DC01
* SRV-FILE01
* SRV-WEB01

---

## Best Practices

* Use descriptive computer names.
* Assign a static IP address before installing server roles.
* Keep the operating system updated.
* Record all configuration changes.
* Take a virtual machine snapshot before major changes.

---

## Lessons Learned

* Successfully deployed a Windows Server 2022 virtual machine.
* Successfully deployed a Windows client virtual machine.
* Configured the initial lab environment.
* Prepared the infrastructure for Active Directory deployment.

---

## Next Learning Goals

* Active Directory Domain Services (AD DS)
* Domain Controller Promotion
* DNS Configuration
* DHCP Configuration
* Group Policy
* Domain Join
* User and Group Management
