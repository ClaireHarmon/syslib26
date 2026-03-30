# OPAC and Cataloging Module Setup

## Overview

This document records the process of creating a basic OPAC (search interface) and a cataloging module (data entry interface) using MySQL, PHP, and Apache. The goal was to better understand how relational databases connect to web applications.

---

## Environment

- OS: Ubuntu 24.04  
- Web Server: Apache2  
- PHP Version: PHP 8.3.6  
- Database: MySQL 8.0.45  
- VM Provider: Google Cloud  

---

## OPAC Setup

Created a search interface using an HTML form and a PHP script.

- File: `mylibrary.html`
- File: `search.php`

The form allows users to:
- search by author, title, or publisher  
- filter results by date range  

Verified by visiting:

```
http://34.28.34.108/mylibrary.html
```

The page loaded correctly and returned expected results from the database.

---

## Cataloging Module Setup

Created a data entry interface for adding records to the database.

- Directory: `/var/www/html/cataloging`
- Files:
  - `index.html` (form)
  - `insert.php` (processes form input)

Verified by visiting:

```
http://34.28.34.108/cataloging/index.html
```

Successfully submitted records and confirmed they appeared in the OPAC.

---

## Security Configuration

Configured Apache authentication using `htpasswd`.

Commands used:

```
sudo htpasswd -c /etc/apache2/.htpasswd libcat
```

Created `.htaccess` file:

```
AuthType Basic
AuthName "Authorization Required"
AuthUserFile /etc/apache2/.htpasswd
Require valid-user
```

Updated Apache configuration:

```
<Directory /var/www/html/cataloging/>
  Options Indexes FollowSymLinks
  AllowOverride AuthConfig
  Require all granted
</Directory>
```

Restarted Apache:

```
sudo systemctl restart apache2
```

---

## Verification

- Accessing cataloging module prompted for username and password  
- Successfully logged in using configured credentials  
- Submitted new records through the form  
- Verified records appeared in OPAC search results  

---

## Issues Encountered

### Issue 1: No Authentication Prompt

- Problem: The cataloging page did not prompt for a username and password  
- Cause: The `<Directory /var/www/html/cataloging/>` block in `apache2.conf` was commented out  
- Solution: Removed the `#` to enable the configuration and restarted Apache  

---

### Issue 2: `.htaccess` Not Appearing to Work

- Problem: Changes to `.htaccess` had no effect  
- Cause: Apache was ignoring the configuration due to the commented-out directory block  
- Solution: Enabled the directory block and confirmed `.htaccess` was being read  

---

### Issue 3: VM Disconnection

- Problem: Unexpectedly disconnected from the VM during setup  
- Solution: Reconnected and verified that files and configurations were still intact before continuing  

---

## Reflection

I learned how MySQL, PHP, and Apache work together to create a system where data can be stored, retrieved, and displayed through a web interface. 
Working directly in the command line made SQL and database relationships clearer.
Creating both the OPAC and cataloging module helped me understand how data flows into and out of a database.
Troubleshooting the Apache configuration also showed how important correct configuration and file placement are. 
Overall, this made the connections between the database, server-side code, and the web interface much clearer.
