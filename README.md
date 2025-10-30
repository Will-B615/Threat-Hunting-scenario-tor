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
---

## # SOC Incident Report: Tor Browser Usage on Endpoint

## Incident Overview
On October 28, 2025, Microsoft Defender for Endpoint (MDE) telemetry confirmed the installation and active use of the Tor Browser by user `cybervm615` on workstation `will-finalprj61`. This activity comprised executable creation, anonymized network connections, and the appearance of artifact files associated with Tor browsing.

## Timeline of Events

| Timestamp                  | Title                         | Details                                                                                                                                                        |
|----------------------------|-------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Oct 28, 2025, 11:58:57 AM  | Tor Browser Setup             | User `cybervm615` created and renamed the Tor Browser installer (`tor-browser-windows-x86_64-portable-14.5.8.exe`), marking setup initiation on endpoint.      |
| Oct 28, 2025, 12:07:30 PM  | Tor Proxy Process Initiated   | `firefox.exe` (Tor Browser ESR) launched, connecting to local SOCKS proxy at 127.0.0.1:9150 for anonymized traffic routing.                                   |
| Oct 28, 2025, 12:08:02 PM  | Outbound Tor Network Connection| `tor.exe` established outbound connection to IP 81.7.18.7, port 9001 (domain: umwb62.com). This is a Tor relay, confirming anonymized external traffic.        |
| Oct 28, 2025, 12:16:10 PM+ | Repeated Tor Browser Execution| Multiple launches of `firefox.exe` from Tor Browser folder by `cybervm615`; all process command-lines consistent with Tor Browser activity.                    |
| Oct 28, 2025, 12:17:49 PM  | Tor-Related Artifact Creation | Desktop shortcut and text files (`tor-shopping-list.lnk`, `tor-shopping-list.txt`) created, confirming user interaction with Tor browsing environment.         |

## Key Findings

- Tor Browser installer and supporting executables were present and active on a user endpoint.
- Network traffic confirmed successful outbound anonymized connections and use of local proxy for browser traffic.
- Associated artifact files placed on the desktop indicate hands-on interaction with Tor browser features.

## Impact and Risk

- Use of anonymizing tools (Tor) elevates risk for unmonitored data flows, possible exfiltration, and security policy violations.
- No additional evidence of endpoint compromise or data loss was found during review.
- This incident underscores the need for ongoing monitoring of anonymization tools within the environment.

## Indicators of Compromise (IoCs)

- Executables: `tor-browser-windows-x86_64-portable-14.5.8.exe`, `tor.exe`, `firefox.exe` (Tor Browser folder)
- Process Paths:  
  - `C:\Users\CyberVM615\Desktop\Tor Browser\Browser\firefox.exe`  
  - `C:\Users\CyberVM615\Desktop\Tor Browser\Browser\torbrowser\tor\tor.exe`
- Network:  
  - Outbound to IP `81.7.18.7` port `9001` (domain: umwb62.com)  
  - Local connection on port `9150` (Tor SOCKS proxy)
- Artifacts:  
  - `tor-shopping-list.lnk`, `tor-shopping-list.txt` (desktop files)

## Recommendations

- Enforce endpoint control policies regarding anonymizing tools.
- Consider more restrictive application whitelisting.
- Educate users on organizational risk and policy for anonymous browsing.
- Continue monitoring endpoint for further privacy tool usage.

## Conclusion

This incident validated Tor Browser installation and use for anonymized browsing on a managed endpoint. Activities were confirmed by Microsoft Defender for Endpoint logs and forensic review of process, file, and network events. No further compromise detected, though improved monitoring and policy clarification are recommended.


---

