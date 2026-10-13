# Linux Homelab

A hands-on Linux homelab built on VirtualBox to practice systems administration, networking, and troubleshooting skills.

## Overview

I built this homelab to gain practical, hands-on experience with Linux systems administration, networking, and web server management. I configured everything from scratch, intentionally broke configurations to practice troubleshooting, and documented every step.

- **Host:** Windows 11 Laptop
- **Hypervisor:** VirtualBox
- **Guest OS:** Ubuntu Server 22.04 LTS
- **Networking:** NAT + Port Forwarding

## Architecture

![Architecture Diagram](screenshots/architecture-diagram.png)


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

## Step 8: Ansible Automation

I wrote an Ansible playbook to automate NGINX installation and service management.

![Ansible Playbook](screenshots/ansible-playbook.png)

Running the playbook:

![Ansible Output](screenshots/ansible-output.png)

The output shows `ok=3, changed=0` — confirming the desired state was already achieved (idempotent).

## Step 9: Alerting with Alertmanager

I configured Alertmanager to send alerts to Slack when CPU usage exceeds 80%.

![Slack Alerts](screenshots/slack-alerts.png)

![Alertmanager Status](screenshots/alertmanager-status.png)

- Prometheus rule triggers when CPU > 80% for 1 minute
- Alertmanager receives the alert and sends it to a Slack channel via webhook
- Verified with both a real CPU load test and a manual test alert


## Challenges and Solutions

### Challenge 1: Apache/NGINX Port 80 Conflict

**Problem:** NGINX failed to start after installation.

**Error:** `nginx: [emerg] still could not bind()`

**Diagnosis:** Ran `sudo ss -tlnp | grep :80` and found Apache was already listening on port 80.

**Solution:** Ran `sudo systemctl stop apache2`, then `sudo systemctl disable apache2`, then `sudo systemctl start nginx`.

**Lesson:** Only one service can bind to a port at a time.

### Challenge 2: NGINX Config Typo

**Problem:** Intentionally broke the NGINX config to practice troubleshooting.

**Error:** `invalid parameter "default_sever" in /etc/nginx/sites-available/default:22`

**Diagnosis:** Used `sudo nginx -t` to validate the config.

**Solution:** Ran `sudo sed -i 's/default_sever/default_server/' /etc/nginx/sites-available/default`, then `sudo nginx -t`, then `sudo systemctl restart nginx`.

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
- Monitoring (Prometheus, Grafana, Node Exporter)
- Automation (Ansible)
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
| Monitoring | Prometheus, Grafana, Node Exporter |
| Automation | Ansible |
| Remote Access | OpenSSH |
| Host OS | Windows 11 |

## Custom HTML Page

The custom landing page at `/var/www/html/index.html` contains a simple HTML file with a heading "My Linux Homelab is Working!" and a paragraph "Served by NGINX on Ubuntu Server".

## Next Steps

- Add SSL/TLS with Let's Encrypt
- Deploy to AWS EC2
- Set up CI/CD with GitHub Actions

## License

This project is for educational purposes.
