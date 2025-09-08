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
