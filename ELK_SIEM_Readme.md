
# 🛡️ Build Your Own SIEM with ELK Stack (CentOS Version)
🔍 Real-Time Log Monitoring | Threat Detection | SOC Practice Project

---

## 📘 Overview

This is a beginner-friendly cybersecurity project where you’ll build your own **SIEM (Security Information and Event Management)** system using the **ELK Stack** (Elasticsearch, Logstash, Kibana), **Fleet Server**, and **Elastic Agent**.

You'll simulate brute-force attacks (SSH, RDP), detect them in real time using Kibana, and learn how a real SOC works.

---

## 🧰 Tech Stack & Tools Used

### 🔧 Core Components
- **CentOS 7** – Server OS
- **Elasticsearch** – Log storage & indexing
- **Logstash** – Log pipeline & processing
- **Kibana** – Dashboard & visualization
- **Fleet & Elastic Agent** – Collect and manage logs

### 🛠️ Attack & Monitoring Tools
- `Hydra` – SSH & RDP brute-force simulation
- `auditd` – File Integrity Monitoring
- `KQL` – Kibana Query Language

---

## 🛠️ Setup Steps (CentOS)

### 1️⃣ Install Java & Add Elastic Repo

```bash
yum update -y
yum install java-11-openjdk-devel -y
```

📁 Create the Elastic repo:

```bash
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
```

---

### 2️⃣ Install ELK Stack Components

```bash
yum install elasticsearch -y
yum install logstash -y
yum install kibana -y
```

✅ Enable and start services:

```bash
systemctl enable elasticsearch logstash kibana
systemctl start elasticsearch logstash kibana
```

---

### 🔐 Set Built-in Passwords

```bash
/usr/share/elasticsearch/bin/elasticsearch-setup-passwords auto
```

🔑 Save all the passwords shown for:
- `elastic`
- `kibana_system`
- `logstash_system`

---

### 🔑 Generate Kibana Encryption Keys

```bash
cd /usr/share/kibana/bin
./kibana-encryption-keys generate
```

📌 Add them to `/etc/kibana/kibana.yml`:

```yaml
xpack.encryptedSavedObjects.encryptionKey: "<key1>"
xpack.reporting.encryptionKey: "<key2>"
xpack.security.encryptionKey: "<key3>"
```

Restart Kibana:

```bash
systemctl restart kibana
```

---

### 🌍 Configure Remote Access

#### 📡 Elasticsearch (`/etc/elasticsearch/elasticsearch.yml`):

```yaml
network.host: 0.0.0.0
http.port: 9200
```

#### 🌐 Kibana (`/etc/kibana/kibana.yml`):

```yaml
server.host: "0.0.0.0"
```

Restart services:

```bash
systemctl restart elasticsearch
systemctl restart kibana
```

---

## 🙌 Like This Project?

If this helped you, drop a ⭐️ and follow me for more cool projects.

> Made with ☕ + ❤️ by [Anivesh Mohan](https://github.com/Anivesh193)
