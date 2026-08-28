# Lab Environment

## Overview

In this project I used a small virtualized Windows domain designed to simulate an entry-level business IT environment.

The lab consists of one Windows Server domain controller and one Windows 11 client workstation. I kept the environment small so I could focus on Active Directory administration, troubleshooting, permissions, Group Policy, and common help-desk workflows.

## Architecture

| System | Operating System | Role | IP Address |
|---|---|---|---|
| LAB-DC01 | Windows Server 2025 | Domain Controller / DNS Server | 10.0.0.10 |
| LAB-PC01 | Windows 11 | Domain-Joined Workstation | 10.0.0.20 |

**Domain:** `corp.local`  
**Network:** `10.0.0.0/24`  
**DNS Server:** `10.0.0.10`  
**Hypervisor:** Hyper-V

The virtual machines communicate through an isolated Hyper-V internal virtual switch named `LAB-NETWORK`.

The lab does not use a default gateway or Internet connection because external connectivity was not required for the Active Directory and help-desk scenarios.

## Active Directory Structure

I created a custom `CORP` organizational unit with separate OUs for users, groups, and computers.

```text
corp.local
└── CORP
    ├── Users
    ├── Groups
    └── Computers
