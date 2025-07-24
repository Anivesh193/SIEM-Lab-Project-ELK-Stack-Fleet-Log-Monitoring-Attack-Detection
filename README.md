# 🚨 Build Your Own SIEM with ELK Stack + Fleet + Attack Detection (CentOS Lab)

Want to learn **cybersecurity hands-on**? Building your own SIEM (Security Information and Event Management) using the **Elastic Stack (ELK Stack)** is a great way to start!

This guide walks you through everything in **simple, beginner-friendly steps**.

---

## 📚 What is the ELK Stack?

The **ELK Stack** is an open-source platform used for real-time data analytics and log monitoring.

It includes:

- **Elasticsearch** — Stores and indexes data (search engine)
- **Logstash** — Processes and ships logs from different sources
- **Kibana** — Dashboard for visualization and analysis
- **Beats / Elastic Agent** — Lightweight agents for collecting logs
- **Fleet** — Central dashboard to manage Elastic Agents

---

## ✅ Step-by-Step Setup Guide (on CentOS)

### 🔧 1. Install & Update CentOS
```bash
yum update -y
☕ 2. Install Java
bash
Copy
Edit
yum install java-11-openjdk-devel -y
📦 3. Add Elastic Repository
bash
Copy
Edit
cat <<EOF | tee /etc/yum.repos.d/elasticsearch.repo
[elasticsearch-7.x]
name=Elasticsearch repository
baseurl=https://artifacts.elastic.co/packages/7.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
EOF
🔍 4. Install & Start Elasticsearch
bash
Copy
Edit
yum install elasticsearch -y
systemctl enable elasticsearch
systemctl start elasticsearch
🔐 5. Set Passwords for Users
bash
Copy
Edit
/usr/share/elasticsearch/bin/elasticsearch-setup-passwords auto
Save all generated passwords (elastic, kibana_system, etc.)

📊 6. Install & Start Kibana
bash
Copy
Edit
yum install kibana -y
systemctl enable kibana
systemctl start kibana
Update kibana.yml:

yml
Copy
Edit
server.host: "0.0.0.0"
bash
Copy
Edit
systemctl restart kibana
🔐 7. Generate Kibana Encryption Keys
bash
Copy
Edit
cd /usr/share/kibana/bin
sudo ./kibana-encryption-keys generate
Paste keys into /etc/kibana/kibana.yml:

yaml
Copy
Edit
xpack.encryptedSavedObjects.encryptionKey: "..."
xpack.reporting.encryptionKey: "..."
xpack.security.encryptionKey: "..."
bash
Copy
Edit
sudo systemctl restart kibana
🛰️ 8. Setup Fleet Server (First Time Only)
1️⃣ Go to Fleet → Add Fleet Server
Select: Default policy

2️⃣ Download Elastic Agent for Linux
Extract it:

bash
Copy
Edit
tar -xzf elastic-agent-7.17.x-linux-x86_64.tar.gz
cd elastic-agent-7.17.x-linux-x86_64
3️⃣ Choose TLS Mode → Quick Start (--insecure)
4️⃣ Add Fleet Host (e.g.):
cpp
Copy
Edit
http://192.168.42.130:8220
5️⃣ Generate Fleet Token (Copy it)
6️⃣ Install Fleet Server:
bash
Copy
Edit
sudo ./elastic-agent install \
  --fleet-server-es=http://localhost:9200 \
  --fleet-server-service-token=<YOUR_TOKEN> \
  --fleet-server-policy=<YOUR_POLICY_ID> \
  --fleet-server-insecure-http
💻 9. Add Windows Host to Fleet
🧱 Step 1: Download Elastic Agent (Windows ZIP)
🪪 Step 2: Get Enrollment Command from Kibana:
Example:

powershell
Copy
Edit
.\elastic-agent.exe install --url=http://192.168.42.130:8220 --enrollment-token=<TOKEN> --insecure
▶️ Step 3: Run as Admin in PowerShell
✅ Step 4: Verify in Kibana:
Agent status = ✅ Healthy

🧪 Simulate & Detect Real Attacks
1️⃣ SSH Brute Force (Linux)
Attacker Machine (e.g. Kali):

bash
Copy
Edit
sudo apt install hydra -y
echo -e "123456\npassword\nadmin123" > password.txt
hydra -l root -P password.txt 192.168.42.130 ssh
Check logs:

bash
Copy
Edit
tail -f /var/log/secure
Kibana Discover filter:

arduino
Copy
Edit
process.name: sshd AND event.outcome: failure
2️⃣ RDP Brute Force (Windows)
Hydra Command:

bash
Copy
Edit
hydra -l Administrator -P password.txt rdp://<windows-ip>
Kibana Discover filter:

csharp
Copy
Edit
event.code: "4625" AND winlog.event_data.LogonType: "10"
Look for:

source.ip

winlog.event_data.TargetUserName

3️⃣ File Integrity Monitoring (Linux)
Install Auditd:

bash
Copy
Edit
sudo yum install audit -y
sudo systemctl enable --now auditd
Monitor /etc/passwd:

bash
Copy
Edit
echo "-w /etc/passwd -p wa -k passwd_changes" | sudo tee -a /etc/audit/rules.d/audit.rules
sudo systemctl restart auditd
Trigger a change:

bash
Copy
Edit
sudo nano /etc/passwd
View logs:

bash
Copy
Edit
sudo ausearch -k passwd_changes
Kibana Filter:

lua
Copy
Edit
auditd.log.key: passwd_changes
📈 Bonus Ideas
Goal	Tool
Monitor file downloads	wget + Filebeat
Detect PowerShell attacks	Sysmon + Elastic Agent
Geo-IP attack detection	Public IP logs + Kibana maps
DNS tunneling	iodine or dnscat2

🧠 SOC Mindset Questions
Who did this? → user.name or source.ip

What happened? → login, process started, file edited

When did it happen? → timestamp

Where did it happen? → which host or file

📌 Final Tips
Secure your stack with firewalls & TLS

Practice in isolated VM / cloud labs

Use real tools like Hydra, Metasploit for attack simulation

👨‍💻 Author
Made with ❤️ by Anivesh Mohan
📷 Instagram
🛡️ CyberSec | Labs | DFIR | Linux
