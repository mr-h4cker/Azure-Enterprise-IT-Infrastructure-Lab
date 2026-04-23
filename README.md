# Azure Enterprise IT Infrastructure Lab

##  Overview

This project demonstrates the design and implementation of a cloud-based enterprise IT infrastructure using Microsoft Azure. The goal is to simulate a real-world environment that includes networking, system deployment, security, and application integration.

The architecture follows a 3-tier design with separate web, application, and data layers to ensure scalability, security, and maintainability.

---

##  Architecture Diagram

![Architecture Diagram](diagrams/00-architecture-diagram.png)

---

##  Architecture Summary

* **Web Layer:** Two Linux-based virtual machines running nginx behind an Azure Load Balancer for high availability
* **Application Layer:** Java-based Help Desk system hosted on a virtual machine
* **Identity Services:** Windows Server providing Active Directory and DNS services
* **Data Layer:** Azure SQL Database for managed and scalable data storage
* **Networking:** Segmented Virtual Network with dedicated subnets and Network Security Groups (NSGs)
* **Security:** Controlled traffic flow between layers using NSG rules

---

##  Objectives

* Simulate a real-world enterprise IT environment
* Apply networking concepts such as subnets, traffic flow, and access control
* Integrate application and database systems in a cloud environment
* Implement high availability and security best practices

---
