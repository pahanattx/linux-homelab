# Linux Homelab

A hands-on Linux homelab built on VirtualBox to practice systems administration, networking, and troubleshooting skills.

## Overview

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

## Challenges and Solutions

### Challenge 1: Apache/NGINX Port 80 Conflict

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
