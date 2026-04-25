# Azure Enterprise IT Infrastructure Lab

## 📌 Overview

This project demonstrates the design and implementation of a cloud-based enterprise IT infrastructure using Microsoft Azure. The goal is to simulate a real-world environment that includes networking, system deployment, security, and application integration.

The architecture follows a 3-tier design with separate web, application, and data layers to ensure scalability, security, and maintainability.

---

## 🧠 Architecture Diagram

![Architecture Diagram](diagrams/00-architecture-diagram.png)

---

## 🏗️ Architecture Summary

* **Web Layer:** Two Linux-based virtual machines running nginx behind an Azure Load Balancer for high availability
* **Application Layer:** Java-based Help Desk system hosted on a virtual machine
* **Identity Services:** Windows Server providing Active Directory and DNS services
* **Data Layer:** Azure SQL Database for managed and scalable data storage
* **Networking:** Segmented Virtual Network with dedicated subnets and Network Security Groups (NSGs)
* **Security:** Controlled traffic flow between layers using NSG rules

---

## 🌐 Network Configuration

### 🔹 Virtual Network Creation

A Virtual Network (VNet) was created to simulate a private enterprise network in Azure.

* Address space: **10.0.0.0/16**

![VNet Basics](screenshots/01-vnet-basics.png)

![Address Space](screenshots/02-vnet-address-space.png)

---

### 🔹 Subnet Configuration

The network was divided into three subnets:

* **Web Subnet (10.0.1.0/24)** → Public-facing web layer
* **App Subnet (10.0.2.0/24)** → Internal application services
* **DB Subnet (10.0.3.0/24)** → Protected data layer

![Web Subnet](screenshots/03-web-subnet.png)

![App Subnet](screenshots/04-app-subnet.png)

![DB Subnet](screenshots/05-db-subnet.png)

![All Subnets](screenshots/06-all-subnets.png)

---

### 🔹 Network Security Groups (NSGs)

Network Security Groups were configured to control traffic between layers.

![NSG Web](screenshots/07-nsg-web-created.png)

![NSG App](screenshots/08-nsg-app-created.png)

![NSG DB](screenshots/09-nsg-db-created.png)

---

### 🔹 Security Rules

* Web subnet allows HTTP traffic from the internet
* App subnet allows traffic only from the web subnet
* DB subnet allows traffic only from the application subnet

![NSG Web Rules](screenshots/10-nsg-web-rules.png)

![NSG App Rules](screenshots/11-nsg-app-rules.png)

![NSG DB Rules](screenshots/12-nsg-db-rules.png)

---

### 🔹 NSG Association

Each NSG was linked to its respective subnet.

![NSG Association](screenshots/13-nsg-subnet-association.png)

---

## 💻 Web Server Deployment

### 🔹 Virtual Machines

Two Ubuntu servers were deployed:

* vm-web-1
* vm-web-2

![VM Web 1 Setup](screenshots/14-vm-web-1-basics.png)

![VM Web 1 Network](screenshots/15-vm-web-1-network.png)

---

### 🔹 Install Nginx

```bash id="n1"}
sudo apt update
sudo apt install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
```

![Nginx Installed](screenshots/17-nginx-installed.png)

---

### 🔹 Custom Web Interface

Each server displays a unique page:

* Web Server 1 → primary
* Web Server 2 → redundancy

![Web Server 1](screenshots/19-vm-web-1-custom-page.png)

![Web Server 2](screenshots/21-vm-web-2-custom-page.png)

---

### 🔹 Web Content

```id="n2"}
/web-content/
```

---

### 🔹 Purpose

* Enables load balancing
* Provides redundancy
* Simulates production setup

---

## ⚖️ Load Balancer Configuration

### 🔹 Overview

Azure Load Balancer distributes traffic across both web servers.

![Load Balancer](screenshots/22-load-balancer-basics.png)

---

### 🔹 Backend Pool

* vm-web-1
* vm-web-2

![Backend Pool](screenshots/24-backend-pool.png)

---

### 🔹 Health Probe

* HTTP
* Port 80

![Health Probe](screenshots/25-health-probe.png)

