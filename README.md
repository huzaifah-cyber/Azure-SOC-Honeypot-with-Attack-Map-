# Azure SOC Honeypot with Attack Map & Cloud Security Hardening

> A cloud-based Security Operations Center (SOC) lab built in Microsoft Azure that demonstrates how quickly internet-facing systems become targets, how to investigate attacks using Microsoft Sentinel, and how to secure cloud infrastructure using Azure security best practices.

---

## System Architecture

<img src="assets/image 1.png" width="1400">

*Figure 1. High-level architecture of the Azure SOC Honeypot environment showing the Windows virtual machine, centralized log collection through Azure Monitor Agent, Microsoft Sentinel SIEM, GeoIP enrichment, attack visualization, and post-deployment cloud security hardening.*

---

# Project Overview

## Project Overview

This project demonstrates the deployment of a cloud-based Security Operations Center (SOC) in Microsoft Azure. A Windows 11 virtual machine was intentionally exposed as a honeypot to collect real-world attack data, which was forwarded to Microsoft Sentinel for centralized monitoring and analysis.

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
- Built an interactive Microsoft Sentinel Attack Map Workbook
- Validated attacker IP reputation using VirusTotal
- Hardened the environment by:
  - Re-enabling Windows Defender Firewall
  - Restricting inbound traffic with Network Security Groups
  - Implementing Azure Role-Based Access Control (RBAC)
  - Enabling Microsoft Defender for Cloud

</details>

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

The first phase of the project focused on deploying the Azure infrastructure required to host the honeypot. A dedicated resource group was created to organize all cloud resources, followed by the deployment of a virtual network and a Windows 11 virtual machine. To intentionally expose the system to internet-based attacks, the Network Security Group (NSG) was configured to allow unrestricted inbound traffic, and Windows Defender Firewall was disabled after deployment.

---

## Create the Resource Group

A dedicated Azure Resource Group named **RG-honeypot** was created to logically organize all resources used throughout the project, including the virtual machine, virtual network, Log Analytics Workspace, Microsoft Sentinel, and supporting networking components.

<img src="assets/image 2.png" width="900">

*Figure 2. Resource group created to centrally manage all Azure resources associated with the honeypot environment.*

---

## Deploy the Virtual Network

A Virtual Network (VNet) was deployed to provide network connectivity for the Windows virtual machine. The VM was placed inside its own subnet, allowing Azure networking components such as the Network Security Group to control inbound and outbound traffic.

<img src="assets/image 3.png" width="900">

*Figure 3. Azure Virtual Network providing isolated network infrastructure for the honeypot virtual machine.*

---

## Deploy the Windows 11 Honeypot

A Windows 11 virtual machine was deployed with a public IP address, allowing it to be accessed directly from the internet. The VM serves as the honeypot, intentionally exposed to attract automated scanners and brute-force attacks targeting Remote Desktop Protocol (RDP).

<img src="assets/image 4.png" width="900">

*Figure 4. Windows 11 virtual machine deployed as an internet-facing honeypot.*

---

## Configure the Network Security Group

To maximize the visibility of internet-based attacks, the default inbound security rules were modified by creating a custom rule named **DANGER-AllowAnyCustomInbound**.

This rule permits **all inbound traffic from any source on any port**, intentionally removing network-level protection so that attackers can freely interact with the virtual machine. Although this configuration is highly insecure and would never be implemented in a production environment, it is ideal for demonstrating how quickly publicly exposed systems begin receiving malicious traffic.

<img src="assets/image 5.png" width="900">

*Figure 5. Custom NSG rule allowing unrestricted inbound traffic to intentionally expose the virtual machine.*

---

## Verify Windows Defender Firewall

After connecting to the virtual machine through Remote Desktop Protocol (RDP), Windows Defender Firewall was inspected to confirm that it was enabled across the Domain, Private, and Public network profiles.

At this stage, the firewall prevented unsolicited inbound traffic from reaching the operating system despite the permissive Network Security Group configuration.

