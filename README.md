# 🛡️ SOC Analyst Home Lab — Hyper-V, Windows AD, Splunk, Sysmon, Kali Linux (End-to-End Implementation with MITRE ATT&CK)

## 📌 Project Overview
This lab is all about getting hands-on with Active Directory and security monitoring. I've set up Windows Server 2022 AD domain controller, a windows PC as a target machine, a Ubuntu Splunk server, and a Kali Linux. I used Kali Linux to play
 The role of an attacker in the network. The goal is to see what kind of events get generated from these attacks and learn how to configure and use Splunk to catch them. I aim to dive into domain environments and sharpen threat detection skills.
This is by no means a comprehensive step-by-step of my process, but rather a few key processes that are interesting or will be useful for my future reference.

## 🚀 Tools Used
- Windows Server 2022 (for Active Directory domain controller): authenticates domain logons, generates Windows Security events.
- Ubuntu Server (for Splunk Server):  
- Splunk (for security event monitoring and analysis):  SIEM, log collection, dashboards, and alerts.
- Kali Linux (for simulating bruteforce attack): brute force with Hydra, run adversary simulations.
- Windows 10 Client (for interacting with the domain environment)
- Atomic Red Team (for simulating various attacks mapped to the MITRE ATT&CK framework)
- Hyper-V ( Hypervisor): NAT configured

## Skills Learned
- Windows and managing Active Directory domain environments
- Setting up and utilizing Splunk for security event monitoring and analysis
- Simulating bruteforce attack using Kali Linux
- Performing cybersecurity tests using Atomic Red Team.
- Identifying and interpreting security events generated from attacks via Splunk
- Analyzing and correlating security events in Splunk with the MITRE ATT&CK framework.

The goal was to:

- Detect brute-force authentication attempts
- Capture process and endpoint telemetry with Sysmon
- Simulate adversary behaviors using Atomic Red Team
- Build Splunk dashboards + alerts for detection and investigation
- 
👉 By the end, I had a fully working SOC lab that detects failed logons, successful brute force, suspicious processes, and firewall activity.

## Architecture Diagram
<img width="880" height="849" alt="image" src="https://github.com/user-attachments/assets/68247bdf-9058-4cea-9e62-9e3967a90d17" />

## 🌐 Hyper-V Networking Notes

- Used a NAT/bridged vSwitch called Nat-Switch for VM-to-VM + internet connectivity
- Could also isolate traffic with multiple vSwitches + routing rules.
- Best practice: created checkpoints before major changes.
- Dynamic disks used (expandable VHDX). Expanded Ubuntu LVM when Splunk disk filled up.
- Enhanced session mode enabled for clipboard/file sharing.

<img width="949" height="795" alt="image" src="https://github.com/user-attachments/assets/006aecfc-2383-4b3f-9d3e-c4fa3aaa72ec" />

<img width="1051" height="869" alt="image" src="https://github.com/user-attachments/assets/07d926d8-f12e-4d79-89b6-a26a88731ddc" />


# 📊 Setting up Splunk Server and Forwarder

## Setting Static IP Address and Default Route:
- Configured a static IP address for the Splunk server and defined a default route with the gateway 192.168.100.11.
  sudo nano /etc/netplan/00-installer-config.yaml

  <img width="835" height="587" alt="image" src="https://github.com/user-attachments/assets/c8ad54e0-cbf5-461e-abb3-ed21b29ad56b" />
Apply the changes: sudo netplan apply

## Install Splunk Enterprise:
- Installed Splunk Enterprise on the Splunk server and configure it to start at boot.
Oluwatosin_admin@oluwatosinsplunk:/opt/splunk/bin$ sudo ./splunk enable boot-start -user splunk

## Setting Up Splunk Forwarder

Splunk acts as the SIEM, centralizing logs, allowing us to:

- Installed and configured Splunk Forwarder on ADDC01 and target-PC (Windows 10)
- To ingest, write correlation searches and send data to the Splunk server as a receiving indexer.

