# Windows Server 2022 & Client PC Lab Documentation

## Overview

This lab simulates a basic enterprise environment using one Windows Server 2022 virtual machine and one Windows client virtual machine. The environment serves as the foundation for learning Windows Server administration, Active Directory, DNS, DHCP, Group Policy, file services, and other enterprise technologies.

---

## Objectives

* Install Windows Server 2022
* Install a Windows client operating system
* Configure the initial server environment
* Prepare both machines for future domain integration
* Establish a stable lab environment for upcoming Windows Server services

---

## Lab Environment

| Component               | Details                                         |
| ----------------------- | ----------------------------------------------- |
| Server Operating System | Windows Server 2022                             |
| Client Operating System | Windows 10 / Windows 11                         |
| Virtualization Platform | VMware Workstation / VirtualBox / Hyper-V       |
| Network Type            | NAT / Host-Only (depending on lab requirements) |
| Server Role             | Standalone Server (Initial Setup)               |
| Client Role             | Workstation                                     |

---

## Virtual Machine Specifications

### Windows Server 2022

* 2 vCPUs
* 4 GB RAM (minimum)
* 60 GB Virtual Disk
* Network Adapter Enabled

### Windows Client

* 2 vCPUs
* 4 GB RAM
* 40 GB Virtual Disk
* Network Adapter Enabled

---

## Initial Configuration Completed

### Windows Server

* Installed Windows Server 2022
* Configured Administrator password
* Renamed server
* Configured static IP address
* Verified network connectivity
* Enabled Remote Desktop
* Installed latest updates (if applicable)

### Windows Client

* Installed Windows
* Configured local user account
* Connected to the same virtual network
* Verified communication with the server

---

## Current Status

The lab environment is successfully deployed and ready for the next phase, including Active Directory Domain Services (AD DS), DNS, DHCP, and domain configuration.

---

## Next Phase

* Install Active Directory Domain Services
* Promote the server to a Domain Controller
* Configure DNS
* Join the client computer to the domain
