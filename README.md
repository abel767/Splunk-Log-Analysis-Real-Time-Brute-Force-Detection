# Splunk-Log-Analysis-Real-Time-Brute-Force-Detection
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
The project creates a safe and effective data flow: 
WSL/Hydra (Attack) → Kali Universal Forwarder → Kali SSH Service → TCP Port 9997 → Windows Splunk Indexer
Real-time log collection and analysis of security-related data is made possible by this architecture.

# 1. Kali Universal Forwarder (UF) Setup & Configuration
Kali Linux's Universal Forwarder is in charge of keeping an eye on local log files and safely sending them to the Splunk Indexer.

# 1.1. UF Installation
The Splunk Universal Forwarder (version 10.0.1) was manually installed on the Kali Linux system.

Download and unpack the .tgz file
cd ~/Downloads
sudo tar -xzvf splunkforwarder-10.0.1-c486717c322b-linux-amd64.tgz -C /opt
Start the UF and accept the license
sudo /opt/splunkforwarder/bin/splunk start --accept-license
<img width="1573" height="407" alt="splunk forwarder 1" src="https://github.com/user-attachments/assets/a73cabee-1b50-4735-bb47-1f71e8a1d241" />

# 1.2. Output Configuration (Destination)