## 🔹 Setup
- Install .deb package from Splunk site.
- Start Splunk → Web GUI on http://<splunk_ip>:8000.
- Open port 9997 for Universal Forwarders.
- I Applied Ubuntu splunk server Ip 192.168.100.11 as the receiving indexer IP
- Opened default port 9997 for domain controller and target machine.

<img width="897" height="476" alt="image" src="https://github.com/user-attachments/assets/74505766-6d44-459c-be00-effacc1d9ce6" />

## Installing Sysmon:
Installed Sysmon to enhance event logging capabilities.

<img width="763" height="539" alt="image" src="https://github.com/user-attachments/assets/307ad734-9000-4781-aa65-275a6a0689cf" />


## Configuring Inputs for Splunk Forwarder:

Created an inputs.conf file in C:\Program Files\SplunkUniversalForwarder\etc\system\local on Domain controller server and target-PC, configuring settings as per.
<img width="1666" height="869" alt="image" src="https://github.com/user-attachments/assets/bc20a268-2771-43fc-8b27-b6bbdead1188" />
<img width="1484" height="933" alt="image" src="https://github.com/user-attachments/assets/eff862a3-6b55-4694-a318-45c2f432114e" />

## Restarting Splunk Forwarder Service:

Restarted the Splunk Forwarder service on ADDC01 and set to log on as local system account.
<img width="1292" height="508" alt="image" src="https://github.com/user-attachments/assets/8371e4e7-d2ac-4e35-9254-fa803098d2ca" />

## Connecting to Splunk Web Interface:

Accessed the Splunk server's web interface at port 8000, then created an index named endpoint as specified in the inputs.conf file. 
I repeated this process for both ADDC (WIN-AF2RB2D1HIP) and target-PC (DESKTOP-C87NO7B) to ensure the Splunk server receives events from both sources.

<img width="1043" height="746" alt="image" src="https://github.com/user-attachments/assets/db5727c9-49f3-4f89-96b7-92b0bd560136" />

# 📥 Setting up Active Directory and provisioning users

Install Active Directory Domain Services on ADDC WIN-AF2RB2D1HIP
<img width="964" height="666" alt="image" src="https://github.com/user-attachments/assets/94859e58-f36e-44d3-ad94-4b73e03e149f" />

Promote ADDC01 to Domain Controller
<img width="772" height="666" alt="image" src="https://github.com/user-attachments/assets/ba79a5fa-3e51-4767-bb16-ddc2502e0821" />

I joined target_PC to the domain and tinkered around with users, groups and permissions. I used this script below to generate 510 users.

<img width="713" height="533" alt="image" src="https://github.com/user-attachments/assets/4d9585de-5089-4066-b6b1-eda5f9f93081" />
<img width="999" height="728" alt="image" src="https://github.com/user-attachments/assets/13da5480-bcf2-4d64-a781-00f502d6e4c1" />

# 🐉 Performing a Brute force attack simulation on target_PC with Kali (Hydra)

Why Hydra?
Hydra is a fast password-cracking tool. I therefore, used Hydra to launch a brute force dictionary attack on target_PC from the Kali Linux machine. In this lab:

- I had enabled RDP on target_PC desktop-c87no7b beforehand so this attack would be feasable.
- Simulated RDP brute force against the target.
- Generated Event ID 4625 (failed logons) and 4624 (successful logons).

<img width="1611" height="484" alt="image" src="https://github.com/user-attachments/assets/60e75c6a-0ca8-4013-90d4-4609cd71cafc" />

After running the attack, we can see that Splunk recorded 6 events with event code 4265, which indicates failed login attempts for source network address192.168.100.13 which is from attacking machine . 
This corresponds to the 4 passwords in the wordlist I used for the attack, which was run twice. 
- index=*endpoint* oluwatosin EventCode=4625
- index=endpoint EventCode=4625 | stats count by Account_Name, Source_Network_Address

Among these, there are two events with event code 4264, representing successful login with 3 attempts.
This outcome is expected since one of the passwords in the wordlist was of course correct

<img width="634" height="574" alt="image" src="https://github.com/user-attachments/assets/b8285171-375d-4bf2-b3cc-3b6051de9cc5" />

