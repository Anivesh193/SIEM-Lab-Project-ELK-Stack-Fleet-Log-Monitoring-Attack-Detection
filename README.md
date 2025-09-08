# 🛡️ Building a Free SIEM with ELK Stack (Elastic Stack) — Step-by-Step Guide for Beginners (CentOS Version)

**By Anivesh Mohan | 8 min read**

---

If you want to learn cybersecurity hands-on, building your own **SIEM (Security Information and Event Management)** using the **Elastic Stack (ELK Stack)** is a great way to start. This guide breaks down the steps in simple language, making it easy for even beginners to follow.

---

## 📄 What is the ELK Stack?

The ELK Stack is an open-source platform used for searching, analyzing, and visualizing real-time data, especially logs. It’s made of:

- **Elasticsearch** — Stores and indexes data. It’s the search engine.  
- **Logstash** — Processes and ships logs from different sources.  
- **Kibana** — The front-end dashboard where you visualize the data.  
- **Beats/Elastic Agent** — Lightweight tools installed on servers to collect and send logs.  
- **Fleet** — Central dashboard to manage Elastic Agents and policies.  

Thousands of companies use ELK for threat detection, log management, and monitoring.

---

## ✅ Step-by-Step Setup Guide

### 1️⃣ Install CentOS Server

You need a CentOS server (local or cloud like AWS, DigitalOcean, etc.). Update it first:

