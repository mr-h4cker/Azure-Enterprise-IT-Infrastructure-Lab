# Azure Enterprise Help Desk Infrastructure (3-Tier Cloud System)

---

## Recruiter Summary

I built a cloud-based 3-tier system on Microsoft Azure that connects networking, backend logic, and a database into a working application.

The system includes:

* Azure Virtual Network with segmented subnets
* Load-balanced Linux web servers running nginx
* A Java-based backend API
* Azure SQL Database
* Windows Server for internal DNS
* End-to-end integration with live data displayed in the browser

This project focuses on how real systems are structured and connected, not just deployed.

---

## Project Overview

The goal of this project was to simulate a real-world enterprise IT environment.

Instead of building isolated components, I focused on creating a system where everything works together:

* A user accesses a web page
* Traffic passes through a load balancer
* The web server forwards requests to a backend API
* The backend retrieves data from a database
* The result is returned to the browser

Final system flow:

```text id="flow1"
User → Load Balancer → Web Server → Java API → Azure SQL Database
```

---

## Architecture Diagram

![Architecture Diagram](diagrams/00-architecture-diagram.png)

---

## Architecture Overview

### Web Layer

Two Ubuntu virtual machines (`vm-web-1`, `vm-web-2`) running nginx.
They act as the entry point for users and are placed behind an Azure Load Balancer.

### Application Layer

A Java-based Help Desk system running on `vm-app-1`.
This layer exposes API endpoints used by the web servers.

### Data Layer

Azure SQL Database is used to store ticket data.
It provides a managed and scalable database solution.

### Identity and Networking

A Windows Server VM is configured for DNS and domain services.
Internal communication uses hostnames instead of IP addresses.

### Security

Network Security Groups (NSGs) are used to restrict traffic between layers.

---

## End-to-End System Flow

```text id="flow2"
1. User opens the web page
2. Request reaches the Azure Load Balancer
3. Load balancer forwards traffic to a web server
4. nginx forwards API requests to the application server
5. Java API processes the request
6. Data is retrieved from Azure SQL
7. Response is returned to the user
```

---

## Network Design

![Subnets](screenshots/06-all-subnets.png)

![NSG Rules](screenshots/10-nsg-web-rules.png)

The network is divided into three subnets:

* Web subnet: handles public traffic
* Application subnet: internal backend communication
* Database subnet: protected data layer

NSGs ensure that:

* Only the web layer is exposed to the internet
* The application layer only accepts traffic from the web layer
* The database only accepts traffic from the application layer

---

## Web Layer (nginx)

### Setup

```bash id="cmd1"
sudo apt update
sudo apt install nginx -y
sudo systemctl start nginx
```

### Role

* Handles incoming user requests
* Acts as a reverse proxy
* Forwards API requests to the backend
* Provides redundancy through load balancing

---

## Load Balancer

![Load Balancer](screenshots/22-load-balancer-basics.png)

The Azure Load Balancer distributes traffic across both web servers.

This ensures:

* High availability
* Fault tolerance
* Even traffic distribution

---

## Backend System (Help Desk Application)

This repository contains the infrastructure setup.

The backend application is implemented in a separate repository:

https://github.com/mr-h4cker/helpdesk-ticket-system-azure

### What the system does

The Help Desk system simulates a real IT support workflow:

* Tracking tickets
* Managing status
* Handling priorities
* Storing issue data

### Why this matters

Instead of displaying static or fake data, this project uses a real backend system.

```text id="flow3"
User request → Ticket system → Database → API → Web UI
```

This makes the project much closer to how real systems operate.

---

## Application Layer (Java API)

### Deployment

```bash id="cmd2"
git clone https://github.com/mr-h4cker/helpdesk-ticket-system-azure.git
cd helpdesk-ticket-system-azure
```

### Compile and Run

```bash id="cmd3"
mkdir -p out
javac -cp "mssql-jdbc-12.8.1.jre11.jar" -d out $(find src -name "*.java")
java -cp "out:mssql-jdbc-12.8.1.jre11.jar" ApiServer
```

### API Endpoints

```text id="api1"
/api/health
/api/tickets
```

### Proof

![Application Connected](screenshots/36-app-connected-success.png)

---

## Azure SQL Database

![SQL Database](screenshots/38-azure-sql-database.png)

Azure SQL is used as the data layer.

It:

* Stores ticket records
* Is accessed using JDBC
* Uses firewall rules for controlled access

---

## Windows Server (DNS and Domain Services)

### What is used

DNS is actively used in this project.
Domain services are configured but not fully integrated yet.

### DNS Records

```text id="dns1"
appserver.itinfra.local
web1.itinfra.local
web2.itinfra.local
```

### Proof

![DNS Records](screenshots/47-dns-records-created.png)

![DNS Test](screenshots/49-dns-resolution-test.png)

### Why this matters

Systems communicate using hostnames instead of IP addresses, which reflects real enterprise environments.

---

## End-to-End Integration

### Reverse Proxy Configuration

```nginx id="nginx1"
location /api/ {
    proxy_pass http://appserver.itinfra.local:8080/api/;
}
```

### Full Flow

![Full Flow](screenshots/53-full-flow-working.png)

This connects all layers of the system:

* Web servers communicate with the API
* The API communicates with the database

---

## Live Data Display

### What was built

The web interface dynamically retrieves and displays ticket data from Azure SQL.

### Flow

```text id="flow4"
Browser → Web Server → API → Azure SQL
```

### Output

![UI Top](screenshots/55-helpdesk-web-top.png)

![UI Bottom](screenshots/56-helpdesk-web-ui-bottom.png)

### Result

The web page is no longer static.
It displays real data coming from the backend system and database.

---

## Troubleshooting and Challenges

### Azure SQL Firewall

The application could not connect to the database initially.

Fix:

```bash id="cmd4"
curl ifconfig.me
```

The VM’s IP was added to the Azure SQL firewall.

---

### DNS Misconfiguration

DNS records were initially set using public IPs.

Issue:

* Name resolution worked
* Connectivity failed

Fix:

* Switched to private IP addresses

---

### JSON Parsing Issue

The API returned invalid JSON due to incorrect formatting.

Fix:

* Corrected the JSON structure in the response

---

### Nginx Reverse Proxy Issue

Requests returned a 404 error.

Fix:

* Corrected the proxy path configuration

---

## What I Learned

* How virtual networks and subnets work in cloud environments
* The difference between public and private IP communication
* How reverse proxies connect frontend and backend systems
* The importance of DNS in real-world infrastructure
* How different system layers interact
* How to debug real deployment issues

---

## Key Achievements

* Built a complete 3-tier architecture
* Integrated web, application, and database layers
* Used real backend logic instead of mock data
* Solved real-world cloud issues

---

## Skills Demonstrated

* Azure Networking (VNet, Subnets, NSGs)
* Linux Administration (nginx, systemctl)
* Windows Server (DNS)
* Load Balancing
* Java Backend Development
* JDBC and SQL
* Cloud Troubleshooting

---

## Full Build Screenshots

All detailed screenshots are available in the `/screenshots` folder.

---

## Future Improvements

* Convert API to Spring Boot
* Add authentication using Active Directory
* Use Private Endpoint for SQL
* Add monitoring and logging

---

## Project Story

This project started as a simple Azure lab.

Instead of stopping there, I extended it into a fully working system by:

* Adding a real backend application
* Connecting it to a cloud database
* Integrating it with the web layer
* Displaying live data in the browser

This helped me understand how real systems are built and connected, not just deployed.
