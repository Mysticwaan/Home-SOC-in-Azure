# 🛡️ Home SOC in Azure: Threat Detection with Microsoft Sentinel
![253ac51c-07eb-4a76-9aad-90e5f08489b5](https://github.com/user-attachments/assets/8b5b07d8-5a38-4593-9daa-aa4cac8f1a95)

This project demonstrates the deployment of a **deliberately vulnerable Windows virtual machine** in Microsoft Azure for the purpose of **observing real-world brute-force attacks**. The VM sends logs to Microsoft Sentinel via Log Analytics, and login failure data is enriched with geolocation information to visualize the attack sources on a global map.

![image](https://github.com/user-attachments/assets/ff2c1c0e-e3ff-4635-87d0-ef0a4d7273ef)

---

## 🔐 Part 1: Deploying a Vulnerable Windows VM Honeypot

### 🧱 Step 1: Create the Azure Infrastructure

1. **Create a Resource Group**
   - Open Azure Portal → "Create a resource" → "Resource Group"
   - Choose a name like `RG-SOC-Lab` and region (e.g., East US 2)
     
![image](https://github.com/user-attachments/assets/938fd769-f2d0-415e-9a72-6e41bed1c567)

2. **Set Up Virtual Network**
   - Create a VNet with a new subnet (e.g., `Vnet-soc-lab`)

![image](https://github.com/user-attachments/assets/1526ab82-d6ad-4b5c-b678-eb1dcad68359)


3. **Deploy a Windows VM**
   - Go to "Create a resource" → "Windows Server 2022 Datacenter"
   - Basic config:
     - VM name: `CORP-NET-TEAST-1`
     - Username: use a decoy (e.g., `labuser`)
     - Password: weak but still valid (e.g., `P@ssw0rd123`)
   - Public Inbound Ports: Allow RDP (port 3389)
   - Network: Use your previously created VNet and subnet
  
 ![image](https://github.com/user-attachments/assets/cfa0a427-d2a8-409a-93fc-86065e89bd9a)

 ![image](https://github.com/user-attachments/assets/10eff5cb-4fc5-4c20-8761-89af73337c07)


4. **Disable the Internal Firewall**
   - In the VM:
     - Run `wf.msc` command to open the settings
     - Turn off Windows Defender Firewall for all profiles
     - 
![image](https://github.com/user-attachments/assets/3c980cde-af78-4651-a1fe-77d288d22ffc)
![image](https://github.com/user-attachments/assets/706b7f36-aa07-4715-83b5-a49d42d8382e)


> ⚠️ **Warning:** This VM is intentionally insecure. Do not use real credentials. Monitor billing and stop/delete resources when finished.

---

### 📈 Step 2: Set Up Log Analytics
![image](https://github.com/user-attachments/assets/3f880197-81fd-42ec-a221-16130aec36cd)
![image](https://github.com/user-attachments/assets/32c01519-2f18-4b0e-9ac8-9f19a1e30e74)


1. **Create Log Analytics Workspace**
   - Go to "Monitor" → "Logs" → "Create Log Analytics Workspace"
   - Name it `law-soc-lab-0000` or similar
     
  ![image](https://github.com/user-attachments/assets/f28d6200-819b-412d-9222-e4d23367368e)
  ![image](https://github.com/user-attachments/assets/e83a7b8c-8d16-4519-a371-7dda22f8ebb5)

     

2. **Install Azure Monitor Agent**
   - Go to the VM → "Extensions + Applications"
   - Add "Azure Monitor Agent"
   - Connect it to your Log Analytics Workspace

3. **Enable Data Collection Rules**
   - Under "Monitoring" → "Data Collection Rules"
   - Add a new rule to collect **Security Events** from your VM
  
     ![image](https://github.com/user-attachments/assets/e28f3762-bab5-4dc7-8f86-ec6a2c408a01)
     ![image](https://github.com/user-attachments/assets/02ac16f9-cdc4-4bb6-887f-3ca1898049a4)
     SecurityEvent
|   where EventID == 4625
|   where TimeGenerated > ago(1m)
|   project TimeGenerated, Account, Computer, EventID, Activity, IpAddress
shows   158 login attempts in the last minute: 
     ![image](https://github.com/user-attachments/assets/e28720f9-3fbb-405e-971d-651160900e64)
Getting specific data using these table commands:
let GeoIPDB_FULL = _GetWatchlist("geoip");
let WindowsEvents = SecurityEvent
    | where IpAddress == "185.243.96.116"
    | where EventID == 4625
    | order by TimeGenerated desc
    | evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network);
WindowsEvents
| project TimeGenerated, Computer, AttackerIp = cityname, countryname, latitude, longitude
![image](https://github.com/user-attachments/assets/f51a8c3e-8cac-408c-ac9a-49a7fcbe432c)

---

### 🧪 Step 3: Test Logging in Log Analytics

After a few minutes of uptime, try RDP brute-forcing the machine yourself or wait for real-world bots.

#### 🔍 Run this KQL query:

```kql
SecurityEvent
| where EventID == 4625
```
![image](https://github.com/user-attachments/assets/6935df96-e8a7-42f6-925b-74e082b61438)

This shows failed login attempts. Look for entries with `EventID 4625`, which indicate failed logins.

You can also run:

```kql
SecurityEvent
| where EventID == 4625
| summarize Count = count() by Account
```
![image](https://github.com/user-attachments/assets/9be4f14d-8969-4ca7-967f-99a18367c106)

---

## 🔍 : Enriching Logs with Geolocation + Sentinel Map

![image](https://github.com/user-attachments/assets/6be99ecb-e6be-4ec5-b3c7-2d6a9d861e1b)

![image](https://github.com/user-attachments/assets/477f4a82-d193-4fd4-bcef-81155d066b4f)

## 🧠 Lessons Learned

- **Honeypot VMs** are quickly scanned and attacked by global bots once exposed to the internet.
- **Microsoft Sentinel** can serve as a full SIEM solution with log ingestion, enrichment, alerting, and visualization.
- **KQL (Kusto Query Language)** is powerful for filtering and analyzing security data.
- **Geolocation enrichment** provides critical insight into attacker origins and behaviors.

---
