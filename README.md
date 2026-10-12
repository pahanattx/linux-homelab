# Linux Homelab

A hands-on Linux homelab built on VirtualBox to practice systems administration, networking, and troubleshooting skills.

## Overview

I built this homelab to gain practical, hands-on experience with Linux systems administration, networking, and web server management. I configured everything from scratch, intentionally broke configurations to practice troubleshooting, and documented every step.

- **Host:** Windows 11 Laptop
- **Hypervisor:** VirtualBox
- **Guest OS:** Ubuntu Server 22.04 LTS
- **Networking:** NAT + Port Forwarding

---

## Step 1: Virtual Machine Setup

I installed VirtualBox on Windows and created a new Ubuntu Server VM with:
- 2GB RAM
- 2 CPU cores
- 20GB disk
- NAT network adapter

Then I installed Ubuntu Server 22.04 LTS from the official ISO.

---

## Step 2: SSH Server Setup

After Ubuntu was installed, I installed and enabled the OpenSSH server so I could connect remotely.

```bash
sudo apt update
sudo apt install openssh-server -y
sudo systemctl enable ssh
sudo systemctl start ssh
Verification
https://screenshots/systemctl-status-ssh.png

The output shows Active: active (running), confirming the SSH server is running.

Step 3: NAT Port Forwarding
Since the VM is on a NAT network, its IP (10.0.2.x) is not directly reachable from Windows. I configured port forwarding in VirtualBox:

Host Port 2222 → Guest Port 22 (SSH)

Host Port 8080 → Guest Port 80 (HTTP)

Then I connected from Windows PowerShell:

powershell
ssh ajax@localhost -p 2222
Verification
https://screenshots/ssh-login.png

The screenshot shows a successful SSH login to the Ubuntu VM from Windows.

Network Configuration
https://screenshots/ip-a-output.png

Step 4: NGINX Web Server
I installed NGINX as the web server.

bash
sudo apt install nginx -y
sudo systemctl start nginx
However, NGINX failed to start because Apache was already using port 80. I resolved this conflict (see Challenge 1 below), then verified NGINX was listening.

bash
sudo ss -tlnp | grep :80
Verification
https://screenshots/port-80-listening.png

NGINX is listening on 0.0.0.0:80 and [::]:80.

https://screenshots/nginx-status.png

The service is active (running) with a master process and worker processes.

Step 5: Custom HTML Page
I replaced the default NGINX page with a custom HTML landing page at /var/www/html/index.html.

Verification
https://screenshots/custom-page-proof.png

Accessed via browser at http://localhost:8080:

https://screenshots/browser-page.png

Step 6: UFW Firewall
I enabled the UFW firewall with a default-deny incoming policy, allowing only SSH and HTTP.

Important: I allowed SSH (port 22) before enabling UFW to avoid locking myself out.

bash
sudo ufw allow 22
sudo ufw allow 80
sudo ufw enable
sudo ufw status verbose
Verification
https://screenshots/ufw-status.png

The output shows:

Default: deny (incoming), allow (outgoing)

Port 22 ALLOW IN

Port 80 ALLOW IN

Step 7: Security Verification
I checked which services were exposed externally:

bash
sudo ss -tlnp
Findings:

Port 22 (SSH) — exposed externally (intended)

Port 80 (NGINX) — exposed externally (intended)

Port 3306 (MySQL) — bound to 127.0.0.1 only (safe)

Port 631 (CUPS) — bound to 127.0.0.1 only (safe)

Port 53 (DNS) — bound to 127.0.0.53 only (safe)

I also checked login history:

bash
last
Only my own sessions and reboots were shown — no unauthorized access.

Challenges and Solutions
Challenge 1: Apache/NGINX Port 80 Conflict
Problem: NGINX failed to start after installation.

Error: nginx: [emerg] still could not bind()

Diagnosis: Ran sudo ss -tlnp | grep :80 and found Apache was already listening on port 80.

Solution:

bash
sudo systemctl stop apache2
sudo systemctl disable apache2
sudo systemctl start nginx
Lesson: Only one service can bind to a port at a time. Always check with ss -tlnp first.

Challenge 2: NGINX Config Error
Problem: Intentionally broke the NGINX config to practice troubleshooting.

Error: invalid parameter "default_sever" in /etc/nginx/sites-enabled/default:22

Diagnosis: Used sudo nginx -t to validate the config, which pointed to line 22.

Solution:

bash
sudo sed -i 's/default_sever/default_server/' /etc/nginx/sites-enabled/default
sudo nginx -t
sudo systemctl restart nginx
Lesson: Always test config with nginx -t before restarting the service. This catches errors before they cause downtime.

Challenge 3: SSH Connection Timeout
Problem: Could not SSH from Windows to the Ubuntu VM.

Error: ssh: connect to host localhost port 22: Connection timed out

Root Cause: VM on NAT network — the IP was not reachable from the host without port forwarding.

Solution: Added a VirtualBox port forwarding rule: Host 2222 → Guest 22.

Lesson: NAT isolates VMs from the host. Port forwarding is required to expose services — conceptually similar to NAT gateways and load balancers in cloud environments.

Skills Demonstrated
Linux CLI — navigation, permissions, process management

Networking — NAT, Port Forwarding, IP addressing, ports

Service Management — systemd, systemctl

Web Server — NGINX installation, configuration, troubleshooting

Firewall — UFW default-deny policy, selective port exposure

Troubleshooting — log analysis, config validation, port conflict resolution

Remote Management — SSH from Windows to Linux VM

Documentation — step-by-step guides, screenshots

Tools Used
Category	Tools
Virtualization	VirtualBox
Operating System	Ubuntu Server 22.04 LTS
Web Server	NGINX
Firewall	UFW
Remote Access	OpenSSH
Host OS	Windows 11
License
This project is for educational purposes.
