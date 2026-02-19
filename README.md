# Active-directory-SIEM-Lab
Simulated Active Directory environment with centralized log collection and SIEM integration for security monitoring


Active Directory + SIEM Lab (WinRM Log Collection)

Project Overview

This lab simulates a real-world Windows environment with centralized log collection for security monitoring.

Objectives
	1.	Deploy an Active Directory domain
	2.	Configure a Domain-Joined client machine
	3.	Enable remote log access using WinRM
	4.	Prepare the environment for SIEM ingestion (Splunk Enterprise)

This project focuses on infrastructure design and remote management configuration before detection engineering.

⸻

Virtual Machines Used

Domain Controller

Windows Server 2022
	•	ADDS
	•	DNS

Client Machines

Windows 10 x64 (Domain-Joined Endpoints)
	•	One normal domain user
	•	One domain administrator

Attacker Machine

Kali Linux
	•	Used to simulate attack activity

SIEM Server

Windows Server
	•	Splunk Enterprise

⸻

Network Configuration
	•	Hypervisor: VMware
	•	Network Mode: NAT
	•	Internal communication between all lab machines
	•	DNS configured to point to Domain Controller

⸻

Active Directory Installation

Install AD DS Role
	•	Installed Active Directory Domain Services role
	•	Promoted server to Domain Controller
	•	Created new forest (SITO.local)
	•	DNS installed automatically during promotion

⸻

Domain Configuration
	•	Verified DNS resolution
	•	Confirmed domain health
	•	Created test domain users
	•	Assigned administrative privileges where required

⸻

Client & Attacker Machine Domain Join / Interaction
	•	Configured client DNS to point to DC
	•	Verified network connectivity (ping, nslookup)
	•	Joined Windows 10 and Kali machine to domain (SSSD for Kali)
	•	Logged into Windows using domain credentials
	•	One Domain Admin
	•	One Normal User

⸻

Pre-SIEM Validation
	•	Verified IP addressing
	•	Confirmed NAT functionality
	•	Tested DC ↔ Client communication
	•	Ensured firewall rules allowed remote management

This step ensured stability before introducing log collection.

⸻

WMI Configuration & Splunk Authentication Summary

Objective

Enable WMI-based log collection and ensure Splunk properly authenticates within a domain environment for monitoring.

⸻

WMI & Remote Management Configuration

Before log ingestion and testing:
	•	Verified WinRM was enabled and functioning
	•	Confirmed PowerShell remoting (Enter-PSSession) worked
	•	Ensured WMI-related ports were accessible
	•	Validated remote connectivity between systems

This confirmed remote management protocols were operational before integrating Splunk.

⸻

Splunk Service Account Configuration (Critical Step)

By default, Splunk runs under the Local System account.

In a domain environment, this creates an authentication issue:
	•	Local System authenticates as the machine account
	•	Machine accounts lack sufficient privileges for domain-level WMI queries
	•	Even if WMI ports are open, authentication may fail or return incomplete results

⸻

Resolution

Using:

services.msc

The Splunk Enterprise service was modified:
	•	Navigated to Log On tab
	•	Changed from Local System account
	•	Configured to run under Domain Administrator account
	•	Restarted the service

After restarting, Splunk successfully authenticated against domain systems.

⸻

Log Validation in Splunk

After reconfiguring the service account: 

index=*
EventCode=4625


Event 4625 was generated using simulated failed login attempts from the Kali attacker machine.)

This confirmed:
	•	Domain authentication events were being ingested
	•	WMI-related activity was observable
	•	Log searches could be refined per user or activity type

⸻

Key Takeaway

In domain environments, enabling WMI and WinRM alone is insufficient.

Splunk must run under a domain account with appropriate privileges. Otherwise, authentication defaults to the machine account and WMI log collection fails despite correct network configuration.