# Lab 01: Hybrid Network Design & VirtualBox Configuration

## Overview

This lab establishes the networking foundation for a hybrid Microsoft infrastructure by preparing an existing on-premises Windows Server 2022 Active Directory environment for future integration with Microsoft Azure.

A dual-network configuration was implemented using Oracle VirtualBox to separate internal domain communication from Internet connectivity. The internal network continues to provide Active Directory, DNS, DHCP, Group Policy, and file services, while a dedicated NAT adapter provides Internet access required for Azure administration, Windows Updates, PowerShell modules, and cloud connectivity.

This networking design closely resembles a small business environment where internal infrastructure remains isolated while maintaining secure outbound Internet access.

---

# Objectives

- Design a hybrid-ready network architecture
- Configure VirtualBox networking for a Windows Server lab
- Maintain Active Directory functionality
- Provide Internet access for Azure administration
- Validate internal and external connectivity
- Troubleshoot VirtualBox networking issues
- Document the environment using Microsoft infrastructure best practices

---

# Lab Environment

| Component | Configuration |
|-----------|---------------|
| Hypervisor | Oracle VirtualBox 7.2.14 |
| Domain Controller | Windows Server 2022 (DC01) |
| Client | Windows 10 (PC01) |
| Domain | contoso.local |
| Internal Network | Host-Only Adapter |
| Internet Network | NAT Adapter |

---

# Hybrid Network Architecture

## Network Design

The lab uses two separate virtual network adapters to simulate an enterprise hybrid infrastructure.

### Host-Only Network

Purpose

- Active Directory Domain Services
- DNS
- DHCP
- SMB File Sharing
- Group Policy
- Internal domain communication

Configuration

| Setting | Value |
|----------|-------|
| Network | 192.168.56.0/24 |
| Domain Controller | 192.168.56.10 |
| DNS Server | 192.168.56.10 |
| DHCP | Windows Server |

---

### NAT Network

Purpose

- Internet connectivity
- Windows Updates
- Microsoft Azure Portal
- Azure PowerShell
- Package downloads
- Cloud administration

Configuration

| Setting | Value |
|----------|-------|
| Network Type | NAT |
| IP Assignment | DHCP |
| Internet Access | Enabled |

---

# VirtualBox Configuration

## Adapter 1

| Setting | Configuration |
|----------|---------------|
| Attached To | NAT |
| Adapter Type | Intel PRO/1000 MT Server (82545EM) |
| Cable Connected | Yes |

---

## Adapter 2

| Setting | Configuration |
|----------|---------------|
| Attached To | Host-Only Adapter |
| Adapter Type | Intel PRO/1000 MT Desktop (82540EM) |
| Cable Connected | Yes |

---

# Network Topology

```text
                         Internet
                             │
                    VirtualBox NAT
                             │
           ┌─────────────────┴─────────────────┐
           │                                   │
      Windows Server 2022                 Windows 10
            DC01                             PC01
             │                                │
             └──────── Host-Only ─────────────┘
                  192.168.56.0/24

             Active Directory
             DNS
             DHCP
             SMB
             Group Policy
```

---

# IP Configuration

| Device | Host-Only IP | NAT IP |
|---------|--------------|---------|
| DC01 | 192.168.56.10 | DHCP (10.0.2.x) |
| PC01 | DHCP | DHCP |

---

# Validation

The following tests were completed after configuring both adapters.

| Test | Result |
|------|:------:|
| DC01 Internet Access | ✅ Pass |
| PC01 Internet Access | ✅ Pass |
| Active Directory | ✅ Pass |
| DNS Resolution | ✅ Pass |
| Domain Authentication | ✅ Pass |
| Internal Communication | ✅ Pass |
| Internet Connectivity | ✅ Pass |

---

# Commands Used

## Verify IP Configuration

```cmd
ipconfig /all
```

## Verify Routing

```cmd
route print
```

## Verify Internet

```cmd
ping 8.8.8.8
ping microsoft.com
```

## Verify DNS

```cmd
nslookup microsoft.com
```

## Verify Active Directory

```cmd
nltest /dsgetdc:contoso.local
```

---

# Screenshots

Include the following screenshots within this lab:

- VirtualBox Adapter 1 (NAT)
- VirtualBox Adapter 2 (Host-Only)
- DC01 ipconfig /all
- PC01 ipconfig /all
- Successful Internet connectivity
- Successful DNS resolution
- Route table after repair

---

# Troubleshooting

## Issue

After adding the NAT adapter, the Windows Server virtual machine was unable to communicate with the Internet.

### Symptoms

- APIPA address (169.254.x.x)
- No default gateway
- Failed DHCP assignment
- General failure when pinging external hosts

---

## Investigation

The following checks were completed:

- Verified NAT configuration
- Verified Host-Only configuration
- Reviewed routing table
- Verified DHCP configuration
- Verified VirtualBox cable connection
- Reviewed interface configuration
- Tested DHCP renewal

---

## Root Cause

The Intel PRO/1000 MT Desktop virtual adapter used for the NAT interface was incompatible with the Windows Server guest operating system, preventing successful communication with the VirtualBox DHCP service.

---

## Resolution

The adapter type was changed to:

**Intel PRO/1000 MT Server (82545EM)**

Following the change:

- DHCP lease successfully obtained
- Default gateway assigned
- Internet connectivity restored
- Azure connectivity verified

---

# Lessons Learned

- Windows Server virtual machines may require a different VirtualBox adapter type for reliable DHCP communication.
- Separating internal and external traffic simplifies hybrid network design.
- Maintaining an isolated Active Directory network improves security while allowing controlled Internet access.
- Validating routing, DHCP, and DNS should be part of every network deployment.

---

# Skills Demonstrated

- Hybrid Network Design
- VirtualBox Administration
- Windows Server Networking
- NAT Configuration
- Host-Only Networking
- TCP/IP Troubleshooting
- DHCP Troubleshooting
- DNS Administration
- Routing Troubleshooting
- Active Directory Networking
- Infrastructure Validation
- Technical Documentation

---

# Security Considerations

- Active Directory traffic remains isolated on the Host-Only network.
- Internet access is provided through a dedicated NAT interface.
- Internal services are not directly exposed to the physical network.
- This configuration provides a secure foundation for future hybrid Azure integration.

---

# Outcome

Successfully designed and implemented a hybrid-ready networking foundation supporting both on-premises Active Directory services and Internet connectivity required for Microsoft Azure integration.

This configuration serves as the baseline for all subsequent Azure infrastructure labs.

---

# Next Lab

**Lab 02 – Azure Subscription & Resource Group Configuration**

The next lab will establish the Azure tenant structure, create resource groups, and prepare the cloud environment for hybrid infrastructure deployment.
