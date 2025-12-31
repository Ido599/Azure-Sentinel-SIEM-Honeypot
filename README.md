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

## Step 1: Create a Honeypot Virtual Machine

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
In that way we ensures that once the Azure Network Security Group (NSG) allows the traffic, the guest OS does not drop the incoming packets, allowing the SIEM to capture the full scope of the attack.

## Step 2: Disable the Firewall in Virtual Machine
Configuring the internal Windows Defender Firewall was disabled across all profiles (Domain, Private, and Public)
<img width="1097" height="395" alt="image" src="https://github.com/user-attachments/assets/b6a5230a-50e1-46aa-ba10-651650e67074" />

- Go to Virtual Machines and find the honeypot VM 
- By clicking on the VM copy the IP address
- Log into the VM via Remote Desktop Protocol (RDP) w
- Accept Certificate warning
- Select NO for all Choose privacy settings for your device
- Click Start and search for "wf.msc" (Windows Defender Firewall)
- Click "Windows Defender Firewall Properties"
- Turn Firewall State OFF for Domain Profile Private Profile and Public Profile- 
- Hit Apply and Ok
- Ping VM via Host's command line to make sure it is reachable

## Step 3: Create a Log Analytics Workspace
- Search for "Log analytics workspaces"
- Select Create Log Analytics workspace
- Put it in the same resource group as VM 
- Give it a desired name 
- Add to same region (East US 2)
- Select Review + create

<img width="740" height="665" alt="image" src="https://github.com/user-attachments/assets/36dcb5e4-9fda-4992-baa6-0aeff8a71a4a" />

## Step 4:  Connect Log Analytics Workspace to Virtual Machine
- Search for "Microsoft Sentinel"
- Select the specific Log Analytics Workspace created for this project (e.g., Law-Soc-Lab-001)
- On the left-hand sidebar, scroll down to the Content management section and click on Content hub.
<img width="1403" height="541" alt="image" src="https://github.com/user-attachments/assets/2ff2faf3-59d7-4230-b9d9-6f89c4b6876e" />

 Than you will search for and install the "Windows Security Events" connector to bridge the gap between your VM and the SIEM.
  <img width="895" height="622" alt="image" src="https://github.com/user-attachments/assets/60a73973-2b55-45c4-bf1c-b822a0efc2ab" />
  <img width="1320" height="676" alt="image" src="https://github.com/user-attachments/assets/a418eaf2-5ff6-4f80-bace-28499a7ed3eb" />

## Step 5:Log Enrichment and Finding Location Data
import a spreadsheet (as a “Sentinel Watchlist”) which contains geographic information for each block of IP addresses.
Download: geoip-summarized.csv https://raw.githubusercontent.com/joshmadakor1/lognpacific-public/refs/heads/main/misc/geoip-summarized.csv 

- Open Microsoft Sentinel: Navigate to your designated Sentinel workspace.
- Configuration Menu: On the left-hand sidebar, locate the Configuration section.
- Select Watchlist: Click on the Watchlist tab.
- Create New: Click on + New (or Add new) at the top to launch the Watchlist wizard.
- Source Upload:
   - Set Source type to "Local file".
   - Set File type to "CSV file with a header (.csv)".
   = Upload: Drag and drop the geoip-summarized.csv file into the upload box.
   - SearchKey: Set the SearchKey to network to allow the SIEM to correlate IP ranges with geographical coordinates.
-Review + Create 
<img width="1542" height="727" alt="image" src="https://github.com/user-attachments/assets/89d83934-24d7-4ecf-913e-ea8a44e6ca79" />
<img width="1076" height="672" alt="image" src="https://github.com/user-attachments/assets/dc779152-f3e4-409d-9338-10e5e4c31e91" />

Observe the logs now have geographic information, so you can see where the attacks are coming from
By uploading the geoip-summarized.csv via the Watchlist wizard, we essentially create lookup table within Log Analytics (our log repository).

let GeoIPDB_FULL = _GetWatchlist("Geoip");
SecurityEvent
| where EventID == 4625
| evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, SearchKey)
| summarize FailureCount = count() by IpAddress, latitude, longitude, cityname, countryname
| project FailureCount, AttackerIp = IpAddress, latitude, longitude, city = cityname, country = countryname, friendly_location = strcat(cityname, " (", countryname, ")")

## Step 6:Map Data in Microsoft Sentinel
- Go to Microsoft Sentinel to see the Overview page and available events.
- lick on Workbooks and go to Defender Portal.
  <img width="1347" height="635" alt="image" src="https://github.com/user-attachments/assets/8c92e6a4-a86d-40bf-9769-5993b9beb4e0" />
- Add workbook then click Edit
- Remove default Workbook 
<img width="1565" height="535" alt="image" src="https://github.com/user-attachments/assets/5e0c42b1-c581-4c47-b27f-22871f5631da" />
- Click Add > Add data source + visualization
- Click Advanced Editor
- Copy/Paste the following query into the query window and Run Query
```kusto
let GeoIPDB_FULL = _GetWatchlist("Geoip");

SecurityEvent
| where EventID == 4625
| evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, SearchKey)
| summarize FailureCount = count() 
    by IpAddress, latitude, longitude, cityname, countryname
| project 
    FailureCount,
    AttackerIp = IpAddress,
    latitude,
    longitude,
    city = cityname,
    country = countryname,
    friendly_location = strcat(cityname, " (", countryname, ")")
```