---

### 🔹 Rule

Traffic on port 80 is balanced across servers.

![LB Rule](screenshots/26-load-balancer-rule.png)

---

### 🔹 Testing

Refreshing shows different servers responding.

![LB Test 1](screenshots/27-load-balancer-test-1.png)

![LB Test 2](screenshots/28-load-balancer-test-2.png)

---

## 💻 Application Layer Deployment

### 🔹 Setup

Application server deployed in App Subnet:

* vm-app-1
* Ubuntu Server

![App VM Setup](screenshots/29-vm-app-basics.png)

![App VM Network](screenshots/30-vm-app-network.png)

![App Connected](screenshots/31-vm-app-connected.png)

---

### 🔹 Install Java

```bash id="n3"}
sudo apt update
sudo apt install openjdk-17-jdk -y
java -version
```

![Java Installed](screenshots/32-java-installed.png)

---

### 🔹 Deploy Project

```bash id="n4"}
git clone https://github.com/mr-h4cker/helpdesk-ticket-system-azure.git
cd helpdesk-ticket-system-azure
ls
find src -name "*.java"

wget https://repo1.maven.org/maven2/com/microsoft/sqlserver/mssql-jdbc/12.8.1.jre11/mssql-jdbc-12.8.1.jre11.jar
```

![Clone + Driver](screenshots/33-git-clone-and-jdbc-driver.png)

---

### 🔹 Compile & Run

```bash id="n5"}
mkdir -p out
javac -cp "mssql-jdbc-12.8.1.jre11.jar" -d out $(find src -name "*.java")
java -cp "out:mssql-jdbc-12.8.1.jre11.jar" Main
```

![Compile](screenshots/34-compile-project.png)

---

### 🔹 Success

Application connects to Azure SQL and runs:

![Success](screenshots/36-app-connected-success.png)

---

## 🛠️ Troubleshooting & Challenges

### 🔹 JDBC Driver Issue

The application initially failed due to missing JDBC driver.

**Fix:**
Downloaded driver and added to classpath.

---

### 🔹 Azure SQL Firewall Issue

Application could not connect due to blocked IP.

![Firewall Error](screenshots/35-sql-firewall-error.png)

---

### 🔍 Root Cause

VM IP was not allowed in Azure SQL firewall.

---

### 🔹 Solution

```bash id="n6"}
curl ifconfig.me
```

Added VM IP to Azure SQL firewall → connection successful.

---

### 💡 Key Learnings

* Cloud firewall rules matter
* VM IP ≠ local IP
* Dependencies must be managed
* Real debugging builds real skills

---
## 🗄️ Azure SQL Database Integration

### 🔹 Overview

An Azure SQL Database was used as the data layer for the application. This provides a managed, scalable, and secure database service without requiring manual server maintenance.

---

### 🔹 Database Setup

A SQL Server and database were created in Azure.

![SQL Overview](screenshots/37-azure-sql-overview.png)

![SQL Database](screenshots/38-azure-sql-database.png)

---

### 🔹 Firewall Configuration

Access to the database was controlled using firewall rules.

* Only trusted IP addresses are allowed
* The application server’s public IP was added

![SQL Firewall](screenshots/39-sql-firewall-settings.png)

---

### 🔹 Java Integration (JDBC)

The application connects to Azure SQL using JDBC.

Example connection setup:

```java id="sql1"
String url = "jdbc:sqlserver://<server-name>.database.windows.net:1433;"
           + "database=<db-name>;"
           + "user=<username>;"
           + "password=<password>;"
           + "encrypt=true;"
           + "trustServerCertificate=false;"
           + "loginTimeout=30;";
```

---

### 🔹 Connection Flow

1. Application runs on VM
2. JDBC driver initiates connection
3. Azure SQL validates IP via firewall
4. Database connection established

---

### 🔹 Purpose

* Provides persistent data storage
* Separates application logic from data layer
* Simulates real-world cloud database usage

---


## 🎯 Objectives

* Simulate enterprise IT environment
* Apply networking & security concepts
* Deploy real applications in cloud
* Implement high availability architecture

---
