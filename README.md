<h1 align="center">🛡️ Build Your Own SIEM with ELK Stack</h1>
<p align="center">A Step-by-Step Cybersecurity Lab Using CentOS, Kibana, Fleet, and Elastic Agent</p>

---

## ✨ Project Highlights

> A beginner-friendly, real-world SIEM lab that lets you monitor logs, simulate attacks, and detect them using **Kibana dashboards** — just like a SOC Analyst would.

---

## 📦 Components Used

### 🧰 Main Stack
- **CentOS 7** – Base OS
- **Elasticsearch** – Stores and indexes logs
- **Logstash** – Pipeline to process and forward logs
- **Kibana** – Visualization & dashboards
- **Fleet & Elastic Agent** – Collect logs from Windows/Linux

### 🔧 Tools for Attack & Detection
- `Hydra` – Brute-force simulator
- `auditd` – File Integrity Monitoring (FIM)
- `Kibana KQL` – Search and detect logs

---

## 🛠️ Setup Steps (CentOS)

### 1️⃣ Install Java and Add Elastic Repo
```bash
yum update -y
yum install java-11-openjdk-devel -y
2️⃣ Install ELK Stack Components
bash
Copy
Edit
yum install elasticsearch -y
yum install logstash -y
yum install kibana -y
Enable & start each service using systemctl.

🔐 Passwords & Encryption Keys
✅ Set Built-in Passwords
bash
Copy
Edit
/usr/share/elasticsearch/bin/elasticsearch-setup-passwords auto
🔑 Generate Kibana Encryption Keys
bash
Copy
Edit
cd /usr/share/kibana/bin
./kibana-encryption-keys generate
Add them to /etc/kibana/kibana.yml and restart Kibana.

🌐 Configure Remote Access
Elasticsearch
yaml
Copy
Edit
network.host: 0.0.0.0
http.port: 9200
Kibana
yaml
Copy
Edit
server.host: "0.0.0.0"
Then:

bash
Copy
Edit
systemctl restart elasticsearch
systemctl restart kibana
📊 Access Kibana Dashboard
Open your browser and go to:

bash
Copy
Edit
http://<your-server-ip>:5601
Login with the elastic user and saved password.

🚀 Add Fleet & Elastic Agent
🛰️ Setup Fleet Server
Open: Kibana > Fleet > Add Fleet Server

Set Fleet Host: http://<your-ip>:8220

Generate a service token

💻 Install Elastic Agent (Linux)
bash
Copy
Edit
./elastic-agent install \
  --fleet-server-es=http://localhost:9200 \
  --fleet-server-service-token=<YourToken> \
  --fleet-server-policy=<PolicyID> \
  --fleet-server-insecure-http
🪟 Add Elastic Agent on Windows
📥 Download Elastic Agent
From: https://www.elastic.co/downloads/elastic-agent

▶️ Run in PowerShell (Admin)
powershell
Copy
Edit
.\elastic-agent.exe install --url=http://<Fleet-IP>:8220 --enrollment-token=<TOKEN> --insecure
🎯 Simulate Attacks & Detect
🔐 SSH Brute Force (Linux)
bash
Copy
Edit
hydra -l root -P passwords.txt 192.168.x.x ssh
Kibana Query:

kql
Copy
Edit
process.name: sshd AND event.outcome: failure
🖥️ RDP Brute Force (Windows)
bash
Copy
Edit
hydra -l Administrator -P rockyou.txt rdp://<windows-ip>
Kibana Query:

kql
Copy
Edit
event.code: "4625" AND winlog.event_data.LogonType: "10"
📝 File Integrity Monitoring (Linux)
bash
Copy
Edit
sudo yum install audit -y
sudo systemctl enable --now auditd
echo "-w /etc/passwd -p wa -k passwd_changes" | sudo tee -a /etc/audit/rules.d/audit.rules
sudo systemctl restart auditd
Kibana Query:

kql
Copy
Edit
auditd.log.key: passwd_changes
🧠 What You’ll Learn
🔍 How to build a functioning SIEM

🧪 How to simulate cyber attacks safely

🧠 How to detect malicious behavior using logs

📈 How to use Kibana for search and visualization

📸 Screenshots (Optional)
Add screenshots in /screenshots/ folder and embed them here:

markdown
Copy
Edit
![Kibana Dashboard](screenshots/kibana.png)
![Fleet Agent](screenshots/fleet-agent.png)
🙌 Final Notes
If you're preparing for SOC roles, TCS HackQuest, or just want to level up your defensive skills — this is a great lab to try. Star ⭐ the repo if you liked it!

Made with 💻 + ☕ by Anivesh Mohan

markdown
Copy
Edit

---

### ✅ Tips:
- Don’t use only `##` for everything — use a mix of `#`, `##`, `###`, and **bold text** to separate sections.
- Emojis and dividers `---` help visually break sections.
- Keep command examples in code blocks.
- Add visuals if possible.

Let me know if you want this version saved as a full `README.md` file or published on GitHub directly.