# 🧪 Installing Atomic Red Team, Performing a Test ( MITRE ATT&CK), and Reviewing Events in Splunk

Atomic Red Team (ART) is an open-source project that offers a collection of tests to simulate cyberattacks based on the MITRE ATT&CK framework.
Instead of running a real attacker toolkit, ART lets us safely simulate adversary techniques and validate whether our detection pipeline (Sysmon → UF → Splunk) catches them.


This was a crucial part of the lab because:

- It validated that Windows Security Events + Sysmon telemetry were being forwarded correctly.
- It proved Splunk could detect & map adversary behavior in line with ATT&CK techniques.
- It helped practice incident triage: which logs appear, how to search, how to visualize.

# ⚙️ Setup
On Target-PC, created a folder C:\AtomicRedTeam.
Before installing Atomic Red Team (ATR) on target_PC, I excluded the C: drive (where ATR will be installed) from Microsoft Defender Anti-Virus scans. 

This exclusion is not recommended for normal circumstances.
To allow PowerShell scripts to run without restrictions for the current user.
Allowed PowerShell scripts to run:

Set-ExecutionPolicy Bypass -Scope CurrentUser

<img width="925" height="343" alt="image" src="https://github.com/user-attachments/assets/348fb8f8-68e6-410f-836c-5548a5b75533" />

Configured Windows Defender to exclude the folder C:\AtomicRedTeam from real-time scanning.

<img width="790" height="484" alt="image" src="https://github.com/user-attachments/assets/af1e8c52-0017-48ec-ad7a-aeab3e5da228" />



Now we can view all the tests available in Atomic Red Team are now accessible. Each test is named accordingly after the corresponding MITRE ATT&CK technique. 
For example, the T1136.001 test, which corresponds to the "Create Account: Local Account" persistence technique in MITRE ATT&CK.
The test was executed as Administrator on the Target_PC. Splunk Forwarder automatically captures these events and forwards them to the Splunk server. 

The following Windows Security events are generated:


<img width="552" height="604" alt="image" src="https://github.com/user-attachments/assets/d758af4b-0e44-4def-bda1-5fcf99c8f319" />
<img width="867" height="963" alt="image" src="https://github.com/user-attachments/assets/99a10dc5-4d63-4b86-a366-ad849b92455b" />

Running the test created a user called NewLocalUser, added it to the local administrators group, and finally deleted the user.

<img width="1166" height="824" alt="image" src="https://github.com/user-attachments/assets/da2c03fd-81f1-4c65-b28e-e9ca0044dcba" />

We see these events replicating in Splunk.

<img width="1287" height="877" alt="image" src="https://github.com/user-attachments/assets/5794cafc-f7d2-4635-ad25-8364f2fa71a2" />


## Corresponding Event Codes

Here are the corresponding event codes:
- 4798: A user's local group membership was enumerated.
- 4738: A user account was changed.
- 4720: A user account was created.
- 4722: A user account was enabled.
- 4724: An attempt was made to reset an account's password.
- 4726: A user account was deleted.

<img width="959" height="951" alt="image" src="https://github.com/user-attachments/assets/09bc01c0-f5e8-4f40-bb0e-49b3cef677ae" />

# 🏁 Conclusion

This lab has been a great learning experience for me. 

I've set up Splunk Enterprise on Ubuntu, deployed Splunk forwarders, performed tests with Atomic Red Team, and analyzed results in Splunk following the MITRE ATT&CK framework.
I also used Kali Linux to simulate a brute force attack and reviewed the outcome in Splunk.

Through setting all this up, I've sharpened my skills in virtualization with Hypervisor, general Windows, powershell scripting, Active Directory on Windows Server, and Linux processes. Building a detection pipeline from endpoint → SIEM → analyst.

## 🚀 Next Steps

I will be happy to keep building on this lab. My next steps include: 
- Add DNS, FTP, Proxy logs.
- Expand detections to persistence & lateral movement.
- Automate responses with SOAR.
- cCreating Splunk alerts, tweaking group policies, and tinkering around in the domain environment.





