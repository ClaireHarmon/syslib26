# Omeka Classic Installation Documentation

## Overview
This document records the process of installing Omeka Classic on a LAMP stack (Linux, Apache, MySQL, PHP).
The goal was to install Omeka Classic as part of a library website project, applying the same logic used in prior installations (bare bones ILS and WordPress).

---

## Environment
- OS: Ubuntu 24.04 LTS
- Web Server: Apache2
- PHP Version: 8.3.6
- Database: MySQL 8.0.45
- VM Provider: Google Cloud

---

## Step 1: Update System and Verify Requirements

Made sure the system was fully updated before beginning:

```bash
sudo apt update
sudo apt upgrade
sudo apt clean
```

Checked installed versions of PHP and MySQL to confirm they met Omeka's system requirements:

```bash
php --version
mysql --version
```

Results:
- PHP 8.3.6 (meets requirement)
- MySQL 8.0.45 (meets requirement)

---

## Step 2: Install Prerequisites

Installed ImageMagick for image/thumbnail processing:

```bash
sudo apt install imagemagick
```

Checked that required PHP extensions were installed and enabled, particularly `mysqli` and `exif`.

Enabled Apache mod_rewrite for Omeka's URL rewriting:

```bash
sudo a2enmod rewrite
sudo systemctl restart apache2
```

---

## Step 3: Download and Extract Omeka Classic

Downloaded the latest Omeka Classic release from the official site:

```bash
cd /var/www/html
sudo wget https://github.com/omeka/Omeka/releases/download/v3.2/omeka-3.2.zip
sudo unzip omeka-3.2.zip
```

Renamed the extracted directory to remove the version number:

```bash
sudo mv omeka-3.2 omeka
```

Result:
- Omeka Classic extracted and available at `/var/www/html/omeka`

---

## Step 4: Create Database and User

Logged into MySQL and created a dedicated database and user for Omeka:

```bash
sudo mysql -u root
```

```sql
create user 'omeka'@'localhost' identified by '********';
create database omeka;
grant all privileges on omeka.* to 'omeka'@'localhost';
show databases;
\q
```

---

## Step 5: Configure db.ini

Located and edited the `db.ini` file in the Omeka directory to add database credentials:

```bash
cd /var/www/html/omeka
sudo nano db.ini
```

Updated all fields containing placeholder values with the appropriate database name, username, and password.

---

## Step 6: Set File Permissions

Set correct ownership and permissions so the Apache web server could read application files and write to the `files/` directory:

```bash
sudo chown -R :www-data /var/www/html/omeka
sudo chmod -R 774 /var/www/html/omeka
```

---

## Step 7: Restart Services and Complete Web Install

Restarted Apache:

```bash
sudo systemctl restart apache2
```

Navigated to `http://[ip-address]/omeka/` in a web browser and completed the installation via the web form.

---

## Issues Encountered

### Issue 1: mod_rewrite Error

- **Problem:** After navigating to the Omeka URL, received an installation error stating `mod_rewrite is not enabled`
- **Cause:** Apache required an additional configuration stanza to allow `.htaccess` overrides in the Omeka directory
- **Solution:** Edited `/etc/apache2/apache2.conf` and added the following block below the `<Directory /var/www/>` stanza:

```apache
<Directory /var/www/html/omeka>
        Options Indexes FollowSymLinks
        AllowOverride ALL
        Require all granted
</Directory>
```

Then restarted Apache:

```bash
sudo systemctl restart apache2
```

---

### Issue 2: VM Crash When Editing apache2.conf

- **Problem:** The VM crashed every time `apache2.conf` was opened for editing, which had also caused a previous VM to become unusable
- **Cause:** The issue was traced to using `sudo edit` to open the file, which caused unexpected behavior
- **Solution:** Switched to `sudo nano` to edit the file, which worked without issue

---

## Post-Installation: WordPress Admin Access Issue

After completing the Omeka installation, it was discovered that the WordPress admin panel (`/wp-admin/`) was no longer accessible. The main WordPress site loaded fine, but attempts to reach the admin panel would hang indefinitely.

- **Cause:** The VM's external IP address had changed since WordPress was originally installed. Google Cloud does not assign static external IPs by default, meaning the IP can change each time the VM is stopped and restarted. WordPress stores the site URL and home URL in the database, so after the IP changed, WordPress was redirecting all admin requests to the old, unreachable IP address.
- **Solution:** Updated the `siteurl` and `home` values in the WordPress database to reflect the current external IP address. Admin access was immediately restored.
- **Prevention:** To avoid this issue in the future, a static external IP address should be reserved in Google Cloud under **VPC Network → IP Addresses**.

---

## Reflection

This installation followed the same general pattern as the bare bones ILS and WordPress assignments, but required more troubleshooting than expected. The mod_rewrite error and the VM crash during config file editing made the process more difficult than it needed to be. Discovering that Google Cloud reassigns external IP addresses on VM restart was an important lesson — one that explained several confusing issues throughout the semester. Going forward, reserving a static IP at the start of a project would save significant time.

---
