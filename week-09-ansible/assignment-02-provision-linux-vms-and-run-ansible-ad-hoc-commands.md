# Assignment 02 — Provision Linux VMs with Terraform and Run Ansible Ad-Hoc Commands

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

In this assignment, you will use Terraform to provision three or four Ubuntu Linux Virtual Machines on either Microsoft Azure or Amazon Web Services.

You will configure SSH key-based authentication, organize the servers using a custom Ansible inventory, and run Ansible ad-hoc commands across individual hosts and inventory groups.

---

# Task 1 — Create the Multi-Host Lab Structure

## Goal

Create a separate project directory for the multi-host lab and prepare the Terraform, Ansible, and documentation files.

This project will use the Git repository and Ansible controller prepared in Assignment 01.

### Evidence

#### Screenshot 1 — Terminal showing the complete `ansible-adhoc-lab` project structure

![output ](screenshots/A2Screenshot1.png)

---

#### Screenshot 2 — Terminal showing `git status --short` with the new project files and updated `.gitignore`

![output ](screenshots/A2Screenshot1.png)

---

### Notes

The project files were created and the .gitignore file was updated to exclude unnecessary and sensitive files. git status --short was used to verify the new files and confirm that the expected changes were detected by Git before committing them.

---

# Task 2 — Create the Terraform Configuration

## Goal

Create the Terraform configuration required to provision three or four Ubuntu Linux VMs on your selected cloud platform.

Complete only one option:

- Option A — Microsoft Azure
- Option B — Amazon Web Services

Do not configure both providers for this assignment.

### Evidence

#### Screenshot 3 — Terraform configuration showing the three or four server roles and the `for_each` or `count` implementation

![output ](screenshots/A2Screenshot3.png)

---

#### Screenshot 4 — Terraform configuration showing SSH restricted to the controller IP and HTTP allowed only for web hosts

![output ](screenshots/A2Screenshot4.png)

---

#### Screenshot 5 — Terraform output configuration showing how public IP addresses are associated with the server roles

![output ](screenshots/A2Screenshot5.png)

---

### Notes

![output ](screenshots/A2Screenshot6.png)

---

# Task 3 — Provision the Infrastructure with Terraform

## Goal

Initialize and validate the Terraform configuration, review the execution plan, provision the selected three or four VMs, and retrieve their public IP addresses.

### Evidence

#### Screenshot 6 — Final `terraform apply` output showing `Apply complete`

![output ](screenshots/A2Screenshot7.png)

---

#### Screenshot 7 — `terraform output public_ips` showing the role-to-IP mapping for all three or four VMs

![output ](screenshots/A2Screenshot7.png)

---

#### Screenshot 8 — Azure Portal or AWS Management Console showing all three or four VMs in the `Running` state, with their role-based names visible

![output ](screenshots/A2Screenshot2.png)

---

### Notes

The Azure/AWS VMs were successfully deployed and verified in the cloud management console. All managed VMs were in the Running state, and their role-based names were visible, confirming that the infrastructure was ready for Ansible management.

---

# Task 4 — Verify SSH Key-Based Access

## Goal

Verify that each managed VM can be accessed from the Ansible controller using SSH key-based authentication.

### Evidence

#### Screenshot 9 — Terminal showing successful SSH hostname output from all VMs

![output ](screenshots/A2Screenshot3.png)

---

### Notes

SSH key-based access was successfully verified from the Ansible controller to all managed VMs. The hostname output from each VM confirmed that the SSH connections were successful without requiring password authentication. This also confirmed that the correct SSH keys, usernames, and network access were configured.

---

# Task 5 — Create the Custom Ansible Inventory

## Goal

Create an Ansible inventory file that groups the managed VMs by role.

The inventory allows Ansible to run commands against all servers, or only specific groups such as `web`, `app`, or `db`.

### Evidence

#### Screenshot 10 — `inventory.ini` showing the `web`, `app`, and `db` groups

