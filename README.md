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
yum install java-11-openjdk-devel -y

3️⃣ Add Elastic Repository

Create a repo file:

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

5️⃣ Set Passwords for Built-in Users (Important for Security & Fleet)

Generate secure passwords for default users:

/usr/share/elasticsearch/bin/elasticsearch-setup-passwords auto

Confirm by typing y. Copy and save all the passwords shown:

PASSWORD elastic = <your-password>
PASSWORD kibana_system = <your-password>
PASSWORD logstash_system = <your-password>


You’ll need them later to log into Kibana and connect Elastic Agent/Fleet.

6️⃣ Install & Start Logstash
yum install logstash -y
systemctl enable logstash
systemctl start logstash

7️⃣ Install & Start Kibana
yum install kibana -y
systemctl enable kibana
systemctl start kibana

8️⃣ Allow Remote Access
✅ Elasticsearch Configuration
nano /etc/elasticsearch/elasticsearch.yml

Uncomment and set:

network.host: 0.0.0.0
http.port: 9200

Restart:

systemctl restart elasticsearch

