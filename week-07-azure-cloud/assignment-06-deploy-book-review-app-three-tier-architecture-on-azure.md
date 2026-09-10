# Assignment 6 — Capstone: Deploy Book Review App (Three-Tier Architecture) on Azure

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

This is the most important assignment of the course. You will deploy the Book Review App in a production-ready, best-practice-compliant three-tier architecture on Azure: separated presentation, application, and database tiers, least-privilege network access, a controlled public entry point, protected secrets, and availability/monitoring evidence.

---

# Task 1 — Design the Azure Three-Tier Architecture

## Goal

Create an architecture diagram and implementation plan identifying the presentation, application, and database components, the chosen Azure services, the public entry point, and the internal traffic paths.

### Evidence

#### Screenshot 1 — Architecture diagram showing the public entry point, three tiers, network boundaries, and traffic flow

Add your screenshot here.

---

#### Screenshot 2 — Written architecture assumptions and selected Azure services

![output ](screenshots/A6Screenshot2.png)

---

# Task 2 — Create the Azure Network Foundation

## Goal

Create a dedicated Resource Group and VNet with separate subnets for the web, application, and database tiers, keeping the application and database tiers without direct public access.

### Evidence

#### Screenshot 3 — Resource Group overview showing the assignment resources

![output ](screenshots/A6Screenshot3.png)

---

#### Screenshot 4 — VNet overview showing the address space and all required subnets

![output ](screenshots/A6Screenshot4.png)

---

#### Screenshot 5 — Route-table or Private DNS evidence where applicable

![output ](screenshots/A6Screenshot5.png)

---

# Task 3 — Configure Security and Secret Management

## Goal

Apply least-privilege NSG rules so traffic flows Internet → public entry point → web tier → application tier → database tier, and store credentials in Azure Key Vault or another approved secure mechanism.

### Evidence

#### Screenshot 6 — NSG rules proving least-privilege access between the tiers

![output ](screenshots/A6Screenshot6.png)

---

#### Screenshot 7 — Key Vault or approved secret-management configuration (without displaying secret values)

![output ](screenshots/A6Screenshot7.png)

---

# Task 4 — Deploy the Presentation (Web) Tier

## Goal

Deploy the Book Review App presentation layer on the approved web-tier compute service, configured to route requests to the internal application-tier endpoint, and not directly exposed except through the public entry service.

### Evidence

#### Screenshot 8 — Web-tier compute overview showing subnet and availability configuration

![output ](screenshots/A6Screenshot8.png)

---

#### Screenshot 9 — Terminal or service output proving the presentation layer is running

![output ](screenshots/A6Screenshot9.png)

---

# Task 5 — Deploy the Business (Application) Tier

## Goal

Deploy the Book Review App backend privately in the application subnet, configured to use the private database endpoint and secured environment values, reachable only through its internal endpoint.

### Evidence

#### Screenshot 10 — Application-tier compute overview showing private subnet placement

![output ](screenshots/A6Screenshot10.png)

---

#### Screenshot 11 — Backend process, service, or listening-port evidence

![output ](screenshots/A6Screenshot11.png)

---

#### Screenshot 12 — Internal health-check or API response (without exposing secrets)

![output ](screenshots/A6Screenshot12.png)

---

# Task 6 — Deploy the Managed Database Tier

## Goal

Create a private Azure managed database (public access disabled), with availability/backup/retention settings, the Book Review App schema imported, and access restricted to the application tier only.

### Evidence

#### Screenshot 13 — Database overview showing private connectivity and public access disabled

![output ](screenshots/A6Screenshot13.png)

---

#### Screenshot 14 — Availability, backup, and retention configuration

![output ](screenshots/A6Screenshot14.png)

---

#### Screenshot 15 — Successful schema or connectivity verification (without exposing credentials)

![output ](screenshots/A6Screenshot15.png)

---

# Task 7 — Configure Traffic Management, Availability, and Monitoring

## Goal

Configure the approved public entry service with health probes and backend pools, internal routing for the application tier where required, and enable Azure Monitor/diagnostics/logs/alerts for the key resources.

### Evidence

#### Screenshot 16 — Public entry service showing listener, frontend endpoint, and healthy web targets

![output ](screenshots/A6Screenshot16.png)

---

#### Screenshot 17 — Internal application-tier load-balancing or routing configuration where applicable

![output ](screenshots/A6Screenshot17.png)

---

#### Screenshot 18 — Azure Monitor, diagnostic settings, logs, metrics, or alert evidence

