# Koha Installation Documentation
 
## Overview
This document records the process of installing Koha, an open source integrated library system (ILS), on a separate virtual machine from the one hosting WordPress and Omeka. The goal was to install and configure Koha's staff interface and public OPAC, and link the OPAC from the library's WordPress site.
 
---
 
## Environment
- OS: Ubuntu 24.04 LTS
- Web Server: Apache2
- Database: MariaDB
- VM Provider: Google Cloud
- Machine Type: e2-medium (2 vCPU, 4 GB RAM)
- Disk: 20GB
- Koha Version: 25.05.09
---
 
## Step 1: Create a New Virtual Machine
 
Koha requires more RAM and disk space than WordPress or Omeka. A new VM was created in Google Cloud with the following parameters:
 
- Machine type: e2-medium (2 vCPU, 4 GB memory)
- OS: Ubuntu 24.04 LTS
- Disk: 20GB
- HTTP traffic: allowed
- Network tags: `koha-staff-8080`, `koha-opac-8081`
---
 
## Step 2: Configure Google Cloud Firewall
 
Two firewall rules were created to allow traffic on the ports Koha uses for its staff and public interfaces.
 
**Firewall Rule 1 — Staff Interface:**
- Name: `koha-staff-8080`
- Target tag: `koha-staff-8080`
- Source IP range: `0.0.0.0/0`
- Protocol: TCP, Port: `8080`
  
**Firewall Rule 2 — Public OPAC:**
- Name: `koha-opac-8081`
- Target tag: `koha-opac-8081`
- Source IP range: `0.0.0.0/0`
- Protocol: TCP, Port: `8081`
---
 
## Step 3: Prepare the Server
 
Connected to the new VM and started a tmux session to prevent losing progress if disconnected:
 
```bash
tmux
```
 
Updated and cleaned the system:
 
```bash
sudo apt update
sudo apt upgrade
sudo apt autoremove -y && sudo apt clean
```
 
---
 
## Step 4: Add the Koha Repository
 
Installed required packages for secure repository access:
 
```bash
sudo apt install apt-transport-https ca-certificates curl
sudo mkdir -p --mode=0755 /etc/apt/keyrings
sudo curl -fsSL https://debian.koha-community.org/koha/gpg.asc -o /etc/apt/keyrings/koha.asc
```
 
Switched to root and added the Koha repository:
 
```bash
sudo su
```
 
```bash
tee /etc/apt/sources.list.d/koha.sources <<EOF
Types: deb
URIs: https://debian.koha-community.org/koha/
Suites: 25.05
Components: main
Signed-By: /etc/apt/keyrings/koha.asc
EOF
```
 
Verified the file contents:
 
```bash
cat /etc/apt/sources.list.d/koha.sources
```
 
Exited root:
 
```bash
exit
```
 
---
 
## Step 5: Install MariaDB
 
```bash
sudo apt update
sudo apt install mariadb-server
```
 
---
 
## Step 6: Install Koha
 
Synced the new Koha repository and installed Koha:
 
```bash
sudo apt update
sudo apt install koha-common
```
 
This step took several minutes to complete. tmux kept the session running throughout.
 
---
 
## Step 7: Configure Koha Ports
 
Made a backup of the Koha sites config file, then edited it to set the staff and OPAC ports:
 
```bash
sudo cp /etc/koha/koha-sites.conf /etc/koha/koha-sites.conf.backup
sudo nano /etc/koha/koha-sites.conf
```
 
Updated the following lines:
 
```
INTRAPORT="8080"
OPACPORT="8081"
```
 
---
 
## Step 8: Configure Apache
 
Enabled required Apache modules:
 
```bash
sudo a2enmod rewrite cgi headers proxy_http
sudo systemctl restart apache2
```
 
Made a backup of the Apache ports config, then edited it to add the Koha ports:
 
```bash
sudo cp /etc/apache2/ports.conf /etc/apache2/ports.conf.backup
sudo nano /etc/apache2/ports.conf
```
 
Added the following lines below `Listen 80`:
 
```
Listen 8080
Listen 8081
```
 
---
 
## Step 9: Create the Koha Instance
 
```bash
sudo koha-create --create-db bibliolib
sudo systemctl restart apache2
```
 
Completed additional Apache configuration:
 
```bash
sudo a2dissite 000-default
sudo a2enmod deflate
sudo a2ensite bibliolib
sudo systemctl reload apache2
sudo systemctl restart apache2
```
 
---
 
## Step 10: Complete the Web Installer
 
Retrieved the auto-generated login credentials:
 
```bash
sudo koha-passwd bibliolib
```
 
Navigated to the staff interface in a web browser:
 
```
http://[ip-address]:8080
```
 
Completed the web installer, including:
- Added sample libraries and sample patrons
- Created a patron category (`STAFF`) for the administrator account
- Created an item type (`BOOK`)
- Created an administrator account
---
 
## Step 11: Set OPACBaseURL
 
In the Koha staff interface:
 
1. Click **More → Administration → System Preferences**
2. Click **OPAC** in the left sidebar
3. Set **OPACBaseURL** to `http://[ip-address]:8081`
4. Click **Save all OPAC Preferences**
Verified the public OPAC loaded at:
 
```
http://[ip-address]:8081
```
 
---
 
## Step 12: Link Koha OPAC from WordPress
 
Logged into the WordPress admin panel on the original VM and added a link to the Koha OPAC in the library homepage post.
 
---
 
## Issues Encountered
 
No significant errors were encountered during this installation. The process went smoothly from start to finish. All links and interfaces loaded correctly on the first attempt.
 
### Note: Network Tags
The VM was initially created without the required network tags (`koha-staff-8080` and `koha-opac-8081`). These were added after the fact via **Compute Engine → VM Instances → Edit** before the firewall rules were created. The tags on the VM and the firewall rules must match exactly for traffic to route correctly.
 
---
