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

<img width="1086" height="642" alt="image" src="https://github.com/user-attachments/assets/2bd1dc69-7e96-418c-acd2-91fb05e6ce6c" />


<img width="762" height="457" alt="image" src="https://github.com/user-attachments/assets/502c55e7-288d-471d-b051-ca7d862eb90f" />

**Administrator account**

- Create a username and password for virtual machine  NOTE: These credentials will be used to log into the virtual machine

**Inbound port rules**

- Public inbound ports: Allow RDP (3389)

**Disks**:

- Leave all defaults
- Select Next : Networking >

 **Networking**:
 
Virtual Network (VNet) Creation
<img width="755" height="552" alt="image" src="https://github.com/user-attachments/assets/8e67acc8-aa79-4fb5-8116-70ca0c47ff4d" />

**Network interface**
Network Security Group (NSG): Configured inbound and outbound security rules to manage
traffic flow and intentionally expose the VM to the public internet for attack collection.

- Remove Inbound rules (1000: default-allow-rdp) by clicking three dots
- Add an inbound rule
- Destination port ranges: * 
- Protocol: Any
- Action: Allow
- Priority: 100 (low)
- Name: Anything (ALLOW_ALL_INBOUND)

<img width="1607" height="362" alt="image" src="https://github.com/user-attachments/assets/785ef61c-4c22-4a4b-82fe-22cd91d8b309" />
<img width="1616" height="666" alt="image" src="https://github.com/user-attachments/assets/23724082-efed-4c07-9879-d4eeaf6e0683" />


Configuring the internal Windows Defender Firewall was disabled across all profiles (Domain, Private, and Public)
<img width="1097" height="395" alt="image" src="https://github.com/user-attachments/assets/b6a5230a-50e1-46aa-ba10-651650e67074" />

In that way we ensures that once the Azure Network Security Group (NSG) allows the traffic, the guest OS does not drop the incoming packets, allowing the SIEM to capture the full scope of the attack.