<img src="assets/image 6.png" width="900">

*Figure 6. Windows Defender Firewall enabled immediately after the virtual machine deployment.*

---

## Test External Connectivity

To verify the firewall's behavior, the virtual machine was pinged from the local workstation using its public IP address.

As expected, the requests timed out because Windows Defender Firewall was actively blocking inbound ICMP traffic, confirming that the operating system was still protected despite the exposed Network Security Group.

<img src="assets/image 7.png" width="900">

*Figure 7. Initial connectivity test showing that inbound ICMP requests were blocked by Windows Defender Firewall.*

---

## Disable Windows Defender Firewall

To transform the virtual machine into a fully exposed honeypot, Windows Defender Firewall was disabled across all network profiles.

With both the Network Security Group and the operating system firewall allowing unrestricted traffic, the VM became fully accessible from the public internet, enabling the collection of genuine attack attempts.

<img src="assets/image 8.png" width="900">

*Figure 8. Windows Defender Firewall disabled to maximize visibility of internet-based attack activity.*

---

## Confirm Public Accessibility

A second connectivity test was performed after disabling the firewall.

This time, the virtual machine responded successfully to ICMP echo requests, confirming that it was publicly reachable and capable of receiving unsolicited traffic from external hosts across the internet.

<img src="assets/image 9.png" width="900">

*Figure 9. Successful connectivity test confirming that the honeypot is publicly accessible.*

# Step 2: Building the Security Operations Center (SOC)

With the honeypot fully exposed to the internet, the next phase focused on building a centralized Security Operations Center (SOC). This involved generating security events, forwarding Windows logs to Azure, configuring Microsoft Sentinel as the SIEM platform, and validating that security events were successfully collected for investigation.

---

## Generate Failed Login Attempts

To simulate brute-force activity and generate security events, multiple Remote Desktop (RDP) login attempts were made using invalid credentials. A non-existent user account named **Hacker** was intentionally used to trigger Windows authentication failures.

These failed authentication attempts would later be collected by Microsoft Sentinel and used throughout the project for log analysis and attack visualization.

<img src="assets/image 10.png" width="900">

*Figure 10. Multiple failed RDP login attempts generated using the username **Hacker** to create authentication events.*

---

## Investigate Windows Security Logs

After generating the failed login attempts, the Windows Event Viewer was used to inspect the Security log.

Multiple **Event ID 4625** entries were observed, confirming that Windows had successfully recorded each failed authentication attempt. These events included valuable information such as the attempted username, timestamp, workstation details, and source IP address.

<img src="assets/image 11.png" width="900">

*Figure 11. Windows Security log displaying Event ID 4625 entries generated by failed RDP login attempts.*

---

## Create the Log Analytics Workspace

A Log Analytics Workspace (LAW) was deployed to serve as the centralized repository for all security logs generated by the virtual machine.

Rather than storing logs locally on the VM, Azure Monitor forwards them to the workspace, enabling centralized storage, querying, and long-term analysis.

<img src="assets/image 12.png" width="900">

*Figure 12. Log Analytics Workspace created to centralize Windows security event collection.*

---

## Deploy Microsoft Sentinel

Microsoft Sentinel was then enabled on the Log Analytics Workspace, transforming it into a cloud-native Security Information and Event Management (SIEM) platform.

Sentinel provides centralized monitoring, threat detection, investigation capabilities, and visualization tools for analyzing security events collected across Azure resources.

<img src="assets/image 13.png" width="900">

*Figure 13. Microsoft Sentinel successfully connected to the Log Analytics Workspace.*

---

## Configure Windows Security Event Collection

To begin collecting Windows Security Events, the **Windows Security Events** solution was installed from Microsoft Sentinel's Content Hub.

This solution prepares the environment for deploying the Azure Monitor Agent (AMA) and configuring Data Collection Rules (DCR), allowing Windows Security Events to be automatically forwarded to the Log Analytics Workspace.

