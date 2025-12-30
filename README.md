# Azure-Sentinel-SIEM-Honeypot


## Objective
[Brief Objective - Remove this afterwards]

SIEM It is a tool that collects event log data from a range of sources within a network such as Firewalls, IDS/IPS, Identity solutions, etc. This allows the security professionals to monitor, prioritize and remediate potential threats in real-time. A honeypot is a security mechanism that creates a virtual trap in a controlled and safe environment to lure attackers. An intentionally compromised computer system to study how attackers work, examine different types of threats and improve security policies. This lab's purpose is to understand how to collect honeypot attack log data, query it in a SIEM and display it in a manner that is easy to understand such data. In this case it will be displayed in a world map by event count and geolocation.

### Skills Learned
[Bullet Points - Remove this afterwards]

- Hands-on experience and working knowledge of a SIEM Log Management Tool (Microsoft's Azure Sentinel).
- Configuration & Deployment of Azure resources such as virtual machines, Log Analytics Workspaces, and Azure Sentinel.
- Understand Windows Security Event logs.
- Display attack data on a dashboard with Workbooks (World Map).
- Utilization of KQL to query logs.

### Tools Used
[Bullet Points - Remove this afterwards]

- Azure Sentinel.Deployed for cloud-native security information and event management, log ingestion, and threat monitoring.
- Log Analytics Workspaces: Configured to aggregate and store security logs from multiple data sources.
- Remote Desktop Protocol (RDP). Examined Remote Desktop Protocol (RDP) traffic patterns and used network monitoring techniques to identify brute-force attempts.
- geoip-summarized file -Integrated IP-to-Geolocation (geoip-summarized) data to map and visualize attack origins.
- KQL (Kusto Query Language): Utilized for advanced log querying, data parsing, and analyzing security events.


## Architecture:

<img width="1408" height="716" alt="PUBLIC INTERNET" src="https://github.com/user-attachments/assets/65d2c071-b24a-44f9-94ae-ad09eef31ed1" />

## Steps

Step 1: Create a Honeypot Virtual Machine

Exposed Windows VM:
<img width="1387" height="662" alt="image" src="https://github.com/user-attachments/assets/3dbb3f48-f0e8-4301-a188-cee5a6976861" />

- Go to portal.azure.com  
- Search for "virtual machines"  
- Create > Azure virtual machine

**Instance details**

- Name your VM (honeypot-vm)  
- Select a recommended region ((US) East US 2)  
- Availability options: No infrastructure redundancy required  
- Security type: Standard  
- Image: Windows 10 Pro, version 21H2 - x62 Gen2  
- VM Architecture: x64  
- Size: Default is okay (Standard_D2s_v3 - 2vcpus, 8 GiB memory)

