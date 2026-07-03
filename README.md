# Azure SOC Honeypot with Attack Map & Cloud Security Hardening

> A cloud-based Security Operations Center (SOC) lab built in Microsoft Azure that demonstrates how quickly internet-facing systems become targets, how to investigate attacks using Microsoft Sentinel, and how to secure cloud infrastructure using Azure security best practices.

---

## System Architecture

<img src="assets/image 1.png" width="1400">

*Figure 1. High-level architecture of the Azure SOC Honeypot environment showing the Windows virtual machine, centralized log collection through Azure Monitor Agent, Microsoft Sentinel SIEM, GeoIP enrichment, attack visualization, and post-deployment cloud security hardening.*

---

## Project Overview

This project demonstrates the deployment of a cloud-based Security Operations Center (SOC) in Microsoft Azure. A Windows 11 virtual machine was intentionally exposed as a honeypot to collect **real-world attack data**, which was forwarded to Microsoft Sentinel for centralized monitoring and analysis.

Using Kusto Query Language (KQL), failed authentication attempts were investigated, enriched with geographic intelligence through a GeoIP watchlist, and visualized on an interactive attack map. After analyzing the collected telemetry, the environment was secured using Azure security best practices, including Network Security Group (NSG) hardening, Windows Defender Firewall, Azure Role-Based Access Control (RBAC), and Microsoft Defender for Cloud.

---

# The Problem This Solves

Internet-facing systems are continuously targeted by automated scanners and threat actors searching for vulnerable services. This project demonstrates how quickly an exposed virtual machine begins receiving real attack attempts and how Microsoft Sentinel can be used to collect, investigate, and visualize that activity.

By combining centralized logging, geographic enrichment, and cloud security hardening, the project showcases both the offensive reality of internet exposure and the defensive practices used to secure Azure workloads.

---
<br>
<details>
<summary><strong>📋 Project Summary (Quick Read) — Click to Expand</strong><br></summary>
<br>

This project demonstrates the complete lifecycle of deploying, monitoring, investigating, and securing an internet-facing Windows 11 honeypot using Microsoft Azure.

### Key Features

- Deployed a Windows 11 virtual machine as an internet-facing honeypot
- Configured an Azure Virtual Network and Network Security Group (NSG)
- Created an unrestricted inbound NSG rule to intentionally expose the VM
- Disabled Windows Defender Firewall to maximize attack visibility
- Generated failed RDP login attempts to simulate brute-force attacks
- Investigated Windows Security Events using Event Viewer
- Created a centralized Log Analytics Workspace for security log collection
- Connected Microsoft Sentinel as the cloud-native SIEM platform
- Configured Azure Monitor Agent (AMA) and Data Collection Rules (DCR)
- Queried Windows Security Events using Kusto Query Language (KQL)
- Imported a GeoIP watchlist to enrich attacker IP addresses with geographic data
- Built an interactive Microsoft Sentinel  Workbook
- Validated attacker IP reputation using VirusTotal
- Hardened the environment by:
  - Re-enabling Windows Defender Firewall
  - Restricting inbound traffic with Network Security Groups
  - Implementing Azure Role-Based Access Control (RBAC)
  - Enabling Microsoft Defender for Cloud

</details>

