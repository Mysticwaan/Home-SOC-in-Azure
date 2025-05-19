# 🛡️ Home SOC in Azure: Threat Detection with Microsoft Sentinel

This project documents my setup of a basic **Security Operations Center (SOC)** in Azure using Microsoft Sentinel, Kusto Query Language (KQL), and a honeypot virtual machine (VM). I built this lab to explore threat detection, log analytics, and attack visualization from real-world telemetry.

---

## ✅ What I Did

### 🆓 Created Free Azure Subscription

- Set up a [free Azure account](https://azure.microsoft.com/free/)
- Verified access to key services like VMs, Log Analytics, and Microsoft Sentinel

### 💻 Created Virtual Machine

- Deployed a Windows Server VM in Azure
- Opened RDP and other ports to the public internet to attract attack attempts
- Installed Sysmon for enhanced event logging and telemetry

### 📄 Viewed Raw Logs on the VM

- Inspected Event Viewer to monitor authentication attempts and system activity
- Verified that Sysmon and event forwarding were operational

### 📦 Created Log Analytics Workspace

- Created a Log Analytics Workspace to centralize and analyze logs
- Configured data retention and workspace settings

### 🔗 Connected VM to Log Analytics

- Installed the Microsoft Monitoring Agent (MMA) on the VM
- Linked the VM to the workspace for real-time log forwarding

### 🔍 Queried Logs with KQL

- Used **Kusto Query Language (KQL)** to search for failed logins and suspicious events
- Filtered for specific Event IDs and extracted attacker IPs

### 🌍 Uploaded Geolocation Data

- Imported a custom IP-to-country geolocation CSV dataset
- Enriched log data by joining with attacker IPs

### 🛰️ Inspected Enriched Logs

- Analyzed login failures alongside geolocation to identify attack patterns
- Created visual filters by region, frequency, and IP address

### 🗺️ Built a Live Attack Map

- Used **Azure Workbooks** to create a real-time map of attacker activity
- Displayed origin countries of brute-force attempts targeting the VM

### 🚨 Created Incidents and Detection Rules

- Configured Microsoft Sentinel to generate incidents for common brute-force attacks
- Set up alert rules to monitor and triage threats
- Simulated real-world SOC workflows with incident review and response

---

## 🧠 Lessons Learned

- Cloud-based SOCs can be built at low cost using Microsoft tools
- Exposing VMs to the internet provides realistic attack data quickly
- KQL is a powerful language for parsing and investigating log data
- Microsoft Sentinel enables threat detection, enrichment, and alerting from a single dashboard

---

## 🧰 Tools Used

- **Azure Virtual Machines**
- **Log Analytics Workspace**
- **Microsoft Sentinel**
- **Sysmon**
- **Microsoft Monitoring Agent**
- **Kusto Query Language (KQL)**
- **Azure Workbooks**

---

## 🧭 Next Steps

- Configure alert rules for more advanced attacker techniques (e.g. persistence, lateral movement)
- Integrate additional data sources like Defender for Endpoint or Office 365
- Use automation rules to trigger responses to specific threats
- Explore Microsoft Defender XDR integration

---

## 📸 Screenshots (Optional)

> _Add screenshots of your attack map, Sentinel dashboard, or KQL queries here._

---

## 📚 Resources

- [Microsoft Sentinel Documentation](https://learn.microsoft.com/en-us/azure/sentinel/)
- [KQL Query Language Reference](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/)
- [Azure Workbooks Overview](https://learn.microsoft.com/en-us/azure/azure-monitor/workbooks-overview)