![output ](screenshots/A6Screenshot18.png)

---

# Task 8 — Validate the Production-Style Deployment

## Goal

Confirm the Book Review App works end to end through the public endpoint, with at least one database read and one write, confirm private tiers are not internet-reachable, and complete a safe availability test.

### Evidence

#### Screenshot 19 — Browser showing the Book Review App through the public endpoint

![output ](screenshots/A6Screenshot19.png)

---

#### Screenshot 20 — Proof of successful database-backed read and write operations

![output ](screenshots/A6Screenshot20.png)

---

#### Screenshot 21 — Evidence that private tiers are not publicly accessible

![output ](screenshots/A6Screenshot21.png)

---

#### Screenshot 22 — Availability-test and healthy-target evidence

![output ](screenshots/A6Screenshot2✅.png)

---

#### Public Endpoint

Paste your public endpoint URL here:

`http://20.215.211.166/`

---

### Notes

Summarize what worked, issues encountered and how they were fixed, and the availability/security/secrets/monitoring/backup choices made.

The Book Review App was successfully deployed as a three-tier Azure application. The Web tier runs the Next.js frontend behind Nginx, the Application tier runs the Node.js/Express backend on port 3001, and the Database tier uses Azure Database for MySQL Flexible Server on port 3306. End-to-end database connectivity was verified, with successful reads and writes of book records.

Several issues were encountered during deployment. The frontend initially displayed “No books available” even though the backend was working. Testing showed that Nginx could successfully reach the private App VM IP (10.0.2.4), but the frontend was constructing the API URL incorrectly as `/api/api/books`. This was fixed by correcting the frontend API request and rebuilding the Next.js application. Nginx configuration also required correction after an initial configuration contained invalid Markdown code fences.

For availability, PM2 was used to keep the Next.js frontend running, with Nginx providing the web entry point. Availability was tested by restarting the frontend process with PM2 and verifying that it returned to an online state and that Nginx continued returning HTTP 200 responses. No load balancer or Application Gateway was ultimately deployed, so no load-balancer target-health evidence was used.

For security, separate NSGs and subnets were used for the Web, Application, and Database tiers. Traffic was restricted between tiers to the required ports, with the application communicating with the database over MySQL port 3306 and the Web tier communicating with the Application tier over port 3001. Private DNS was configured for the MySQL Flexible Server. Administrative SSH access was restricted rather than being broadly exposed.

For secrets management, database credentials and connection information were intended to be stored in Azure Key Vault rather than hard-coded in application source code, Terraform files, or container images. Managed identity was selected as the preferred method for allowing Azure resources to access secrets.

For monitoring, Azure Monitor capabilities such as Metrics, Alerts, Diagnostic Settings, and Logs were used as the monitoring options for the deployed Azure resources. For database resilience, Azure Database for MySQL Flexible Server backup and retention settings were used to provide automated backup and recovery capability.

Overall, the deployment successfully demonstrated the main application flow: public Web tier → private Application tier → database, with database-backed reads and writes verified and security, availability, secrets management, monitoring, and backup considered as part of the production-style deployment.


---

# Submission Instructions

- Add all required screenshots and links in your submission
- Do not expose passwords, keys, connection strings, or subscription IDs

---

# Completion Checklist

- [✅] Task 1: Architecture diagram and assumptions documented (Screenshots 1–2)
- [✅] Task 2: Network foundation created with isolated tiers (Screenshots 3–5)
- [✅] Task 3: Least-privilege security and secret management configured (Screenshots 6–7)
- [✅] Task 4: Presentation tier deployed (Screenshots 8–9)
- [✅] Task 5: Application tier deployed privately (Screenshots 10–12)
- [✅] Task 6: Managed database tier deployed privately (Screenshots 13–15)
- [✅] Task 7: Public entry, internal routing, and monitoring configured (Screenshots 16–18)
- [✅] Task 8: End-to-end validation and availability test completed (Screenshots 19–22, Public Endpoint, Notes)
- [✅] No sensitive data exposed

---

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## 📌 Resources

- 🌐 DMI Official Website: https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme  
- 🎓 University: https://university.pravinmishra.com?utm_source=github&utm_medium=readme  
- 💬 Discord Community: https://discord.pravinmishra.com?utm_source=github&utm_medium=readme  
- 📝 Blog: https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme  
- ▶️ YouTube Playlist: https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 Pravin Mishra (LinkedIn): https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 CloudAdvisory (LinkedIn): https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*
