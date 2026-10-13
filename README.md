# Linux Homelab

A hands-on Linux homelab built on VirtualBox to practice systems administration, networking, and troubleshooting skills.

## Overview

<<<<<<< HEAD
I built this homelab to gain practical, hands-on experience with Linux systems administration, networking, and web server management. Instead of just reading tutorials, I configured everything from scratch, intentionally broke configurations to practice troubleshooting, and documented every step.

- **Host:** Windows 11 Laptop
- **Hypervisor:** VirtualBox
- **Guest OS:** Ubuntu Server 22.04 LTS
- **Networking:** NAT + Port Forwarding

## Architecture

- Windows 11 (Host)
  - VirtualBox
    - Ubuntu Server VM
      - SSH (Port 22)
      - NGINX (Port 80)
      - UFW Firewall
- Port Forwarding: Host 2222 to Guest 22 (SSH), Host 8080 to Guest 80 (HTTP)

## What I Built

### 1. Virtual Machine Setup
- Installed VirtualBox and created an Ubuntu Server VM
- Configured 2GB RAM, 20GB disk, NAT network

### 2. SSH Server
- Installed and enabled OpenSSH server
- Connected from Windows PowerShell via port forwarding

### 3. NAT Port Forwarding
- Created rules: Host 2222 to Guest 22 (SSH)
- Created rules: Host 8080 to Guest 80 (HTTP)

### 4. NGINX Web Server
- Installed NGINX and resolved a Port 80 conflict with Apache
- Deployed a custom HTML landing page
- Verified functionality via curl and browser

### 5. UFW Firewall
- Enabled UFW with default-deny incoming policy
- Allowed only Port 22 (SSH) and Port 80 (HTTP)
- Verified rules with `ufw status verbose`

### 6. Security Verification
- Confirmed MySQL, CUPS, and DNS services are bound to localhost only (127.0.0.1)
- Checked login history and failed attempts

## Screenshots

**NGINX Service Running**

![NGINX Status](screenshots/nginx-status.png)

**Port 80 Listening**

![Port 80](screenshots/port-80-listening.png)

**UFW Firewall Rules**

![UFW Status](screenshots/ufw-status.png)

**SSH Service Running**

![SSH Status](screenshots/systemctl-status-ssh.png)

**Custom HTML Page**

![Custom Page](screenshots/custom-page-proof.png)

**Network Configuration**

![IP Config](screenshots/ip-a-output.png)

**SSH Login from Windows**

![SSH Login](screenshots/ssh-login.png)

**Browser Verification**

![Browser](screenshots/browser-page.png)

=======
I built this homelab to gain practical, hands-on experience with Linux systems administration, networking, and web server management. I configured everything from scratch, intentionally broke configurations to practice troubleshooting, and documented every step.

- Host: Windows 11 Laptop
- Hypervisor: VirtualBox
- Guest OS: Ubuntu Server 22.04 LTS
- Networking: NAT + Port Forwarding

## Step 1: Virtual Machine Setup

I installed VirtualBox on Windows and created a new Ubuntu Server VM with 2GB RAM, 2 CPU cores, 20GB disk, and a NAT network adapter.

## Step 2: SSH Server Setup

I installed OpenSSH server with `sudo apt install openssh-server -y`, then enabled and started the service.

![SSH Status](screenshots/systemctl-status-ssh.png)

## Step 3: NAT Port Forwarding

The VM is on a NAT network, so its IP is not reachable from Windows. I configured port forwarding in VirtualBox:

- Host 2222 to Guest 22 (SSH)
- Host 8080 to Guest 80 (HTTP)

Then connected from Windows PowerShell with `ssh ajax@localhost -p 2222`.

![SSH Login](screenshots/ssh-login.png)

![IP Config](screenshots/ip-a-output.png)

## Step 4: NGINX Web Server

I installed NGINX with `sudo apt install nginx -y`. It failed to start because Apache was already on port 80 (see Challenge 1). After resolving, I verified with `sudo ss -tlnp | grep :80`.

![Port 80](screenshots/port-80-listening.png)

![NGINX Status](screenshots/nginx-status.png)

## Step 5: Custom HTML Page

I replaced the default NGINX page at `/var/www/html/index.html` with a custom HTML landing page.

![Custom Page](screenshots/custom-page-proof.png)

Accessed at `http://localhost:8080`:

![Browser](screenshots/browser-page.png)

## Step 6: UFW Firewall

I allowed SSH and HTTP, then enabled UFW with default-deny incoming policy:

`sudo ufw allow 22`
`sudo ufw allow 80`
`sudo ufw enable`
`sudo ufw status verbose`

![UFW Status](screenshots/ufw-status.png)

## Step 7: Monitoring Stack

I set up a monitoring stack to track server health in real-time.

