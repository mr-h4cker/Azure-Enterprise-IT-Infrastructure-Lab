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

The network was divided into three subnets to separate different layers of the architecture:

* **Web Subnet (10.0.1.0/24)** → Public-facing web layer
* **App Subnet (10.0.2.0/24)** → Internal application services
* **DB Subnet (10.0.3.0/24)** → Protected data layer

![Web Subnet](screenshots/03-web-subnet.png)

![App Subnet](screenshots/04-app-subnet.png)

![DB Subnet](screenshots/05-db-subnet.png)

![All Subnets](screenshots/06-all-subnets.png)

---

### 🔹 Network Security Groups (NSGs)

Network Security Groups were created to control traffic between subnets and enforce security rules.

![NSG Web](screenshots/07-nsg-web-created.png)

![NSG App](screenshots/08-nsg-app-created.png)

![NSG DB](screenshots/09-nsg-db-created.png)

---

### 🔹 Security Rules Configuration

Each subnet was secured with specific inbound rules:

* Web subnet allows HTTP traffic from the internet
* App subnet allows traffic only from the web subnet
* DB subnet allows traffic only from the application subnet

![NSG Web Rules](screenshots/10-nsg-web-rules.png)

![NSG App Rules](screenshots/11-nsg-app-rules.png)

![NSG DB Rules](screenshots/12-nsg-db-rules.png)

---

### 🔹 NSG Association

Each NSG was associated with its corresponding subnet to enforce security boundaries.

![NSG Association](screenshots/13-nsg-subnet-association.png)

---

## 💻 Web Server Deployment

### 🔹 Virtual Machine Setup

Two Linux-based virtual machines were deployed in the Web Subnet to host web services.

* **VM 1:** vm-web-1
* **VM 2:** vm-web-2
* OS: Ubuntu Server 22.04

![VM Web 1 Setup](screenshots/14-vm-web-1-basics.png)

![VM Web 1 Network](screenshots/15-vm-web-1-network.png)

---

### 🔹 Web Server Configuration

Nginx was installed on both virtual machines to serve web content.

![Nginx Installation](screenshots/17-nginx-installed.png)

---

### 🔹 Custom Web Interface

Each web server was configured with a custom HTML page to clearly identify which server is responding.

The interface also provides a brief overview of the project, simulating a real-world production environment.

* Web Server 1 handles primary responses
* Web Server 2 supports redundancy and load sharing

![Web Server 1 Page](screenshots/19-vm-web-1-custom-page.png)

![Web Server 2 Page](screenshots/21-vm-web-2-custom-page.png)

---

### 🔹 Web Content

The HTML files used for the web servers are included in the repository:

```
/web-content/
```

---

### 🔹 Purpose

* Enables load balancing across multiple servers
* Provides redundancy in case one server fails
* Simulates a production web environment

---

## ⚖️ Load Balancer Configuration

### 🔹 Overview

An Azure Load Balancer was configured to distribute incoming traffic across multiple web servers, ensuring high availability and fault tolerance.

![Load Balancer Setup](screenshots/22-load-balancer-basics.png)

---

### 🔹 Backend Pool

Both web servers were added to the backend pool:

* vm-web-1
* vm-web-2

![Backend Pool](screenshots/24-backend-pool.png)

---

### 🔹 Health Probe

A health probe was configured to monitor the availability of each web server.

* Protocol: HTTP
* Port: 80

![Health Probe](screenshots/25-health-probe.png)

---

### 🔹 Load Balancing Rule

Traffic on port 80 is distributed across both servers.

![Load Balancer Rule](screenshots/26-load-balancer-rule.png)

---

### 🔹 Testing

The load balancer was tested by accessing its public IP and refreshing the page multiple times.

The response alternates between Web Server 1 and Web Server 2, confirming successful load balancing.

![LB Test 1](screenshots/27-load-balancer-test-1.png)

![LB Test 2](screenshots/28-load-balancer-test-2.png)

---

### 🔹 Purpose

* Distributes incoming traffic across multiple servers
* Prevents a single point of failure
* Improves system reliability and availability

---

## 🎯 Objectives

* Simulate a real-world enterprise IT environment
* Apply networking concepts such as subnets, traffic flow, and access control
* Integrate application and database systems in a cloud environment
* Implement high availability and security best practices

---