```bash
yum update -y
2️⃣ Install Java (Required by Elasticsearch)
bash
Copy code
yum install java-11-openjdk-devel -y
3️⃣ Add Elastic Repository
Create a repo file:

bash
Copy code
cat <<EOF | tee /etc/yum.repos.d/elasticsearch.repo
[elasticsearch-7.x]
name=Elasticsearch repository for 7.x packages
baseurl=https://artifacts.elastic.co/packages/7.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
EOF
4️⃣ Install & Start Elasticsearch
bash
Copy code
yum install elasticsearch -y
systemctl enable elasticsearch
systemctl start elasticsearch
systemctl status elasticsearch
5️⃣ Set Passwords for Built-in Users (Important for Security & Fleet)
Generate secure passwords for default users:

bash
Copy code
/usr/share/elasticsearch/bin/elasticsearch-setup-passwords auto
Confirm by typing y. Copy and save all the passwords shown:

pgsql
Copy code
PASSWORD elastic = <your-password>
PASSWORD kibana_system = <your-password>
PASSWORD logstash_system = <your-password>
You’ll need them later to log into Kibana and connect Elastic Agent/Fleet.

6️⃣ Install & Start Logstash
bash
Copy code
yum install logstash -y
systemctl enable logstash
systemctl start logstash
7️⃣ Install & Start Kibana
bash
Copy code
yum install kibana -y
systemctl enable kibana
systemctl start kibana
8️⃣ Allow Remote Access
✅ Elasticsearch Configuration
bash
Copy code
nano /etc/elasticsearch/elasticsearch.yml
Uncomment and set:

yaml
Copy code
network.host: 0.0.0.0
http.port: 9200
Restart:

bash
Copy code
systemctl restart elasticsearch
✅ Kibana Configuration
bash
Copy code
nano /etc/kibana/kibana.yml
Uncomment and set:

pgsql
Copy code
server.host: "0.0.0.0"
Restart:

bash
Copy code
systemctl restart kibana
9️⃣ Generate Kibana Encryption Keys (For Fleet Setup)
To securely manage saved objects, reports, and session data, you need encryption keys.

A. Navigate to Kibana CLI
bash
Copy code
cd /usr/share/kibana/bin
B. Run the Key Generator
bash
Copy code
sudo ./kibana-encryption-keys generate
You’ll see output like this:

makefile
Copy code
xpack.encryptedSavedObjects.encryptionKey: <key1>
xpack.reporting.encryptionKey: <key2>
xpack.security.encryptionKey: <key3>
C. Add Keys to kibana.yml
bash
Copy code
sudo nano /etc/kibana/kibana.yml
Paste the keys at the bottom like this:

pgsql
Copy code
xpack.encryptedSavedObjects.encryptionKey: "<key1>"
xpack.reporting.encryptionKey: "<key2>"
xpack.security.encryptionKey: "<key3>"
D. Restart Kibana
bash
Copy code
sudo systemctl restart kibana
Now your Kibana instance is secured and ready for Fleet integrations!

🔟 Access Kibana
Open in your browser:

cpp
Copy code
http://<server-ip>:5601
Log in with:

Username: elastic

Password: from step 5

🛰️ Add Fleet Server and Enroll Elastic Agent (First-Time Setup)
📡 What is a Fleet Server?
A Fleet Server acts as the communication bridge between Kibana and Elastic Agents installed on other systems like Linux or Windows.

You must install at least one Fleet Server before adding agents.

✅ Step 1: Choose an Agent Policy
In Kibana:

Go to Management > Fleet > Agents > Add Fleet Server

Use the default policy

✅ Step 2: Download Elastic Agent
Download and extract the Linux TAR installer on your CentOS machine:

bash
Copy code
tar -xzf elastic-agent-7.17.x-linux-x86_64.tar.gz
cd elastic-agent-7.17.x-linux-x86_64
✅ Step 3: Choose TLS Mode
In Kibana, choose:

Quick Start → Fleet Server generates a self-signed certificate (for labs)

Production → You provide a certificate (for real use)

For this lab, choose Quick Start.

✅ Step 4: Add Fleet Server Host
Enter your server IP or domain:

cpp
Copy code
http://192.168.42.130:8220
✅ Step 5: Generate a Service Token
Click Generate Service Token, copy it, and save it securely — it’s only used once.

Example token:

nginx
Copy code
AAEAAWVsYXN0aWMvZmxlZXQtc2VydmVyL3Rva2VuLTE3NTMzMDgwNTgxNzU6d0ltcFI1enBUVnlLRDVqTzQ3NUdBQQ
✅ Step 6: Start Fleet Server
Run this on your CentOS terminal:

bash
Copy code
sudo ./elastic-agent install \
  --fleet-server-es=http://localhost:9200 \
  --fleet-server-service-token=<your-token> \
  --fleet-server-policy=<your-policy-id> \
  --fleet-server-insecure-http
After installation, Kibana will show Fleet Server is connected.

🖥 Add Elastic Agent (Host Windows Machine)
✅ Step 1: Download Elastic Agent for Windows
Visit:

👉 https://www.elastic.co/downloads/elastic-agent

Download the .zip, extract it to C:\elastic-agent\.

✅ Step 2: Get the Enrollment Token
In Kibana:

Go to Fleet > Agents > Add Agent

Select Platform: Windows

Select Agent Policy: Default Policy

Copy the command provided.

✅ Step 3: Run Command on Windows
Open PowerShell as Administrator, go to the extracted folder, and paste the command.

✅ Step 4: Verify in Kibana
Within seconds, your Windows machine should appear as Healthy, now monitored in real-time.

🔍 Monitor Common Windows Attacks
You can now detect:

RDP brute-force attacks

Suspicious PowerShell usage

File integrity changes in Windows folders

Windows login failures and successes

🛡️ Real-World Attack Simulations & Detection
After setting up the SIEM, let’s simulate common cyberattacks and detect them.

🔐 1. Brute Force Attack on SSH (Linux)
Goal:
Simulate attackers guessing SSH passwords.

Steps:
Install Hydra:

bash
Copy code
sudo apt update && sudo apt install hydra -y
Create a password list:

bash
Copy code
echo -e "123456\npassword\nadmin123" > password.txt
Run brute-force:

bash
Copy code
hydra -l root -P password.txt <server-ip> ssh
View logs:

bash
Copy code
tail -f /var/log/secure
Filter in Kibana:

arduino
Copy code
process.name: sshd AND event.outcome: failure
Learnings:
✔ How brute force looks in logs
✔ How to spot attacker IP
✔ How to correlate logs by time or username

🖥 2. RDP Brute Force Attack (Windows)
Goal:
Simulate an attack on Windows via RDP.

Steps:
Run Hydra:

bash
Copy code
hydra -l Administrator -P password.txt rdp://<windows-ip>
Windows logs failed attempts in Event ID 4625.

Filter in Kibana:

csharp
Copy code
event.code: "4625" AND winlog.event_data.LogonType: "10"
Learnings:
✔ How attackers exploit RDP
✔ How to spot failed logins
✔ Early ransomware detection

🧾 3. File Integrity Monitoring (Linux)
Goal:
Detect unauthorized file changes.

Steps:
Install auditd:

bash
Copy code
sudo yum install audit -y
sudo systemctl enable --now audit
Set rules to monitor /etc/passwd:

bash
Copy code
sudo auditctl -w /etc/passwd -p wa -k passwd_changes
Edit file:

bash
Copy code
sudo nano /etc/passwd
View logs:

bash
Copy code
sudo ausearch -k passwd_changes
Filter in Kibana:

lua
Copy code
auditd.log.key: passwd_changes
Learnings:
✔ Detect file tampering
✔ Track user activity
✔ Identify privilege escalation attempts

📌 Follow Me
For more cybersecurity projects and step-by-step guides, follow me on:

👉 LinkedIn
👉 Medium Blog

Made with ☕ + ❤️ by Anivesh Mohan








Ask ChatGPT