![output ](screenshots/A2Screenshot8.png)

---

#### Screenshot 11 — Output of `ansible-inventory -i inventory.ini --graph`

![output ](screenshots/A2Screenshot9.png)

---

### Notes

The Ansible inventory was successfully verified using the ansible-inventory --graph command. The output confirmed that the managed servers were correctly organized into the web, app, and db groups. This grouping makes it possible to target specific server roles when running Ansible commands.

---

# Task 6 — Run Ansible Ad-Hoc Commands

## Goal

Run Ansible ad-hoc commands from the controller to verify connectivity, check server information, and manage packages and services across inventory groups.

This task proves that the inventory is working and that Ansible can control multiple managed VMs without writing a playbook.

### Evidence

#### Screenshot 12 — Output of `ansible all -i inventory.ini -m ping`

![output ](screenshots/A2Screenshot7.png)

---

#### Screenshot 13 — Output of `ansible all -i inventory.ini -m command -a "uptime"`

![output ](screenshots/A2Screenshot6.png)

---

#### Screenshot 14 — Output of `ansible web -i inventory.ini -m apt -a "name=nginx state=present update_cache=yes" --become`

![output ](screenshots/A2Screenshot7.png)
---

#### Screenshot 15 — Output of `ansible web -i inventory.ini -m service -a "name=nginx state=started enabled=yes" --become`

![output ](screenshots/A2Screenshot8.png)

---

#### Screenshot 16 — Output of `ansible all -i inventory.ini -m apt -a "name=htop state=present update_cache=yes" --become`

![output ](screenshots/A2Screenshot9.png)

---

#### Screenshot 17 — Output of `ansible web -i inventory.ini -m command -a "systemctl is-active nginx"`

![output ](screenshots/A2Screenshot7.png)

---

### Notes

The final verification confirmed that Nginx was active on the web servers. I used the Ansible command module to run systemctl is-active nginx against the web group. The successful active response confirmed that Nginx was running correctly and that Ansible could execute commands on the web hosts through the configured inventory.

---

# LinkedIn Post Required

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

`https://lnkd.in/p/emgpK32Z`

---

#### Screenshot — Published LinkedIn post

![output ](screenshots/A4LinkedInPost.png)

---

# Assignment Questions

Answer the following in your own words:

**1. What is the purpose of an Ansible inventory file?**

An Ansible inventory file tells Ansible which servers it needs to manage. It contains the hostnames or IP addresses of the servers and can organize them into groups. This allows me to run commands against individual servers or groups of servers such as web, app, and database servers.

---

**2. What is the difference between the `web`, `app`, and `db` groups in your inventory?**

The web group contains the servers responsible for handling web traffic, such as running Nginx. The app group contains the application servers where the application logic runs. The db group contains the database servers responsible for storing application data. Separating the servers into groups allows Ansible commands to target only the servers that need a particular task.

---

**3. What does the Ansible `ping` module verify?**

The Ansible ping module verifies that Ansible can connect to the managed servers and successfully communicate with them. A successful pong response confirms that the host is reachable through Ansible and that the required Python environment for the module is available.

---

**4. Why do package installation commands require `--become`?**

Package installation normally requires administrator privileges because it modifies system packages and files. The --become option allows Ansible to execute the command with elevated privileges, usually through sudo, so that packages such as Nginx and htop can be installed successfully.

---

**5. When would you use an ad-hoc command instead of a playbook?**

I would use an ad-hoc command for a quick, one-time task or when I need to check something immediately on one or more servers. For example, I can use an ad-hoc command to check uptime, test connectivity, install a package, or check whether a service is running. For repeatable, multi-step, or more complex configuration tasks, I would use a playbook.

---

**6. What is one challenge you faced while setting up SSH or inventory, and how did you fix it?**

