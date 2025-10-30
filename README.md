<img width="400" src="https://github.com/user-attachments/assets/44bac428-01bb-4fe9-9d85-96cba7698bee" alt="Tor Logo with the onion and a crosshair on it"/>

# Threat Hunt Report: Unauthorized TOR Usage
- [Scenario Creation](https://github.com/Will-B615/Threat-Hunting-scenario-tor/blob/main/threat-hunting-scenario-tor-event-creation.md) 

## Platforms and Languages Leveraged
- Windows 10 Virtual Machines (Microsoft Azure)
- EDR Platform: Microsoft Defender for Endpoint
- Kusto Query Language (KQL)
- Tor Browser

##  Scenario

Management suspects that some employees may be using TOR browsers to bypass network security controls because recent network logs show unusual encrypted traffic patterns and connections to known TOR entry nodes. Additionally, there have been anonymous reports of employees discussing ways to access restricted sites during work hours. The goal is to detect any TOR usage and analyze related security incidents to mitigate potential risks. If any use of TOR is found, notify management.

### High-Level TOR-Related IoC Discovery Plan

- **Check `DeviceFileEvents`** for any `tor(.exe)` or `firefox(.exe)` file events.
- **Check `DeviceProcessEvents`** for any signs of installation or usage.
- **Check `DeviceNetworkEvents`** for any signs of outgoing connections over known TOR ports.

---

## Steps Taken

### 1. Searched the `DeviceFileEvents` Table

On October 28, 2025, workstation will-finalprj61 under user account cybervm615 was used to download and install the Tor browser, as well as create files associated with Tor browsing activity, including tor-shopping-list.lnk and tor-shopping-list.txt on the desktop. This activity was traced and validated through the DeviceFileEvents table and matching timestamps, providing evidence of anonymous browsing tool usage on the monitored endpoint.

<img width="1158" height="733" alt="Screenshot 2025-10-28 125657" src="https://github.com/user-attachments/assets/5d6f03e0-e274-40aa-b08d-aa31a29ec738" />


---

### 2. Searched the `DeviceProcessEvents` Table

On October 28, 2025, account cybervm615 on device will-finalprj61 created a process for firefox.exe located at C:\Users\CyberVM615\Desktop\Tor Browser\Browser\firefox.exe, validating that the Tor Browser was installed and actively launched on the workstation. This activity, identified through process creation logs, demonstrates direct usage of Tor for anonymous browsing capability on the endpoint, with command line and SHA256 indicators available for incident response

<img width="1145" height="722" alt="DeviceProcessEvents" src="https://github.com/user-attachments/assets/cbec603c-e382-47b2-9820-413009a53a63" />

---

### 3. Searched the `DeviceNetworkEvents` Table for TOR Network Connections

On October 28, 2025, workstation will-finalprj61 — under user cybervm615 — initiated a successful outbound network connection via tor.exe from the directory c:\users\cybervm615\desktop\tor browser\browser\torbrowser\tor\tor.exe. The process connected to external IP 81.7.18.7 on port 9001, mapped to the URL https://www.umwb62.com/. Connection over Tor relay ports (such as 9001) indicates instance-driven anonymized traffic, warranting further review for unauthorized network obfuscation or data exfiltration risk

<img width="1201" height="557" alt="Screenshot 2025-10-28 172859" src="https://github.com/user-attachments/assets/abac39b6-ee22-439e-b50a-4036cc72d0af" />


---

## Timeline of Tor Browser Events on Device: will-finalprj61
## Oct 28, 2025, 11:58:57 AM
User cybervm615 created and renamed tor-browser-windows-x86_64-portable-14.5.8.exe on workstation will-finalprj61, signifying the initial setup and preparation for Tor browser installation.​​

Oct 28, 2025, 12:07:30 PM
A process firefox.exe (belonging to the Tor Browser suite) was launched from the directory C:\Users\CyberVM615\Desktop\Tor Browser\Browser\firefox.exe by user cybervm615, which initiated a successful local network connection to 127.0.0.1 over port 9150, consistent with Tor's SOCKS proxy functionality for local anonymized traffic routing.​​

Oct 28, 2025, 12:08:02 PM
Process tor.exe, located at c:\users\cybervm615\desktop\tor browser\browser\torbrowser\tor\tor.exe, executed by user cybervm615, successfully established an outbound connection to remote IP address 81.7.18.7 on port 9001 (associated with domain https://www.umwb62.com). This port serves Tor relay communications, confirming that Tor client initiated anonymized outbound traffic as is typical for Tor network activity.​​

Oct 28, 2025, 12:16:10 PM (and surrounding times)
Multiple process creation events for firefox.exe occurred in the same Tor Browser directory (C:\Users\CyberVM615\Desktop\Tor Browser\Browser\firefox.exe) by account cybervm615. Command-line and startup characteristics verified execution as part of the expected Tor Browser (Firefox ESR-based) behavior.​​

Oct 28, 2025, 12:17:49 PM
Files tor-shopping-list.lnk and tor-shopping-list.txt were created on the desktop, likely referencing Tor browser activities. These artifacts confirm hands-on interaction with Tor-related files on the endpoint by user cybervm615

---

## Summary

The user "employee" on the "threat-hunt-lab" device initiated and completed the installation of the TOR browser. They proceeded to launch the browser, establish connections within the TOR network, and created various files related to TOR on their desktop, including a file named `tor-shopping-list.txt`. This sequence of activities indicates that the user actively installed, configured, and used the TOR browser, likely for anonymous browsing purposes, with possible documentation in the form of the "shopping list" file.

---

## Response Taken

TOR usage was confirmed on the endpoint `threat-hunt-lab` by the user `employee`. The device was isolated, and the user's direct manager was notified.

---