**[Click to skip to Attack Map](https://github.com/huzaifah-cyber/Azure-SOC-Honeypot-with-Attack-Map#build-and-analyze-the-attack-map)**

# Technologies Used

| Technology | Purpose |
|------------|---------|
| Microsoft Azure | Cloud infrastructure platform |
| Windows 11 Virtual Machine | Internet-facing honeypot |
| Azure Virtual Network | Virtual networking |
| Network Security Group (NSG) | Cloud firewall and traffic filtering |
| Azure Monitor Agent (AMA) | Log collection from the VM |
| Log Analytics Workspace | Centralized security log repository |
| Microsoft Sentinel | Cloud-native SIEM platform |
| Kusto Query Language (KQL) | Security log analysis |
| GeoIP Watchlist | Geographic enrichment of attacker IP addresses |
| Microsoft Defender for Cloud | Cloud security posture management |
| Azure Role-Based Access Control (RBAC) | Identity and access management |
| VirusTotal | Threat intelligence validation |

---

# Step 1: Infrastructure Deployment

The first phase focused on deploying the Azure infrastructure and configuring a Windows 11 virtual machine as an internet-facing honeypot.

---

## Create the Resource Group

A dedicated resource group named **RG-honeypot** was created to organize all Azure resources for the project.

<img src="assets/image 2.png" width="900">

*Figure 2. Resource group created to centrally manage all Azure resources associated with the honeypot environment.*

---

## Deploy the Virtual Network

A Virtual Network (VNet) was deployed to provide network connectivity for the Windows 11 virtual machine.

<img src="assets/image 3.png" width="900">

*Figure 3. Azure Virtual Network providing isolated network infrastructure for the honeypot virtual machine.*

---

## Deploy the Windows 11 Honeypot

A Windows 11 virtual machine with a public IP address was deployed to serve as the internet-facing honeypot.

<img src="assets/image 4.png" width="900">

*Figure 4. Windows 11 virtual machine deployed as an internet-facing honeypot.*

---

## Configure the Network Security Group

A custom NSG rule named **DANGER_AllowAnyCustomInbound** was created to allow all inbound traffic and intentionally expose the virtual machine.

<img src="assets/image 5.png" width="900">

*Figure 5. Custom NSG rule allowing unrestricted inbound traffic to intentionally expose the virtual machine.*

---

## Verify Windows Defender Firewall

Windows Defender Firewall was verified to be enabled across all network profiles.

<img src="assets/image 6.png" width="900">

*Figure 6. Windows Defender Firewall enabled immediately after the virtual machine deployment.*

---

## Test External Connectivity

A ping test confirmed that inbound traffic was blocked while Windows Defender Firewall remained enabled.

<img src="assets/image 7.png" width="900">

*Figure 7. Initial connectivity test showing that inbound ICMP requests were blocked by Windows Defender Firewall.*

---

## Disable Windows Defender Firewall

Windows Defender Firewall was disabled across all network profiles to fully expose the honeypot.

<img src="assets/image 8.png" width="900">

*Figure 8. Windows Defender Firewall disabled to maximize visibility of internet-based attack activity.*

---

## Confirm Public Accessibility

A second ping test confirmed that the virtual machine was publicly reachable.

<img src="assets/image 9.png" width="900">

*Figure 9. Successful connectivity test confirming that the honeypot is publicly accessible.*

# Step 2: Building the Security Operations Center (SOC)

After exposing the honeypot, a centralized Security Operations Center (SOC) was built using Microsoft Sentinel to collect, monitor, and investigate Windows Security Events.

---

## Generate Failed Login Attempts

Multiple failed RDP login attempts were performed using the username **Hacker** to generate authentication events.

<img src="assets/image 10.png" width="900">

*Figure 10. Multiple failed RDP login attempts generated using the username **Hacker** to create authentication events.*

---

## Investigate Windows Security Logs

Windows Event Viewer was used to verify that the failed login attempts were recorded as **Event ID 4625** entries.

<img src="assets/image 11.png" width="900">

*Figure 11. Windows Security log displaying Event ID 4625 entries generated by failed RDP login attempts.*

---

## Create the Log Analytics Workspace

A Log Analytics Workspace (LAW) was created to centrally collect and store Windows Security Events.

<img src="assets/image 12.png" width="900">

*Figure 12. Log Analytics Workspace created to centralize Windows security event collection.*

---

## Deploy Microsoft Sentinel

Microsoft Sentinel was connected to the Log Analytics Workspace to monitor and investigate security events.

<img src="assets/image 13.png" width="900">

*Figure 13. Microsoft Sentinel successfully connected to the Log Analytics Workspace.*

---

## Configure Windows Security Event Collection

The **Windows Security Events** solution was installed from the Sentinel Content Hub to enable Windows Security Event collection.

<img src="assets/image 14.png" width="900">

*Figure 14. Windows Security Events solution installed from the Sentinel Content Hub.*

---

## Configure the Data Collection Rule (DCR)

A Data Collection Rule (DCR) was created and assigned to the virtual machine to collect Windows Security Events.

<img src="assets/image 15.png" width="900">

*Figure 15. Data Collection Rule configured to collect Windows Security Events from the honeypot VM.*

---

## Verify Azure Monitor Agent Deployment

The Azure Monitor Agent (AMA) was automatically deployed, completing the log collection pipeline.

<img src="assets/image 16.png" width="900">

*Figure 16. Azure Monitor Agent successfully deployed and connected to the virtual machine.*

---

## Review the Azure Resources

The resource group was reviewed to verify that all required SOC resources had been successfully deployed.

<img src="assets/image 17.png" width="900">

*Figure 17. Azure Resource Group showing all resources deployed throughout the SOC environment.*

---

## Query Failed Login Attempts with KQL

A KQL query was used to retrieve **Event ID 4625** records from Microsoft Sentinel.

```kql
SecurityEvent
| where EventID == 4625
| project TimeGenerated, EventID, Activity, AttackerIP = IpAddress, Account, Computer
```

The query successfully returned all failed login attempts collected from the honeypot, recording **2,975 failed authentication events** during testing.

<img src="assets/image 18.png" width="900">

*Figure 18. KQL query displaying failed authentication attempts (Event ID 4625) collected by Microsoft Sentinel.*

# Step 3: Threat Intelligence & Attack Visualization

With security events flowing into Microsoft Sentinel, the collected logs were enriched with geographic data and visualized on an interactive attack map to identify the origin of attack attempts.

---

## Import the GeoIP Watchlist

A **GeoIP watchlist** containing IP network ranges and geographic information was imported into Microsoft Sentinel to enrich attacker IP addresses with location data.

<img src="assets/image 19.png" width="900">

*Figure 19. GeoIP watchlist imported into Microsoft Sentinel for geographic enrichment of attacker IP addresses.*

---

## Enrich Security Events with Geographic Data

The `ipv4_lookup()` function was used to correlate failed login events with the GeoIP watchlist, adding geographic information to each attacker IP.

```kql
let GeoIPDB_FULL = _GetWatchlist("geoip");
let WindowsEvents = SecurityEvent
| where EventID == 4625
| order by TimeGenerated desc
| evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network);

WindowsEvents
| project TimeGenerated, EventID, Activity, Computer, AttackerIP = IpAddress, cityname, countryname
```

<img src="assets/image 20.png" width="900">

*Figure 20. KQL query enriching failed login attempts with geographic information from the GeoIP watchlist.*

---

## Build and Analyze the Attack Map

A Microsoft Sentinel Workbook was created to visualize failed login attempts on an interactive world map. After leaving the honeypot online, Microsoft Sentinel recorded thousands of failed RDP login attempts originating from multiple countries. The highest number of attacks originated from **Japan**, followed by several other countries across North America, Europe, and Asia.

<img src="assets/image 21.png" width="900">

*Figure 22. Geographic distribution of failed login attempts targeting the Azure honeypot.*

---

## Validate an Attacker IP with VirusTotal

One of the observed attacker IP addresses was analyzed using **VirusTotal**. The lookup confirmed the IP had previously been identified as malicious by multiple security vendors.

<img src="assets/image 22.png" width="900">

*Figure 23. VirusTotal analysis confirming the reputation of an attacker IP observed in Microsoft Sentinel.*

---

## Key Observations

- The honeypot began receiving attack traffic shortly after being exposed to the internet.
- Thousands of failed RDP login attempts were collected and analyzed.
- GeoIP enrichment provided geographic context for attacker IP addresses.
- VirusTotal validated the reputation of observed attacker IPs.

# Step 4: Cloud Security Hardening

After analyzing the collected attack data, the environment was secured by reducing its attack surface and implementing Azure security best practices.

---

## Harden the Network Security Group (NSG)

The unrestricted inbound rule was replaced with a rule allowing only **Remote Desktop Protocol (RDP)** traffic over port **3389**, significantly reducing the attack surface while maintaining secure administrative access.

<img src="assets/image 23.png" width="900">

*Figure 24. Network Security Group hardened by replacing the unrestricted inbound rule with a controlled RDP access rule.*

---

## Implement Azure Role-Based Access Control (RBAC)

Azure RBAC was configured by assigning the **Virtual Machine User Login** role to the **JewelSales** security group, enforcing the principle of least privilege.

<img src="assets/image 24.png" width="900">

*Figure 25. Azure RBAC configured to enforce least-privilege access using the Virtual Machine User Login role.*

> **Security Benefit:** Restricts access by granting users only the permissions required to perform their assigned tasks.

---

## Enable Microsoft Defender for Cloud

Microsoft Defender for Cloud was enabled to continuously assess the security posture of the deployed Azure resources and provide security recommendations.

<img src="assets/image 25.png" width="900">

*Figure 26. Microsoft Defender for Cloud enabled to continuously assess and improve the security posture of the Azure environment.*

> **Security Benefit:** Continuously monitors Azure resources for security risks, misconfigurations, and compliance issues.

---

## Security Improvements Summary

The honeypot was intentionally deployed with minimal security controls to observe real-world attack activity. After monitoring and analysis, multiple defensive controls were implemented to restore a secure configuration.

| Security Control | Purpose |
|------------------|---------|
| Windows Defender Firewall | Restores host-level protection |
| Hardened Network Security Group | Restricts unnecessary inbound traffic |
| Azure RBAC | Enforces least-privilege access |
| Microsoft Defender for Cloud | Continuously monitors cloud security posture |

Together, these controls demonstrate the importance of a **defense-in-depth** approach for securing cloud workloads.