<img src="assets/image 14.png" width="900">

*Figure 14. Windows Security Events solution installed from the Sentinel Content Hub.*

---

## Configure the Data Collection Rule (DCR)

A Data Collection Rule (DCR) was created and associated with the Windows 11 virtual machine.

The DCR instructs the Azure Monitor Agent to collect Windows Security Events and continuously forward them to the Log Analytics Workspace without requiring manual intervention.

<img src="assets/image 15.png" width="900">

*Figure 15. Data Collection Rule configured to collect Windows Security Events from the honeypot VM.*

---

## Verify Azure Monitor Agent Deployment

After assigning the virtual machine to the Data Collection Rule, Azure automatically deployed the Azure Monitor Agent extension.

Once installed, the VM began continuously forwarding Windows Security Events into the Log Analytics Workspace, completing the log collection pipeline.

<img src="assets/image 16.png" width="900">

*Figure 16. Azure Monitor Agent successfully deployed and connected to the virtual machine.*

---

## Review the Azure Resources

With the logging infrastructure complete, the resource group contained all major components of the Security Operations Center environment, including the virtual machine, virtual network, Network Security Group, Log Analytics Workspace, Microsoft Sentinel, Azure Monitor Agent, Data Collection Rule, and associated networking resources.

<img src="assets/image 17.png" width="900">

*Figure 17. Azure Resource Group showing all resources deployed throughout the SOC environment.*

---

## Query Failed Login Attempts with KQL

Once Windows Security Events began flowing into Microsoft Sentinel, Kusto Query Language (KQL) was used to retrieve failed authentication attempts.

The following query filters for **Event ID 4625**, which represents failed logon attempts in Windows Security Logs.

```kql
SecurityEvent
| where EventID == 4625
| project
    TimeGenerated,
    Account,
    Computer,
    IpAddress,
    Activity
| order by TimeGenerated desc
```

The query successfully returned all failed login attempts collected from the honeypot. During testing, Microsoft Sentinel recorded **2,975 failed login attempts**, demonstrating how rapidly an exposed internet-facing system attracts automated brute-force attacks.

<img src="assets/image 18.png" width="900">

*Figure 18. KQL query displaying failed authentication attempts (Event ID 4625) collected by Microsoft Sentinel.*

# Step 3: Threat Intelligence & Attack Visualization

With security events successfully flowing into Microsoft Sentinel, the next phase focused on transforming raw authentication logs into meaningful threat intelligence. This was achieved by enriching attacker IP addresses with geographic information, creating an interactive attack map, and validating one of the observed attacker IPs using VirusTotal.

---

## Import the GeoIP Watchlist

Windows Security Events contain the attacker's IP address but do not include geographic information such as the country or city of origin. To enrich these logs, a **GeoIP watchlist** was created in Microsoft Sentinel by importing the `geoip-summarized.csv` dataset.

The watchlist contains approximately **55,000 IP network ranges**, allowing Sentinel to correlate public IP addresses with geographic locations including city, country, latitude, and longitude.

<img src="assets/image 19.png" width="900">

*Figure 19. GeoIP watchlist imported into Microsoft Sentinel for geographic enrichment of attacker IP addresses.*

---

## Enrich Security Events with Geographic Data

After importing the watchlist, Kusto Query Language (KQL) was used to correlate failed login events with the GeoIP database using the `ipv4_lookup()` function.

This enrichment process transforms raw IP addresses into actionable threat intelligence by identifying the geographic origin of each attack.

```kql
let GeoIPDB_FULL = _GetWatchlist("geoip");
let WindowsEvents = SecurityEvent
| where EventID == 4625
| order by TimeGenerated desc
| evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network);

WindowsEvents
| project
    TimeGenerated,
    Computer,
    Account,
    AttackerIp = IpAddress,
    cityname,
    countryname,
    latitude,
    longitude
```

