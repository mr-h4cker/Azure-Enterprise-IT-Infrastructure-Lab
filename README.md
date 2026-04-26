# Azure Enterprise Help Desk Infrastructure  
## 3-Tier Cloud System on Microsoft Azure

---

## Recruiter Summary

I built and documented a cloud-based 3-tier IT support system on Microsoft Azure that connects networking, backend logic, and database services into one working application.

The project includes:

- Azure Virtual Network with segmented subnets
- Network Security Groups for traffic control
- Two Linux nginx web servers behind an Azure Load Balancer
- Java backend API running on a separate application server
- Azure SQL Database storing live Help Desk ticket data
- Windows Server DNS for internal hostname-based communication
- End-to-end web interface displaying real database records

This project demonstrates how enterprise systems are designed, deployed, connected, and troubleshot across multiple layers.

---

## Problem Statement

Many enterprise IT environments are not made of one single server. They usually have separate layers for:

- Public-facing web access
- Internal application logic
- Centralized database storage
- Network security and access control
- Internal DNS/name resolution

The goal of this project was to simulate that kind of environment in Azure and build a working system where users can access a web page that retrieves live Help Desk ticket data from a backend API and Azure SQL Database.

---

## Final System Flow

```text
User → Azure Load Balancer → nginx Web Servers → Java API → Azure SQL Database
````

---

## Architecture Diagram

![Architecture Diagram](diagrams/00-architecture-diagram.png)

### Diagram Explanation

The architecture is divided into multiple layers:

* The user connects through the public Azure Load Balancer.
* The Load Balancer distributes traffic across two nginx web servers.
* The web servers act as reverse proxies and forward API requests to the application server.
* The Java API runs on the application server and handles backend logic.
* The API retrieves ticket data from Azure SQL Database.
* Windows Server DNS provides internal hostname resolution for systems such as `appserver.itinfra.local`.

---

## Design Decisions

### Why use a Load Balancer?

The Load Balancer distributes incoming traffic across two web servers. This improves availability and avoids depending on a single web server.

### Why separate Web and App layers?

The web layer handles public traffic, while the application layer handles backend logic. This separation reflects real enterprise architecture and improves security and maintainability.

### Why use Azure SQL?

Azure SQL provides a managed database service without needing to maintain a database server manually. It also helped me practice cloud database connectivity using JDBC.

### Why use DNS?

Instead of hardcoding private IP addresses, internal systems communicate using hostnames such as:

```text
appserver.itinfra.local
web1.itinfra.local
web2.itinfra.local
```

This is closer to how real enterprise environments are managed.

### Why use nginx reverse proxy?

nginx allows the public web layer to forward `/api/` requests to the internal Java API server while keeping the backend server separate from direct public access.

---

## What This Project Demonstrates

* 3-tier cloud architecture
* Azure networking and subnet design
* Network Security Groups and traffic control
* Linux server administration
* nginx web hosting and reverse proxy
* Azure Load Balancer configuration
* Java API deployment
* JDBC database connectivity
* Azure SQL integration
* Windows Server DNS configuration
* Real troubleshooting across networking, application, and database layers

---

## Network Design

### Virtual Network

```text
VNet: 10.0.0.0/16
```

### Subnets

```text
Web Subnet: 10.0.1.0/24
App Subnet: 10.0.2.0/24
DB Subnet: 10.0.3.0/24
```

### Security Model

* Web subnet accepts HTTP traffic from the internet.
* App subnet accepts traffic from the web subnet.
* DB layer is protected and only accessed by the application layer.
* NSGs are used to control traffic between layers.

![Subnets](screenshots/06-all-subnets.png)

![NSG Rules](screenshots/10-nsg-web-rules.png)

---

## Web Layer

Two Ubuntu VMs were deployed:

```text
vm-web-1
vm-web-2
```

Both servers run nginx.

### nginx Installation

```bash
sudo apt update
sudo apt install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
```

### Role of the Web Layer

The web layer:

* Receives user traffic
* Serves the frontend page
* Forwards API requests to the application server
* Provides redundancy through load balancing

---

## Load Balancer

Azure Load Balancer was configured to distribute traffic across both web servers.

### Components

* Frontend public IP
* Backend pool containing `vm-web-1` and `vm-web-2`
* HTTP health probe
* Load balancing rule for port 80

![Load Balancer](screenshots/22-load-balancer-basics.png)

---

## Backend System

The backend application used in this project comes from my separate Help Desk system repository:

```text
https://github.com/mr-h4cker/helpdesk-ticket-system-azure
```

This is a Java-based Help Desk Ticket Management System that simulates real IT support workflows such as:

* Creating tickets
* Tracking ticket status
* Managing priority
* Storing issue data
* Retrieving ticket records from a database

Instead of using dummy data, this Azure infrastructure project connects to a real backend system.

---

## Application Layer

The Java application runs on a separate application server:

```text
vm-app-1
```

### Clone the Backend Project

```bash
git clone https://github.com/mr-h4cker/helpdesk-ticket-system-azure.git
cd helpdesk-ticket-system-azure
```

### Download JDBC Driver

```bash
wget https://repo1.maven.org/maven2/com/microsoft/sqlserver/mssql-jdbc/12.8.1.jre11/mssql-jdbc-12.8.1.jre11.jar
```

### Compile and Run API

```bash
mkdir -p out
javac -cp "mssql-jdbc-12.8.1.jre11.jar" -d out $(find src -name "*.java")
java -cp "out:mssql-jdbc-12.8.1.jre11.jar" ApiServer
```

### API Endpoints

```text
/api/health
/api/tickets
```

### API Purpose

* `/api/health` confirms that the API is running.
* `/api/tickets` retrieves ticket records from Azure SQL.

![Application Connected](screenshots/36-app-connected-success.png)

---

## Azure SQL Database

Azure SQL Database is used as the data layer for the Help Desk system.

### Purpose

Azure SQL stores ticket data and allows the Java backend API to retrieve records through JDBC.

### JDBC Connection Format

```java
jdbc:sqlserver://<server-name>.database.windows.net:1433;
```

![SQL Database](screenshots/38-azure-sql-database.png)

---

## Windows Server DNS

A Windows Server VM was deployed to simulate enterprise DNS/domain services.

### DNS Records

```text
appserver.itinfra.local
web1.itinfra.local
web2.itinfra.local
```

DNS is actively used so internal systems can communicate using hostnames instead of private IP addresses.

![DNS Records](screenshots/47-dns-records-created.png)

![DNS Test](screenshots/49-dns-resolution-test.png)

### Note

Domain services were configured as part of the Windows Server environment, but full Active Directory authentication is planned as a future improvement.

---

## End-to-End Integration

The web servers were configured to forward API requests to the backend application server.

### nginx Reverse Proxy Configuration

```nginx
location /api/ {
    proxy_pass http://appserver.itinfra.local:8080/api/;
}
```

This allows the user to access the API through the Load Balancer and web layer instead of directly accessing the application server.

![Full Flow](screenshots/53-full-flow-working.png)

---

## Live Ticket Data Display

The web interface was upgraded to dynamically fetch and display live ticket data from Azure SQL.

### Final Flow

```text
Browser → Load Balancer → Web Server → Java API → Azure SQL
```

### Output

![Help Desk UI Top](screenshots/55-helpdesk-web-top.png)

![Help Desk UI Bottom](screenshots/56-helpdesk-web-ui-bottom.png)

### Result

The web page is no longer static. It displays real database records coming from the backend Help Desk system.

---

## How to Recreate This Lab

### 1. Create Azure Resource Group

Create one resource group to keep all resources organized.

Example:

```text
rg-it-infra-lab
```

### 2. Create Virtual Network

Create a VNet with address space:

```text
10.0.0.0/16
```

Create subnets:

```text
web-subnet: 10.0.1.0/24
app-subnet: 10.0.2.0/24
db-subnet: 10.0.3.0/24
```

### 3. Configure NSGs

Create NSGs for each layer:

```text
nsg-web
nsg-app
nsg-db
```

Allow only the required traffic between layers.

### 4. Deploy Web Servers

Create two Ubuntu VMs in the web subnet and install nginx.

```bash
sudo apt update
sudo apt install nginx -y
sudo systemctl start nginx
```

### 5. Configure Load Balancer

Create an Azure Load Balancer with:

* Public frontend IP
* Backend pool containing both web VMs
* HTTP health probe
* Port 80 load balancing rule

### 6. Deploy Application Server

Create an Ubuntu VM in the app subnet.

Install Java:

```bash
sudo apt update
sudo apt install openjdk-17-jdk -y
java -version
```

Clone and run the backend API:

```bash
git clone https://github.com/mr-h4cker/helpdesk-ticket-system-azure.git
cd helpdesk-ticket-system-azure
wget https://repo1.maven.org/maven2/com/microsoft/sqlserver/mssql-jdbc/12.8.1.jre11/mssql-jdbc-12.8.1.jre11.jar
mkdir -p out
javac -cp "mssql-jdbc-12.8.1.jre11.jar" -d out $(find src -name "*.java")
java -cp "out:mssql-jdbc-12.8.1.jre11.jar" ApiServer
```

### 7. Create Azure SQL Database

Create Azure SQL Server and Database.

Update the Java database connection settings with:

```text
server name
database name
username
password
```

### 8. Configure Azure SQL Firewall

Find the app VM public IP:

```bash
curl ifconfig.me
```

Add this IP to Azure SQL firewall rules.

### 9. Configure Windows DNS

Create DNS records:

```text
appserver.itinfra.local
web1.itinfra.local
web2.itinfra.local
```

Configure Linux servers to use the Windows Server as DNS resolver.

### 10. Configure nginx Reverse Proxy

On both web servers, update nginx:

```nginx
location /api/ {
    proxy_pass http://appserver.itinfra.local:8080/api/;
}
```

Restart nginx:

```bash
sudo nginx -t
sudo systemctl restart nginx
```

### 11. Test Full System

Test:

```text
http://<load-balancer-ip>/api/health
http://<load-balancer-ip>/api/tickets
```

Then open the main webpage to confirm live ticket data is displayed.

---

## Troubleshooting and Challenges

### Azure SQL Firewall Issue

The application server could not connect to Azure SQL because its public IP was not allowed.

Fix:

```bash
curl ifconfig.me
```

The VM public IP was added to Azure SQL firewall rules.

---

### JDBC Driver Issue

The Java app required the Microsoft SQL Server JDBC driver.

Fix:

```bash
wget https://repo1.maven.org/maven2/com/microsoft/sqlserver/mssql-jdbc/12.8.1.jre11/mssql-jdbc-12.8.1.jre11.jar
```

The driver was added to the runtime classpath.

---

### DNS Misconfiguration

DNS records were initially created using public IP addresses.

Issue:

* Hostnames resolved
* Internal connectivity failed

Fix:

* Updated DNS records to use private IP addresses

Learning:

```text
DNS resolution does not always mean connectivity.
Internal systems should use private IP addresses.
```

---

### nginx Reverse Proxy Issue

The full flow initially returned a 404 error because the request path was not forwarded correctly.

Fix:

```nginx
proxy_pass http://appserver.itinfra.local:8080/api/;
```

---

### JSON Parsing Issue

The API initially returned invalid JSON due to incorrect formatting.

Fix:

* Corrected the JSON array closing bracket
* Improved JSON escaping in the API response

---

## What I Learned

This project helped me understand:

* How cloud networks are segmented
* How public and private IPs behave differently
* How NSGs and firewalls control traffic
* How nginx reverse proxy connects frontend and backend systems
* How Java connects to Azure SQL using JDBC
* How DNS is used in internal enterprise communication
* How real troubleshooting happens across multiple layers

---

## Skills Demonstrated

* Azure Virtual Network
* Azure Subnets
* Network Security Groups
* Azure Load Balancer
* Azure SQL Database
* Linux server administration
* nginx web server configuration
* nginx reverse proxy
* Windows Server DNS
* Java backend API
* JDBC database connectivity
* SQL troubleshooting
* Cloud debugging
* 3-tier architecture design

---

## Full Build Screenshots

All detailed step-by-step screenshots are available in the `/screenshots` folder.

The main README only includes the most important proof screenshots to keep the project readable.

---

## Future Improvements

Planned improvements:

* Convert the API to Spring Boot
* Add user authentication with Active Directory
* Use Azure Private Endpoint for SQL
* Add Azure Monitor and Log Analytics
* Add HTTPS with a domain name
* Automate deployment using Azure CLI or Terraform

---

## Project Story

This project started as a simple Azure infrastructure lab.

Instead of stopping after deploying VMs, I kept extending it:

1. Built the network foundation
2. Added web servers
3. Added load balancing
4. Deployed a Java backend
5. Connected it to Azure SQL
6. Added internal DNS
7. Connected the full flow through nginx
8. Displayed real database data in the browser

This helped me understand how systems are not just deployed, but connected and maintained in real environments.

```
```
