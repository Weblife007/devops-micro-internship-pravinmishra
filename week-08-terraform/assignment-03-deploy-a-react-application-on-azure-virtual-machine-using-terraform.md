# Assignment 3 — Deploy a React Application on Azure Virtual Machine Using Terraform

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will use Terraform to provision an Azure resource group, network, and Ubuntu 20.04 VM, then deploy the `my-react-app` React application onto the VM over SSH and serve it through Nginx.

---

# Task 1 — Create a New Terraform Project

## Goal

Create a `terraform-react-azure` project directory for the Azure Terraform configuration.

### Evidence

#### Screenshot 1 — File Explorer, VS Code, or terminal showing the `terraform-react-azure` project directory

![output ](screenshots/A3Screenshot1.png)

---

# Task 2 — Write main.tf to Provision the Azure Infrastructure

## Goal

Define the resource group, virtual network/subnet, Network Security Group (SSH 22, HTTP 80), public IP, network interface, and Ubuntu 20.04 Standard B1s VM in `main.tf`.

### Evidence

#### Screenshot 2 — VS Code showing `main.tf` with the required Azure resources, with any password or sensitive values hidden

![output ](screenshots/A3Screenshot2.png)

---

# Task 3 — Initialize Terraform

## Goal

Run `terraform init` and confirm the working directory initializes successfully.

### Evidence

#### Screenshot 3 — Terminal showing successful `terraform init` output

![output ](screenshots/A3Screenshot3.png)

---

# Task 4 — Plan and Apply the Configuration

## Goal

Review `terraform plan`, run `terraform apply`, and record the VM's public IP.

### Evidence

#### Screenshot 4 — Terraform apply output showing successful completion

![output ](screenshots/A3Screenshot4.png)

---

#### Screenshot 5 — Azure portal showing the Virtual Machine running and its public IP

![output ](screenshots/A3Screenshot5.png)

---

# Task 5 — Connect to the Virtual Machine

## Goal

Establish an SSH session with the Ubuntu VM through its public IP.

### Evidence

#### Screenshot 6 — Terminal showing a successful SSH connection to the Azure VM

![output ](screenshots/A3Screenshot6.png)

---

# Task 6 — Install Node.js, npm, and Git

## Goal

Update Ubuntu and install Node.js, npm, and Git.

### Evidence

#### Screenshot 7 — Terminal showing successful installation and the `node -v` and `npm -v` output

![output ](screenshots/A3Screenshot7.png)

---

# Task 7 — Clone, Build, and Serve the React App with Nginx

## Goal

Follow the `my-react-app` repository README to clone, install, and build the app, then serve the production build through Nginx.

### Evidence

#### Screenshot 8 — Terminal showing the successful React build

![output ](screenshots/A3Screenshot8.png)

---

#### Screenshot 9 — Terminal showing that Nginx is active and running

![output ](screenshots/A3Screenshot9.png)

---

# Task 8 — Test the Deployment

## Goal

Confirm the React application loads through the VM's public IP and navigation works.

### Evidence

#### Screenshot 10 — Browser showing the React application with the Azure VM public IP visible in the address bar

![output ](screenshots/A3Screenshot✅.png)

---

### Notes

Write a short summary of what you built and any issues you encountered and how you resolved them.

### Notes

In this assignment, I used Terraform to provision an Azure Virtual Machine and deploy a React application that was served through Nginx. The main issue I encountered was an error in the Cloud-Init configuration after the VM was provisioned. I investigated the Cloud-Init error and traced it back to a syntax error in the deployment script. After correcting the syntax error and applying the configuration again, the Cloud-Init script executed successfully. I was then able to connect to the VM through SSH, install Node.js, npm, and Git, build the React application, configure Nginx, and verify that the application was accessible through the VM's public IP. This assignment helped me understand how Terraform and Cloud-Init can be used together to automate VM provisioning and application deployment, as well as how to troubleshoot initialization scripts when automated deployments fail.


---

# Submission Instructions

- Add all required screenshots in your submission
- Include the Azure VM public IP
- Do not expose Azure credentials, passwords, or private keys

---

# Completion Checklist

- [✅] Task 1: `terraform-react-azure` project created (Screenshot 1)
- [✅] Task 2: `main.tf` defines all required Azure resources (Screenshot 2)
- [✅] Task 3: `terraform init` completed successfully (Screenshot 3)
- [✅] Task 4: Plan applied and VM running with public IP (Screenshots 4–5)
- [✅] Task 5: SSH connection verified (Screenshot 6)
- [✅] Task 6: Node.js, npm, and Git installed (Screenshot 7)
- [✅] Task 7: React app built and served through Nginx (Screenshots 8–9)
- [✅] Task 8: App verified through the VM public IP (Screenshot 10)
- [✅] Summary paragraph written (Notes)
- [✅] No sensitive information exposed

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
