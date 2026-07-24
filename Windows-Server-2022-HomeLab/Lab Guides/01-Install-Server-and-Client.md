# Lab Guide: Install Windows Server 2022 & Windows Client

## Objective

Deploy a Windows Server 2022 virtual machine and a Windows client virtual machine to create the foundation of an enterprise home lab.

---

## Requirements

* Windows Server 2022 ISO
* Windows 10/11 ISO
* VMware Workstation, VirtualBox, or Hyper-V
* Minimum 8 GB RAM on the host computer
* Sufficient storage space

---

## Step 1 – Create the Windows Server VM

* Create a new virtual machine.
* Attach the Windows Server 2022 ISO.
* Allocate CPU, RAM, and storage.
* Complete the Windows installation.
* Set the Administrator password.

---

## Step 2 – Initial Server Configuration

* Rename the server.
* Assign a static IP address.
* Verify network connectivity.
* Enable Remote Desktop.
* Restart the server if required.

---

## Step 3 – Create the Windows Client VM

* Create a second virtual machine.
* Attach the Windows installation ISO.
* Complete the installation.
* Create a local user account.
* Connect the client to the same virtual network as the server.

---

## Step 4 – Verify Connectivity

* Confirm both virtual machines are powered on.
* Check that both systems are on the same network.
* Test basic communication between the server and the client.

---

## Verification Checklist

* Windows Server installed successfully
* Windows client installed successfully
* Server renamed
* Static IP configured
* Client connected to the lab network
* Both systems operational

---

## Troubleshooting

### Client cannot communicate with the server

* Verify both VMs use the same virtual network.
* Check IP configuration.
* Ensure the network adapter is enabled.

### Static IP issues

* Confirm IP address, subnet mask, gateway, and DNS settings.
* Restart the network adapter if necessary.

---

## Result

The Windows Server 2022 and Windows client machines are successfully deployed and ready for enterprise service configuration.
