# Azure Enterprise Help Desk Infrastructure (3-Tier Cloud System)

## 📌 Overview

This project demonstrates the design and implementation of a **real-world, enterprise-style IT infrastructure** on Microsoft Azure.

The goal was not just to deploy cloud resources, but to build a **fully connected system** where:

* Users interact with a web interface
* Requests pass through a load-balanced web layer
* A backend application processes the request
* Data is retrieved from a cloud database

The backend system used in this project is a **custom-built Java Help Desk Ticket Management System**, integrated into Azure.

---

## 🚀 What This Project Demonstrates

* End-to-end cloud system design
* Network segmentation using subnets
* Load balancing and high availability
* Backend API development (Java)
* Database integration (Azure SQL)
* Internal DNS-based communication
* Real-world debugging and troubleshooting

---

## 🧠 Architecture Diagram

![Architecture Diagram](diagrams/00-architecture-diagram.png)

---

## 🏗️ Architecture Summary

### 🔹 Web Layer

* Two Linux VMs (`vm-web-1`, `vm-web-2`)
* Running nginx
* Behind Azure Load Balancer

### 🔹 Application Layer

* Java Help Desk system on `vm-app-1`
* Exposes API endpoints

### 🔹 Data Layer

* Azure SQL Database

### 🔹 Identity & Networking

* Windows Server (DNS)
* Internal hostname-based communication

---

## 🔄 End-to-End Traffic Flow

```text id="h6z42q"
User → Load Balancer → Web Server → Java API → Azure SQL Database
```

---

## 🌐 Network Configuration

### 🔹 Virtual Network

* Address space: `10.0.0.0/16`

### 🔹 Subnets

* Web → `10.0.1.0/24`
* App → `10.0.2.0/24`
* DB → `10.0.3.0/24`

### 🔹 Security (NSGs)

* Web allows HTTP from internet
* App allows only Web subnet
* DB allows only App subnet

---

## 💻 Web Layer (Nginx)

### 🔹 Setup

```bash id="q3z3v1"
sudo apt update
sudo apt install nginx -y
sudo systemctl start nginx
```

### 🔹 Role

* Entry point for users
* Reverse proxy to backend
* Load-balanced across servers

---

## ⚖️ Load Balancer

* Distributes traffic across web servers
* Provides redundancy
* Ensures high availability

---

## 🧩 Help Desk System (Backend Application Source)

The backend application used in this project was built as a separate project:

👉 https://github.com/mr-h4cker/helpdesk-ticket-system-azure

---

### 🔹 What it is

A **Java-based Help Desk Ticket Management System** that simulates real-world IT support workflows:

* Ticket creation
* Status tracking
* Priority management
* Issue monitoring

---

### 🔹 Why it was used

Instead of using dummy data, this project integrates a **real backend system** to simulate:

```text id="q1eh2n"
User issue → Ticket → Database → API → Web UI
```

---

### 🔹 What it adds

```text id="1v44i6"
✔ Real backend logic
✔ Real database interaction
✔ Real enterprise workflow
```

---

## 💻 Application Layer (Java API)

### 🔹 Deployment

```bash id="ntptn7"
git clone https://github.com/mr-h4cker/helpdesk-ticket-system-azure.git
cd helpdesk-ticket-system-azure
```

---

### 🔹 Compile & Run

```bash id="xxtzyt"
mkdir -p out
javac -cp "mssql-jdbc-12.8.1.jre11.jar" -d out $(find src -name "*.java")
java -cp "out:mssql-jdbc-12.8.1.jre11.jar" ApiServer
```

---

### 🔹 API Endpoints

```text id="k6l2q9"
/api/health
/api/tickets
```

---

## 🗄️ Azure SQL Database

### 🔹 Purpose

* Stores ticket data
* Managed cloud database

### 🔹 Connection

```java id="uj1ytc"
jdbc:sqlserver://<server-name>.database.windows.net:1433;
```

---

## 🪟 Identity Services (DNS)

### 🔹 Why it matters

Systems communicate using **hostnames**, not IP addresses.

---

### 🔹 DNS Records

```text id="1fptc6"
appserver.itinfra.local
web1.itinfra.local
web2.itinfra.local
```

---

## 🔗 End-to-End Integration

### 🔹 Nginx Reverse Proxy

```nginx id="cz7o1l"
location /api/ {
    proxy_pass http://appserver.itinfra.local:8080/api/;
}
```

---

### 🔹 Result

* Web servers connect to API
* API connects to database
* Full system works together

---

## 🔥 Live Ticket Data (FINAL FEATURE)

### 🔹 What was built

The web interface dynamically loads real ticket data from Azure SQL.

---

### 🔹 Flow

```text id="zdfgq1"
Browser → Web → API → Azure SQL
```

---

### 🔹 Output

![UI Top](screenshots/55-helpdesk-web-top.png)

![UI Bottom](screenshots/56-helpdesk-web-ui-bottom.png)

---

### 🔹 Result

```text id="9bp0ek"
Static page ❌ → Dynamic system ✔
```

---

## 🛠️ Troubleshooting & Challenges

### 🔹 Azure SQL Firewall

* Blocked VM access
* Fixed using VM public IP

```bash id="1ntt8y"
curl ifconfig.me
```

---

### 🔹 DNS Issue

* Used public IP instead of private
* Fixed DNS records

---

### 🔹 JSON Error

* Invalid API response
* Fixed JSON formatting

---

### 🔹 Nginx Proxy

* 404 due to incorrect path
* Fixed proxy_pass

---

## 🎯 Key Achievements

* Built full 3-tier system
* Integrated frontend, backend, and database
* Implemented real networking concepts
* Debugged real cloud issues

---

## 🧰 Skills Demonstrated

* Azure Networking (VNet, NSG)
* Linux Administration (nginx)
* Windows Server (DNS)
* Load Balancing
* Java Backend Development
* JDBC & SQL
* Cloud Troubleshooting

---

## 🚀 Future Improvements

* Convert API to Spring Boot
* Add authentication (AD integration)
* Use Private Endpoint for SQL
* Add monitoring

---

## 💬 Project Story

This project started as a simple Azure infrastructure lab.

Instead of stopping there, I extended it into a **fully working system** by:

* Adding a real backend application
* Connecting it to a cloud database
* Integrating it into the web layer
* Displaying real data in the browser

This helped me understand how individual components connect to form a real-world system.

---
