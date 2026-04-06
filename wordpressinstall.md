# WordPress Installation Documentation

## Overview

This document records the process of installing WordPress on a LAMP stack (Linux, Apache, MySQL, PHP). 
The goal was to install WordPress and customize the homepage to prepare for creating a model library website.

---

## Environment

- OS: Ubuntu 24.04.4 LTS  
- Web Server: Apache2  
- PHP Version: 8.3.6  
- Database: MySQL 8.0.45  
- VM Provider: Google Cloud  

---

## Step 1: Verify Requirements

Checked installed versions:

```bash
php --version
mysql --version
cat /etc/issue.net
```

Results:
- PHP 8.3.6 (meets requirement)  
- MySQL 8.0.45 (meets requirement)  
- Ubuntu 24.04.4 LTS  

Installed required PHP modules:

```bash
sudo apt install php-curl php-xml php-imagick php-mbstring php-zip php-intl
```

Restarted services:

```bash
sudo systemctl restart apache2
sudo systemctl restart mysql
```

---

## Step 2: Download and Extract WordPress

```bash
cd /var/www/html
sudo wget https://wordpress.org/latest.zip
```

Attempted extraction:

```bash
sudo unzip latest.zip
```

### Issue Encountered

- Error: `unzip` command not found  

### Solution

```bash
sudo apt install unzip
sudo unzip latest.zip
```

Result:
- WordPress successfully extracted  
- Directory created at `/var/www/html/wordpress`

---

## Step 3: Create Database and User

```bash
sudo mysql -u root
```

```sql
create user 'wordpress'@'localhost' identified by '********';
create database wordpress;
grant all privileges on wordpress.* to 'wordpress'@'localhost';
show databases;
\q
```

### Issue Encountered

- VM unexpectedly disconnected/restarted after completing this step  

### Verification

```bash
sudo mysql -u root
```

```sql
show databases;
```

Result:
- Confirmed `wordpress` database existed  
- No data was lost  

---

## Step 4: Configure WordPress

```bash
cd /var/www/html/wordpress
sudo cp wp-config-sample.php wp-config.php
sudo nano wp-config.php
```

Updated:
- DB_NAME
- DB_USER
- DB_PASSWORD

No issues encountered.

---

## Step 5: Access WordPress

Accessed site via:

```
http://34.28.34.108/wordpress/
```

Confirmed WordPress setup page loaded successfully.

---

## Step 6: Complete Installation

- Completed installation through browser  
- Created WordPress admin account  
- Customized homepage using built-in tools  

Result:
- WordPress site fully functional  

---

## Issues Encountered

### Issue 1: Missing unzip Utility

- Problem: Could not extract WordPress files  
- Cause: `unzip` not installed  
- Solution: Installed `unzip` using apt  

---

### Issue 2: VM Disconnection

- Problem: VM disconnected after database setup  
- Solution: Reconnected and verified database persisted  

---

## Reflection

I learned that installing WordPress is similar to setting up the OPAC and cataloging module, especially in how the database and configuration files 
are structured. The process felt more complex because WordPress has more dependencies and files, but the overall logic was familiar. 
I also saw how important it is to verify system requirements before installing software. Troubleshooting small issues, like missing packages or 
reconnecting after a VM interruption, reinforced the need to check and confirm each step. 
This assignment showed how a CMS like WordPress can serve as the front end of a library system while connecting to other services behind the scenes.