- **Node Exporter** exposes system metrics (CPU, RAM, Disk) on port 9100
- **Prometheus** scrapes and stores those metrics on port 9090
- **Grafana** visualizes the data in dashboards on port 3000

![Grafana Dashboard](screenshots/grafana-dashboard.png)

---

## Step 8: Ansible Automation

I wrote an Ansible playbook to automate NGINX installation and service management.

![Ansible Playbook](screenshots/ansible-playbook.png)

Running the playbook:

![Ansible Output](screenshots/ansible-output.png)

The output shows `ok=3, changed=0` — confirming the desired state was already achieved (idempotent).

>>>>>>> 9e2225b5127d00a934aa73f601b611db78635d44
## Challenges and Solutions

### Challenge 1: Apache/NGINX Port 80 Conflict

<<<<<<< HEAD
**Problem:** NGINX failed to start after installation.

**Error:** `nginx: [emerg] still could not bind()`

**Diagnosis:** Ran `sudo ss -tlnp | grep :80` and found Apache was already listening on port 80.

**Solution:** Ran `sudo systemctl stop apache2`, then `sudo systemctl disable apache2`, then `sudo systemctl start nginx`.

**Lesson:** Only one service can bind to a port at a time.

### Challenge 2: NGINX Config Typo

**Problem:** Intentionally broke the NGINX config to practice troubleshooting.

**Error:** `invalid parameter "default_sever" in /etc/nginx/sites-enabled/default:22`

**Diagnosis:** Used `sudo nginx -t` to validate the config.

Ran `sudo sed -i 's/default_sever/default_server/' /etc/nginx/sites-available/default`, then `sudo nginx -t`, then `sudo systemctl restart nginx`.

**Lesson:** Always test config with `nginx -t` before restarting.

### Challenge 3: SSH Connection Timeout

**Problem:** Could not SSH from Windows to Ubuntu VM.

**Error:** `ssh: connect to host localhost port 22: Connection timed out`

**Root Cause:** VM on NAT network - IP not reachable from host without port forwarding.

**Solution:** Added VirtualBox port forwarding rule: Host 2222 to Guest 22.

**Lesson:** NAT isolates VMs from hosts. Port forwarding exposes services - similar to NAT gateways in cloud.

## Skills Demonstrated

- Linux CLI (navigation, permissions, process management)
- Networking (NAT, Port Forwarding, IP addressing)
- Service Management (systemd, systemctl)
- Web Server Administration (NGINX)
- Firewall Configuration (UFW)
- Troubleshooting (log analysis, config validation)
- Remote Server Management (SSH)
- Documentation and Knowledge Sharing

## Tools Used

| Category | Tools |
|----------|-------|
| Virtualization | VirtualBox |
| Operating System | Ubuntu Server 22.04 LTS |
| Web Server | NGINX |
| Firewall | UFW |
| Remote Access | OpenSSH |
| Host OS | Windows 11 |

## Custom HTML Page

The custom landing page at `/var/www/html/index.html` contains a simple HTML file with a heading "My Linux Homelab is Working!" and a paragraph "Served by NGINX on Ubuntu Server".

## Next Steps

- Automate setup with Ansible
- Add SSL/TLS with Let's Encrypt
- Deploy to AWS EC2
- Set up CI/CD with GitHub Actions
- Add monitoring with Prometheus/Grafana

## License

This project is for educational purposes.
=======
**Error:** `nginx: [emerg] still could not bind()`

**Diagnosis:** Ran `sudo ss -tlnp | grep :80` and found Apache already listening on port 80.

**Solution:** Stopped and disabled Apache, then started NGINX.

**Lesson:** Only one service can bind to a port at a time.

### Challenge 2: NGINX Config Error

**Error:** `invalid parameter "default_sever" in /etc/nginx/sites-enabled/default:22`

**Diagnosis:** Used `sudo nginx -t` to validate, which pointed to line 22.

**Solution:** Fixed the typo with `sudo sed -i 's/default_sever/default_server/' /etc/nginx/sites-enabled/default`, then restarted NGINX.

**Lesson:** Always run `nginx -t` before restarting the service.

### Challenge 3: SSH Connection Timeout

**Error:** `ssh: connect to host localhost port 22: Connection timed out`

**Root Cause:** VM on NAT network without port forwarding.

**Solution:** Added VirtualBox port forwarding rule: Host 2222 to Guest 22.

**Lesson:** NAT isolates VMs from hosts. Port forwarding exposes services.

## Skills Demonstrated

- Linux CLI
- Networking (NAT, Port Forwarding)
- Service Management (systemd)
- Web Server (NGINX)
- Firewall (UFW)
- Troubleshooting
- SSH Remote Management

## Tools Used

- VirtualBox
- Ubuntu Server 22.04 LTS
- NGINX
- UFW
- OpenSSH
- Windows 11

## License

This project is for educational purposes.
>>>>>>> 9e2225b5127d00a934aa73f601b611db78635d44