The enriched results now include geographic metadata alongside the original security events, making it possible to identify where attack attempts originated.

<img src="assets/image 20.png" width="900">

*Figure 20. KQL query enriching failed login attempts with geographic information from the GeoIP watchlist.*

---

## Build the Live Attack Map

To visualize attack activity, a Microsoft Sentinel **Workbook** named **Attack-Map** was created.

Using the enriched security events, the workbook aggregates failed login attempts by geographic location and plots them on an interactive world map. Bubble size increases with the number of observed attacks, providing an intuitive overview of global attack activity targeting the honeypot.

```kql
let GeoIPDB_FULL = _GetWatchlist("geoip");
let WindowsEvents = SecurityEvent;

WindowsEvents
| where EventID == 4625
| evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network)
| summarize FailedAttempts = count()
    by IpAddress, latitude, longitude, cityname, countryname
| project
    FailedAttempts,
    AttackerIp = IpAddress,
    latitude,
    longitude,
    city = cityname,
    country = countryname,
    Location = strcat(cityname, ", ", countryname)
```

<img src="assets/image 21.png" width="900">

*Figure 21. Microsoft Sentinel workbook visualizing failed login attempts on a global attack map.*

---

## Analyze the Attack Activity

After leaving the honeypot exposed to the internet, Microsoft Sentinel recorded thousands of real-world brute-force login attempts originating from multiple countries.

The attack map revealed that the highest number of failed authentication attempts originated from **Japan**, with approximately **1,280 failed login attempts**. Additional attack traffic was observed from the **United States, United Kingdom, Sweden, Portugal, Austria, Taiwan, and Spain**.

Although the geographic location represents where the IP address is registered, it does not necessarily indicate the true physical location of the attacker. Many threat actors route traffic through compromised servers, VPN services, cloud infrastructure, or proxy networks located around the world.

<img src="assets/image 22.png" width="900">

*Figure 22. Geographic distribution of failed login attempts targeting the Azure honeypot.*

---

## Validate an Attacker IP with VirusTotal

To further investigate the observed attack activity, one of the external IP addresses identified in the failed login events was analyzed using **VirusTotal**.

The lookup confirmed that the IP address had previously been flagged by multiple security vendors and was associated with malicious behavior, including Remote Desktop Protocol (RDP) brute-force activity. This validation demonstrates that the honeypot attracted genuine malicious traffic rather than simulated events.

<img src="assets/image 23.png" width="900">

*Figure 23. VirusTotal analysis confirming the reputation of an attacker IP observed in Microsoft Sentinel.*

---

## Key Observations

The collected telemetry demonstrates how rapidly publicly exposed systems become targets on the internet.

- The honeypot began receiving automated attack traffic shortly after becoming publicly accessible.
- Thousands of failed RDP authentication attempts were successfully collected and analyzed.
- Microsoft Sentinel centralized security event collection and enabled efficient investigation using KQL.
- GeoIP enrichment transformed raw IP addresses into meaningful geographic intelligence.
- VirusTotal confirmed that observed attacker IPs had a known history of malicious activity, validating the authenticity of the collected attack data.

These findings reinforce the importance of minimizing the exposure of internet-facing systems and implementing layered security controls to reduce an organization's attack surface.

# Step 4: Cloud Security Hardening

After observing real-world attack activity against the honeypot, the virtual machine was secured using Azure security best practices. The objective of this phase was to reduce the attack surface while demonstrating how layered security controls can significantly improve the security posture of an internet-facing workload.

The hardening process focused on restoring host-level protection, restricting network access, implementing role-based access control, and enabling continuous cloud security monitoring.

---

## Re-enable Windows Defender Firewall

Since Windows Defender Firewall had been disabled to maximize attack visibility during the monitoring phase, it was re-enabled across all network profiles.

Restoring the firewall provides the first layer of defense by filtering unsolicited inbound traffic before it reaches Windows services. Even if a network security rule is accidentally misconfigured, the operating system firewall continues to provide an additional security boundary.

