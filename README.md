# Azure Enterprise Help Desk Infrastructure (3-Tier Cloud System)

---

## 🎯 Recruiter Summary

Built and deployed a **cloud-based 3-tier IT support system** on Microsoft Azure using:

* Azure Virtual Networks, Subnets, and NSGs
* Linux (nginx) web servers with load balancing
* Java backend API (Help Desk system)
* Azure SQL Database
* Windows Server (DNS / domain services)
* Real-world troubleshooting and debugging

This project demonstrates how modern enterprise systems connect **networking + backend + database + web interface** into a working solution.

---

## 📌 Project Overview

This project was designed to simulate a **real enterprise IT environment**, not just a basic cloud lab.

Instead of deploying isolated components, the goal was to build a **fully connected system** where:

* Users access a web interface
* Requests flow through a load-balanced web layer
* A backend API processes the request
* Data is retrieved from a cloud database

Final system flow:

```text
User → Load Balancer → Web Server → Java API → Azure SQL Database
```

---

## 🧠 Architecture Diagram

![Architecture Diagram](diagrams/00-architecture-diagram.png)

---

## 🏗️ Architecture Overview

### 🔹 Web Layer

* Two Ubuntu VMs (`vm-web-1`, `vm-web-2`)
* Running nginx
* Behind Azure Load Balancer

### 🔹 Application Layer

* Java-based Help Desk system (`vm-app-1`)
* Exposes REST-like API endpoints

### 🔹 Data Layer

* Azure SQL Database (managed service)

### 🔹 Identity & Networking

* Windows Server for DNS and domain services
* Internal hostname-based communication

### 🔹 Security

* NSGs restrict traffic between layers
* Segmented subnets for isolation

---

## 🔄 End-to-End System Flow

```text
1. User opens web page
2. Request hits Azure Load Balancer
3. Routed to one of the nginx web servers
4. Nginx forwards /api requests to app server
5. Java API processes request
6. Data fetched from Azure SQL
7. Response returned to browser
```

---

## 🌐 Network Design

### 🔹 Virtual Network

* Address space: `10.0.0.0/16`

### 🔹 Subnets

* Web → `10.0.1.0/24`
* App → `10.0.2.0/24`
* DB → `10.0.3.0/24`

### 🔹 Security (NSGs)

* Web → Allows HTTP from internet
* App → Allows only Web subnet
* DB → Allows only App subnet

---

## 💻 Web Layer (nginx)

### 🔹 Setup

```bash
sudo apt update
sudo apt install nginx -y
sudo systemctl start nginx
```

### 🔹 Role

* Entry point for users
* Reverse proxy to backend API
* Load-balanced across two servers

---

## ⚖️ Load Balancer

* Distributes traffic across web servers
* Uses health probes for availability
* Ensures high availability and redundancy

---

## 🧩 Backend System (Help Desk Application)

⚠️ **Important:**
This repository contains the **infrastructure**.

The backend application comes from a separate project:

👉 https://github.com/mr-h4cker/helpdesk-ticket-system-azure

---

### 🔹 What the system does

A Java-based Help Desk system that simulates:

* Ticket creation
* Status tracking
* Priority handling
* Issue management

---

### 🔹 Why this matters

Instead of using dummy data, this project integrates a **real backend system**:

```text
User request → Ticket system → Database → API → Web UI
```

This makes the project **realistic and production-like**.

---

## 💻 Application Layer (Java API)

### 🔹 Deployment

```bash
git clone https://github.com/mr-h4cker/helpdesk-ticket-system-azure.git
cd helpdesk-ticket-system-azure
```

---

### 🔹 Compile & Run

```bash
mkdir -p out
javac -cp "mssql-jdbc-12.8.1.jre11.jar" -d out $(find src -name "*.java")
java -cp "out:mssql-jdbc-12.8.1.jre11.jar" ApiServer
```

---

### 🔹 API Endpoints

```text
/api/health
/api/tickets
```

---

## 🗄️ Azure SQL Database

* Stores Help Desk ticket data
* Managed cloud service

### 🔹 Connection

```java
jdbc:sqlserver://<server-name>.database.windows.net:1433;
```

---

## 🪟 Windows Server (DNS & Domain Services)

### 🔹 What is actually used

* DNS is actively used for internal communication
* Domain services are configured but not fully integrated yet

---

### 🔹 DNS Setup

```text
appserver.itinfra.local
web1.itinfra.local
web2.itinfra.local
```

---

### 🔹 Why this matters

* Systems communicate using hostnames
* Not hardcoded IP addresses
* Matches real enterprise environments

---

## 🔗 End-to-End Integration

### 🔹 Nginx Reverse Proxy

```nginx
location /api/ {
    proxy_pass http://appserver.itinfra.local:8080/api/;
}
```

---

### 🔹 Result

* Web → API → Database fully connected
* All layers working together

---

## 🔥 Live Data Display (FINAL FEATURE)

### 🔹 What was built

The web interface dynamically loads ticket data from Azure SQL.

---

### 🔹 Flow

```text
Browser → Web → API → Azure SQL
```

---

### 🔹 Output

![UI Top](screenshots/55-helpdesk-web-top.png)

![UI Bottom](screenshots/56-helpdesk-web-ui-bottom.png)

---

### 🔹 Result

* Static page → Dynamic application
* Real database data displayed in browser

---

## 🛠️ Troubleshooting & Challenges

### 🔹 Azure SQL Firewall

* VM could not connect
* Fixed using public IP:

```bash
curl ifconfig.me
```

---

### 🔹 DNS Misconfiguration

* Used public IP instead of private
* Fixed DNS records

---

### 🔹 Nginx Proxy Issue

* 404 error due to wrong path
* Fixed proxy configuration

---

### 🔹 JSON Parsing Issue

* Invalid API response
* Fixed JSON formatting

---

## 📚 What I Learned

* How cloud networking actually works (VNet, NSGs, subnets)
* Difference between public vs private IP communication
* How reverse proxies connect frontend and backend
* How real systems use DNS instead of IPs
* Importance of debugging in cloud environments
* How different layers (web, app, database) interact

---

## 🎯 Key Achievements

* Built full 3-tier architecture
* Connected frontend, backend, and database
* Implemented real enterprise patterns
* Solved real-world cloud issues

---

## 🧰 Skills Demonstrated

* Azure Networking (VNet, NSG, Subnets)
* Linux (nginx, systemctl)
* Windows Server (DNS)
* Load Balancing
* Java Backend Development
* JDBC & SQL
* Cloud Troubleshooting

---

## 🚀 Future Improvements

* Convert API to Spring Boot
* Add authentication using Active Directory
* Use Private Endpoint for SQL
* Add monitoring and alerts

---

## 💬 Project Story

This project started as a basic Azure lab.

Instead of stopping there, it was extended into a **fully working system** by:

* Adding a real backend application
* Connecting it to a cloud database
* Integrating it with the web layer
* Displaying real data in the browser

This helped develop a deeper understanding of how **real systems are built and connected**, not just deployed.

---
