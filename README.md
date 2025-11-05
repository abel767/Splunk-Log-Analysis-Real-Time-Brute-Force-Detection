# Project Overview
From data ingestion to real-time threat detection and alerting, this project shows how to set up a fundamental Security Information and Event Management (SIEM) pipeline. It focuses on using Splunk to detect SSH brute-force attacks against a Linux system, which are simulated using the Hydra tool.

# Technologies Used
* Splunk Enterprise: (Indexer, Search Head) - Hosted on a Windows machine.
* Splunk Universal Forwarder (UF): - Installed on Kali Linux, acting as a data source.
* Kali Linux: - Target system for SSH attacks and host for the Universal Forwarder.
* Windows Subsystem for Linux (WSL): - Attacker machine, running Hydra.
* Hydra: - Network logon cracker used to simulate brute-force attempts.
* Search Processing Language (SPL): - Splunk's powerful query language for data analysis and correlation.

# Data Flow Architecture
The project creates a safe and effective data flow: <br>

**WSL/Hydra (Attack) → Kali Universal Forwarder → Kali SSH Service → TCP Port 9997 → Windows Splunk Indexer** <br>
<br>
Real-time log collection and analysis of security-related data is made possible by this architecture.

# 1. Kali Universal Forwarder (UF) Setup & Configuration
Kali Linux's Universal Forwarder is in charge of keeping an eye on local log files and safely sending them to the Splunk Indexer.

### 1.1. UF Installation
The Splunk Universal Forwarder (version 10.0.1) was manually installed on the Kali Linux system. <br>

Download and unpack the .tgz file <br>

**cd ~/Downloads** <br>
**sudo tar -xzvf splunkforwarder-10.0.1-c486717c322b-linux-amd64.tgz -C /opt** <br>

Start the UF and accept the license <br>
**sudo /opt/splunkforwarder/bin/splunk start --accept-license <br>**
<br>

<img width="1573" height="407" alt="splunk forwarder 1" src="https://github.com/user-attachments/assets/a73cabee-1b50-4735-bb47-1f71e8a1d241" /> 

### 1.2. Output Configuration (Destination)
The UF was set up to send all monitored data via TCP port 9997 to the Splunk Indexer running on the Windows computer (IP: 192.168.1.4). <br>

**sudo /opt/splunkforwarder/bin/splunk add forward-server 192.168.1.4:9997** <br>

Verification
<br>
**sudo /opt/splunkforwarder/bin/splunk list forward-server** <br>

<img width="421" height="162" alt="2" src="https://github.com/user-attachments/assets/3b3fb202-d0b4-400e-97f0-058e79c7b31f" />

### 1.3. Input Configuration (Source) (CRUCIAL STEP) <br>
The UF is instructed by this configuration on which file to send and how to classify it.  Even if a default setting worked for a short time, this step is essential for data integrity. <br>

**Content of /opt/splunkforwarder/etc/system/local/inputs.conf** <br>

**[monitor:///var/log/auth.log]
disabled = false
index = main
sourcetype = auth**
<br>

<img width="622" height="243" alt="image" src="https://github.com/user-attachments/assets/699e3dc8-d760-4e28-8141-2d9059931b6d" />

# 2. Attack Simulation & Initial Log Verification
### 2.1. WSL Setup & Hydra Execution
<br>
Hydra was used to initiate a brute-force attack from the WSL environment.
<br>


<img width="1471" height="470" alt="image" src="https://github.com/user-attachments/assets/586ba45b-134e-4777-8f5f-f69826ace7e6" /> <br>

# 3. Real-Time Threat Detection & Alerting
## 3.1. Baseline Search Creation (Simple Log Verification)
<br>
The simplest search to confirm that raw failed login events are successfully arriving and being indexed from the Kali Forwarder is documented in this step.
<br>

Verification Search Query:

**index=main host=kali sourcetype=auth "Failed password"**
<br>

<img width="1901" height="875" alt="7revised" src="https://github.com/user-attachments/assets/a791118b-e61b-4825-8150-391c596104c8" />
<br>
  
## 3.2. Real-Time Alert Configuration
To keep an eye out for a spike in login failures and set an anomaly threshold, the baseline search was transformed into a real-time alert.
<br>

<img width="1006" height="856" alt="8 revised" src="https://github.com/user-attachments/assets/b9ff457a-25db-49be-bc54-367e2a45e6fd" />
<br>


**Alert Configuration Summary**

The following settings were used to configure the final alert:
<br>

<img width="987" height="461" alt="table" src="https://github.com/user-attachments/assets/5a9b7137-7fe8-4a72-958e-98191cc54770" />
<br>

## 3.3. Final Alert Verification
By carrying out a fresh attack and confirming that the alert triggers and logs the history appropriately, this step shows that the alert logic is effective.
<br>
Verification Procedure: 
<br>
1. The Kali Forwarder was started again.
2. The Hydra attack was executed again (a third time) to generate fresh log data for the alert to       find.
3. The scheduled alert ran, found the new surge, and logged the result.
<br>

**Final Proof of Trigger**
<br>

**Action: Navigate to Activity → Triggers in the Splunk UI.**
<br>

<img width="1906" height="716" alt="9" src="https://github.com/user-attachments/assets/a2eda5c3-30c5-41d7-a7c8-6a334177c476" />
<br>
<img width="1343" height="431" alt="10" src="https://github.com/user-attachments/assets/338c7aa4-6198-41a9-8220-fa856b07e845" />
<br>

# Conclusion
From endpoint collection to real-time alerting, this project effectively built a comprehensive, operational Security Information and Event Management (SIEM) pipeline.
<br>
This exercise's thoroughness validates abilities in three main areas:
<br>
1. **Data Reliability:** Solved and fixed operational issues (such as the "Forwarder Not Running" status) that halt the data stream, as well as successfully debugged and guaranteed continuous log forwarding from a Linux Universal Forwarder (Kali).
2. **Advanced Analysis (SPL):** Developed and implemented strong Search Processing Language (SPL) logic that goes beyond log viewing to actionable threat detection by precisely detecting brute-force patterns by establishing anomaly thresholds (Number of results > 5).
3. **Operational Alerting:** set up a dependable, prearranged alert that was successfully tested against a live attack (Hydra).  The system's ability to carry out the alert logic, trigger successfully, and add the required record to the alert history was demonstrated by the final verification.