<img src="assets/image 24.png" width="900">

*Figure 24. Windows Defender Firewall re-enabled to restore host-based protection after completing the monitoring phase.*

---

## Harden the Network Security Group (NSG)

The intentionally insecure **DANGER-AllowAnyCustomInbound** rule was removed and replaced with a restrictive rule permitting only Remote Desktop Protocol (RDP) traffic over port **3389**.

This significantly reduced the exposed attack surface by blocking unnecessary inbound traffic while still allowing authorized administrative access to the virtual machine.

<img src="assets/image 25.png" width="900">

*Figure 25. Network Security Group hardened by replacing the unrestricted inbound rule with a controlled RDP access rule.*

---

## Implement Azure Role-Based Access Control (RBAC)

To enforce the principle of least privilege, Azure Role-Based Access Control (RBAC) was configured for the virtual machine.

A security group named **JewelSales** was assigned the **Virtual Machine User Login** role, allowing authorized users to access the VM without granting excessive administrative permissions.

Implementing RBAC ensures that users receive only the permissions necessary to perform their assigned responsibilities, reducing the risk of privilege misuse and unauthorized administrative access.

> **Security Benefit:** RBAC minimizes privilege escalation risks by enforcing granular access control based on organizational roles rather than assigning broad administrative permissions.

---

## Enable Microsoft Defender for Cloud

As the final security enhancement, **Microsoft Defender for Cloud** was enabled to continuously assess the security posture of the deployed Azure resources.

Defender for Cloud provides:

- Continuous security posture assessment
- Actionable security recommendations
- Threat detection and alerting
- Best practice compliance monitoring
- Secure Score evaluation

After enabling Defender for Cloud, the deployed resources were successfully evaluated and confirmed to comply with the implemented security controls.

> **Security Benefit:** Defender for Cloud provides continuous visibility into security risks and helps identify configuration weaknesses before they can be exploited.

---

## Security Improvements Summary

The project began with an intentionally vulnerable virtual machine designed to attract real-world attackers and generate authentic security telemetry. After collecting and analyzing attack data, multiple defensive controls were implemented to significantly reduce the system's exposure.

| Security Control | Purpose |
|------------------|---------|
| Windows Defender Firewall | Restores host-level traffic filtering |
| Hardened Network Security Group | Restricts unnecessary inbound traffic |
| Azure RBAC | Enforces least-privilege access control |
| Microsoft Defender for Cloud | Continuously monitors and assesses cloud security posture |

Together, these security controls demonstrate the importance of a **defense-in-depth** strategy, where multiple independent layers of protection work together to secure cloud workloads against evolving threats.

# Skills Demonstrated

Throughout this project, I designed, deployed, monitored, investigated, and secured a cloud-based Security Operations Center (SOC) environment using Microsoft Azure. The project demonstrates practical experience across cloud infrastructure, security monitoring, threat detection, and defensive security engineering.

### Cloud & Infrastructure

- Microsoft Azure
- Azure Resource Groups
- Azure Virtual Machines
- Azure Virtual Network (VNet)
- Public IP Address Management
- Network Security Groups (NSGs)

### Security Operations (SOC)

- Microsoft Sentinel (SIEM)
- Azure Monitor Agent (AMA)
- Data Collection Rules (DCR)
- Log Analytics Workspace (LAW)
- Windows Security Event Monitoring
- Security Event Investigation

### Threat Detection & Analysis

- Kusto Query Language (KQL)
- Windows Event IDs (4625)
- RDP Brute-Force Detection
- GeoIP Log Enrichment
- Threat Intelligence
- VirusTotal Investigation
- Attack Attribution
- Security Log Analysis

### Cloud Security

- Windows Defender Firewall
- Azure Role-Based Access Control (RBAC)
- Microsoft Defender for Cloud
- Security Hardening
- Defense-in-Depth
- Least Privilege Access Control
- Cloud Security Best Practices

---
