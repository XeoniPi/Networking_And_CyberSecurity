# Troubleshooting: Unable to Ping Between Windows Server and Client PCs

## Issue

After installing Windows Server 2022 and two Windows client virtual machines, all systems were connected to the same virtual network using:

* NAT Adapter
* Host-Only Adapter

Although the network configuration was correct, the virtual machines could not communicate with each other.

### Symptoms

* `ping` returned **Request timed out**.
* Communication worked through the NAT adapter but failed on the Host-Only network.
* IP configuration was correct on all machines.

---

## Root Cause

The Windows Defender Firewall blocks incoming ICMP Echo Requests (Ping) by default. As a result, devices on the Host-Only network could not respond to ping requests even though they were on the same subnet.

---

## Solution

### Option 1 (Recommended for Lab)

Allow incoming ICMP Echo Requests while keeping Windows Firewall enabled.

```cmd
netsh advfirewall firewall add rule name="Allow ICMP" protocol=icmpv4:8,any dir=in action=allow
```

This command creates a firewall rule that allows ICMPv4 Echo Requests (Ping).

---

### Option 2 (Lab Only)

Temporarily disable Windows Firewall for testing purposes.

```cmd
netsh advfirewall set allprofiles state off
```

After disabling the firewall, all virtual machines were able to communicate successfully using the `ping` command.

---

## Re-enable Windows Firewall

After completing testing, enable Windows Firewall again.

```cmd
netsh advfirewall set allprofiles state on
```

---

## Verification

Run the following command from each machine:

```cmd
ping <Target-IP>
```

Expected result:

```text
Reply from <Target-IP>: bytes=32 time<1ms TTL=128
```

Successful replies confirm that communication between the Windows Server and client PCs has been established.

---

## Lessons Learned

* Correct IP addressing alone does not guarantee connectivity.
* Windows Defender Firewall blocks ICMP Echo Requests by default.
* Allowing ICMP traffic is a better solution than disabling the firewall entirely.
* Disabling the firewall should only be used temporarily in a controlled lab environment.

---

## Best Practice

✔ Keep Windows Firewall enabled whenever possible.

✔ Create only the required firewall rules instead of disabling the firewall.

✔ Re-enable the firewall immediately after testing is complete.

---

## Lab Status

* Windows Server 2022 installed
* Windows Client PCs installed
* NAT and Host-Only network configured
* ICMP communication verified
* Lab network ready for Active Directory deployment
