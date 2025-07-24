<h1 align="center">🚀 Build Your Own SIEM using ELK Stack (CentOS Version)</h1>
<p align="center">
  🔒 Hands-on Cybersecurity Lab • Real-Time Log Monitoring • Brute-force Detection • SOC Skills
</p>

---

## 📘 Overview

This project shows you how to build a complete **SIEM (Security Information and Event Management)** system using the **ELK Stack** on **CentOS**. It covers everything from setting up the ELK components to simulating brute-force attacks and detecting them in **Kibana dashboards**.

> ⚡ Ideal for cybersecurity beginners, students, and SOC aspirants!

---

## 🧰 Tech Stack Used

| Component         | Purpose                                |
|-------------------|----------------------------------------|
| **CentOS 7**       | Base server OS                         |
| **Elasticsearch**  | Log storage & search engine            |
| **Logstash**       | Log pipeline & processing              |
| **Kibana**         | Visualization dashboard                |
| **Fleet Server**   | Manages Elastic Agents                 |
| **Elastic Agent**  | Collects logs from Linux & Windows     |
| **Auditd / Hydra** | Attack simulation & FIM logs           |

---

## 📦 Setup Guide

### 🔧 1. Install ELK Stack on CentOS

```bash
yum update -y
yum install java-11-openjdk-devel -y
➡️ Add Elastic repo, install Elasticsearch, Logstash, Kibana
➡️ Start services, set passwords
➡️ Configure elasticsearch.yml and kibana.yml

🔐 2. Set Encryption Keys for Kibana
bash
Copy
Edit
cd /usr/share/kibana/bin
sudo ./kibana-encryption-keys generate
Paste keys into /etc/kibana/kibana.yml and restart Kibana.

🌐 3. Access Kibana Web Interface
bash
Copy
Edit
http://<your-server-ip>:5601
Login using elastic username and saved password.

🛰️ Fleet & Agent Setup
➕ Add Fleet Server in Kibana
Go to: Kibana > Fleet > Add Fleet Server

Add host IP & generate token

🖥️ Add Elastic Agent on Windows
powershell
Copy
Edit
.\elastic-agent.exe install --url=http://<Fleet-IP>:8220 --enrollment-token=<TOKEN> --insecure
🎯 Simulate Attacks & Detect
🔐 SSH Brute Force (Linux)
bash
Copy
Edit
hydra -l root -P password.txt 192.168.x.x ssh
Kibana KQL:

kql
Copy
Edit
process.name: sshd AND event.outcome: failure
🖥️ RDP Brute Force (Windows)
bash
Copy
Edit
hydra -l Administrator -P rockyou.txt rdp://<windows-ip>
Kibana KQL:

kql
Copy
Edit
event.code: "4625" AND winlog.event_data.LogonType: "10"
📁 File Integrity Monitoring (Linux)
bash
Copy
Edit
sudo yum install audit -y
sudo systemctl enable --now auditd
echo "-w /etc/passwd -p wa -k passwd_changes" | sudo tee -a /etc/audit/rules.d/audit.rules
sudo systemctl restart auditd
Kibana KQL:

kql
Copy
Edit
auditd.log.key: passwd_changes
📸 Screenshots
Add screenshots of:

✅ Kibana Dashboard

✅ Agent Status

✅ Brute Force Log Detection

✅ Auditd Log Alerts

💡 Key Learnings
✅ How SIEM tools collect, process, and visualize logs
✅ Detecting brute-force and unauthorized login attempts
✅ Setting up a SOC-like monitoring lab
✅ Deploying Elastic Agents and Fleet Manager
✅ Hands-on exposure to Kibana, Hydra, auditd, and more

🧠 Ideal For
Cybersecurity Interns

SOC Analyst (L1) Aspirants

TCS HackQuest, Bug Bounty Practice

Portfolio / Resume Projects

Anyone who loves practical security labs

🙌 Final Words
If this helped you, drop a ⭐️ and follow for more cool cybersecurity projects.

Built with 💙 by Anivesh Mohan
GitHub: @Anivesh193
LinkedIn: Anivesh Mohan

yaml
Copy
Edit

---

### 🔧 To Use This:
- Copy this into your `README.md` file in your repo
- Add screenshots in a `/screenshots` folder and use `![alt text](screenshots/img.png)` if needed

Let me know if you want a **dark mode preview card**, **GitHub badges**, or a **hosted demo link** section 