One challenge I faced was making sure the correct SSH key was being used when connecting to the servers. I verified the SSH key configuration and loaded the correct ED25519 key into the SSH agent. I then tested SSH connectivity to confirm that the servers could be accessed before adding them to the Ansible inventory. This helped ensure that Ansible could connect to the correct hosts using key-based authentication.

---

# Required Files

Confirm that the following files are included in your assignment workspace:

- [ ] `ansible-adhoc-lab/README.md`
- [ ] `ansible-adhoc-lab/terraform/providers.tf`
- [ ] `ansible-adhoc-lab/terraform/main.tf`
- [ ] `ansible-adhoc-lab/terraform/variables.tf`
- [ ] `ansible-adhoc-lab/terraform/outputs.tf`
- [ ] `ansible-adhoc-lab/ansible/inventory.ini`
- [ ] Updated `.gitignore`

---

# Submission Instructions

- Add all required screenshots from the tasks.
- Full Name must be visible in required screenshots.
- Mention whether you used Azure or AWS.
- Mention whether you used the three-VM option or four-VM option.
- Add the public IP addresses of the VMs, redacted if preferred.
- Add your `inventory.ini` proof.
- Add a short explanation of what you learned.
- Answer all assignment questions clearly in your own words.
- Add your LinkedIn post URL.
- Do not expose SSH private keys, Terraform state files, cloud credentials, passwords, access keys, secret keys, account IDs, or subscription IDs.

---

# Completion Checklist

- [ ] Task 1: `ansible-adhoc-lab` project structure created
- [ ] Task 1: `.gitignore` updated for Terraform files
- [ ] Task 2: Terraform configuration created
- [ ] Task 2: Server roles defined for either three or four VMs
- [ ] Task 2: `count` or `for_each` used
- [ ] Task 2: SSH restricted to the controller public IP
- [ ] Task 2: HTTP allowed only for web hosts
- [ ] Task 2: Terraform output maps roles to public IPs
- [ ] Task 3: Terraform initialized successfully
- [ ] Task 3: Terraform configuration validated
- [ ] Task 3: Terraform apply completed successfully
- [ ] Task 3: All selected VMs are running
- [ ] Task 4: SSH key-based access works for every VM
- [ ] Task 5: `inventory.ini` contains `web`, `app`, and `db` groups
- [ ] Task 5: `ansible-inventory -i inventory.ini --graph` shows the correct groups
- [ ] Task 6: `ansible all -i inventory.ini -m ping` returns `SUCCESS`
- [ ] Task 6: Ad-hoc commands run successfully
- [ ] Task 6: `--become` was used for package and service tasks
- [ ] Task 6: Nginx is active on the `web` group
- [ ] Screenshots 1–17 are included
- [ ] Assignment questions are answered
- [ ] LinkedIn post published
- [ ] LinkedIn post URL added
- [ ] No sensitive information is exposed

---

## About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra and The CloudAdvisory, focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## Resources

- DMI Official Website: [https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme](https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme)
- University: [https://university.pravinmishra.com?utm_source=github&utm_medium=readme](https://university.pravinmishra.com?utm_source=github&utm_medium=readme)
- Discord Community: [https://discord.pravinmishra.com?utm_source=github&utm_medium=readme](https://discord.pravinmishra.com?utm_source=github&utm_medium=readme)
- Blog: [https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme](https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme)
- YouTube Playlist: [https://www.youtube.com/playlist?list=PLFeSNDtI4Cho](https://www.youtube.com/playlist?list=PLFeSNDtI4Cho)
- Pravin Mishra LinkedIn: [https://www.linkedin.com/in/pravin-mishra-aws-trainer/](https://www.linkedin.com/in/pravin-mishra-aws-trainer/)
- CloudAdvisory LinkedIn: [https://www.linkedin.com/company/thecloudadvisory/](https://www.linkedin.com/company/thecloudadvisory/)

---

*This submission is part of DevOps Micro Internship (DMI) — Agentic AI Track.*