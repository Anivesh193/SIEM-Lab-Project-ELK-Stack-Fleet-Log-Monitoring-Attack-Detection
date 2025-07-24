🚀 Building a Free SIEM with ELK Stack (Elastic Stack) – Step-by-Step Guide for Beginners (CentOS Version)
If you want to learn cybersecurity hands-on, building your own SIEM (Security Information and Event Management) using the Elastic Stack (ELK Stack) is a great way to start. This guide breaks down the steps in simple language so even beginners can follow.

📄 What is the ELK Stack?
The ELK Stack is an open-source platform used for searching, analyzing, and visualizing real-time data, especially logs. It's made of:

Elasticsearch: Stores and indexes data.
Logstash: Processes and ships logs.
Kibana: The front-end dashboard where you visualize the data.
Beats/Elastic Agent: Lightweight tools installed on servers to collect and send logs.
Fleet: Central dashboard to manage Elastic Agents and policies.
✅ Step-by-Step Setup Guide
1️⃣ Install CentOS Server
yum update -y
2️⃣ Install Java (Required by Elasticsearch)
yum install java-11-openjdk-devel -y
3️⃣ Add Elastic Repository
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
yum install elasticsearch -y
systemctl enable elasticsearch
systemctl start elasticsearch
systemctl status elasticsearch
5️⃣ Set Passwords for Built-in Users
/usr/share/elasticsearch/bin/elasticsearch-setup-passwords auto
📌 Save all generated passwords like elastic, kibana_system, logstash_system.

6️⃣ Install & Start Logstash
yum install logstash -y
systemctl enable logstash
systemctl start logstash
7️⃣ Install & Start Kibana
yum install kibana -y
systemctl enable kibana
systemctl start kibana
🌐 Configure Remote Access
Elasticsearch
Edit config:

nano /etc/elasticsearch/elasticsearch.yml
Uncomment and set:

network.host: 0.0.0.0
http.port: 9200
Restart:

systemctl restart elasticsearch
Kibana
Edit config:

nano /etc/kibana/kibana.yml
Uncomment and set:

server.host: "0.0.0.0"
Restart:

systemctl restart kibana
🔐 Generate Kibana Encryption Keys
cd /usr/share/kibana/bin
sudo ./kibana-encryption-keys generate
You’ll see output like:

xpack.encryptedSavedObjects.encryptionKey: <key1>
xpack.reporting.encryptionKey: <key2>
xpack.security.encryptionKey: <key3>
Add them to:

sudo nano /etc/kibana/kibana.yml
Paste:

xpack.encryptedSavedObjects.encryptionKey: "<key1>"
xpack.reporting.encryptionKey: "<key2>"
xpack.security.encryptionKey: "<key3>"
Restart Kibana:

sudo systemctl restart kibana
🌍 Access Kibana
Open in your browser:

http://<server-ip>:5601
Login with elastic and your password.

🛰️ Setup Fleet Server (First-Time)
1️⃣ Go to Fleet
Kibana > Management > Fleet > Agents > Add Fleet Server

Select Default Policy.

2️⃣ Download Elastic Agent (Linux)
tar -xzf elastic-agent-7.17.x-linux-x86_64.tar.gz
cd elastic-agent-7.17.x-linux-x86_64
3️⃣ Choose TLS Mode
Choose Quick Start and allow self-signed cert.

4️⃣ Add Fleet Server Host in Kibana
Example:

http://192.168.42.130:8220
5️⃣ Generate Service Token
Kibana will give you a token like:

AAEAAWVsYXN0a...
6️⃣ Start Fleet Server (on CentOS)
sudo ./elastic-agent install   --fleet-server-es=http://localhost:9200   --fleet-server-service-token=<YourToken>   --fleet-server-policy=<PolicyID>   --fleet-server-insecure-http
🖥️ Add Windows Machine (Elastic Agent)
1️⃣ Download Elastic Agent for Windows
From: https://www.elastic.co/downloads/elastic-agent

2️⃣ Get Enrollment Command from Kibana
Example:

.\elastic-agent.exe install --url=http://192.168.42.130:8220 --enrollment-token=<TOKEN> --insecure
3️⃣ Run in PowerShell (Admin)
cd C:\elastic-agent.\elastic-agent.exe install --url=... --enrollment-token=... --insecure
✅ Verify in Kibana
Go to Fleet > Agents, your Windows agent should appear as Healthy.

🛡️ Simulate Attacks & Detect
🔐 SSH Brute Force (Linux)
hydra -l root -P password.txt 192.168.42.130 ssh
tail -f /var/log/secure
In Kibana Discover:

process.name: sshd AND event.outcome: failure
🖥️ RDP Brute Force (Windows)
hydra -l Administrator -P password.txt rdp://<windows-ip>
In Kibana Discover:

event.code: "4625" AND winlog.event_data.LogonType: "10"
🧾 File Integrity Monitoring (Linux)
sudo yum install audit -y
sudo systemctl enable --now auditd
Create rule:

echo "-w /etc/passwd -p wa -k passwd_changes" | sudo tee -a /etc/audit/rules.d/audit.rules
sudo systemctl restart auditd
Trigger edit:

sudo nano /etc/passwd
View logs:

sudo ausearch -k passwd_changes
Kibana Discover:

auditd.log.key: passwd_changes
🎯 Learn More
Add Sysmon & Suricata
Visualize Geo-IP data
Create alerts in Kibana
🙌 Final Tip
Deploy in lab, simulate attacks, practice detection, and build SOC skills!

If this helped you, follow me for more such practical cybersecurity projects.
