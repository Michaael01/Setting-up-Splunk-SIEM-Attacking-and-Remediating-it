# Splunk Lab
## Objective
This lab is all about getting hands-on with Active Directory and security monitoring. I've set up Windows Server 2022 AD domain controller, a windows PC as a target machine, a Ubuntu Splunk server, and a Kali Linux. I used Kali Linux to play
 The role of an attacker in the network. The goal is to see what kind of events get generated from these attacks and learn how to configure and use Splunk to catch them. I aim to dive into domain environments and sharpen threat detection skills.
This is by no means a comprehensive step-by-step of my process, but rather a few key processes that are interesting or will be useful for my future reference.

## Network Diagram
<img width="880" height="849" alt="image" src="https://github.com/user-attachments/assets/68247bdf-9058-4cea-9e62-9e3967a90d17" />

## Tools Used
- Windows Server 2022 (for Active Directory domain controller)
- Ubuntu Server (for Splunk Server)
- Splunk (for security event monitoring and analysis)
- Kali Linux (for simulating bruteforce attack)
- Windows 10 Client (for interacting with the domain environment)
- Atomic Red Team (for simulating various attacks mapped to the MITRE ATT&CK framework)
- VirtualBox (for virtualising the lab environment)

## Skills Learned
- Windows and managing Active Directory domain environments
- Setting up and utilizing Splunk for security event monitoring and analysis
- Simulating bruteforce attack using Kali Linux
- Performing cybersecurity tests using Atomic Red Team.
- Identifying and interpreting security events generated from attacks via Splunk
- Analyzing and correlating security events in Splunk with the MITRE ATT&CK framework.

## Table of Contents
1. Table of Contents
2. Objective
3. Skills Learned
4. Tools Used
5. Network Diagram
- Setting up Splunk Server and Forwarders
- Setting Static IP Address and Default Route
- Install Splunk Enterprise
- Setting Up Splunk Forwarder
- Installing Sysmon
- Configuring Inputs for Splunk Forwarder
- Restarting Splunk Forwarder Service
- Connecting to Splunk Web Interface
5. Setting up Active Directory and Provisioning Users
6. Performing a Brute Force Attack on Target_PC and Reviewing Events via Splunk
7. Installing Atomic Red Team, Performing a Test, and Reviewing Events in Splunk
9. Conclusion

# Setting up Splunk Server and Forwarder
## Setting Static IP Address and Default Route:
- Configured a static IP address for the Splunk server and defined a default route with the gateway 192.168.100.11.
  sudo nano /etc/netplan/00-installer-config.yaml

  <img width="835" height="587" alt="image" src="https://github.com/user-attachments/assets/c8ad54e0-cbf5-461e-abb3-ed21b29ad56b" />
Apply the changes: sudo netplan apply

## Install Splunk Enterprise:
- Installed Splunk Enterprise on the Splunk server and configure it to start at boot.
Oluwatosin_admin@oluwatosinsplunk:/opt/splunk/bin$ sudo ./splunk enable boot-start -user splunk
<img width="1134" height="112" alt="image" src="https://github.com/user-attachments/assets/569cfac9-8b01-42fd-b41e-2be094fce832" />

## Setting Up Splunk Forwarder

Installed and configured Splunk Forwarder on ADDC01 and target-PC (Windows 10) to send data to the 
Splunk server as a receiving indexer. Applied Ubuntu splunk server Ip 192.168.100.11 as the receiving indexer ip and default is 9997 for domain controller and target machine.
<img width="897" height="476" alt="image" src="https://github.com/user-attachments/assets/74505766-6d44-459c-be00-effacc1d9ce6" />

## Installing Sysmon:
Installed Sysmon to enhance event logging capabilities.

<img width="833" height="280" alt="image" src="https://github.com/user-attachments/assets/da3514ee-1fbf-462d-b15b-1db8e12c97da" />
<img width="763" height="539" alt="image" src="https://github.com/user-attachments/assets/307ad734-9000-4781-aa65-275a6a0689cf" />






