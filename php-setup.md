# PHP Installation and Apache Configuration

## Overview

This document records the process of installing PHP on an Ubuntu virtual machine and configuring it to work with the Apache web server.
 The goal was to confirm that Apache could process PHP files correctly and be seen in a web browser.

This process also included modifying Apache's configuration so that `index.php` is prioritized over `index.html`, 
and creating a simple PHP browser detection page.

---

## Environment

- VM Provider: Google Cloud
- OS: Ubuntu 22.04 LTS
- Web Server: Apache2
- PHP Version: PHP 8.3.6
- Browser Used for Testing:Safari

---

## Step 1: Install PHP and Apache PHP Module

PHP and the Apache module that allows Apache to process PHP files were installed using `apt`.

```
sudo apt install php libapache2-mod-php
```


After installation, Apache was restarted so the module would load.

```
sudo systemctl restart apache2
```

---

## Step 2: Verify PHP Installation

To confirm that PHP installed correctly, I checked the installed version:

```
php -v
```

The command returned the installed PHP version, confirming that PHP was available on the system.

---

## Step 3: Verify Apache Status

After installing PHP and restarting Apache, I confirmed that the Apache service was still running properly.

```
systemctl status apache2
```

The output showed that Apache was **active (running)**.

---

## Step 4: PHP Test File

To test that Apache could correctly process PHP, I created a PHP file.

First, I navigated to the directory:

```
cd /var/www/html
```

Then I created a test file:

```
sudo edit info.php
```

I added the following code in edit:

```php
<?php
phpinfo();
?>
```

This script displays detailed information about the PHP installation and server configuration.

---

## Step 5: Test PHP in the Browser

To confirm PHP was working with Apache, I opened a regular browser on Safari and went to:

```
http://34.171.11.63/info.php
```

The browser showed the PHP configuration page with system and PHP information. This confirmed that Apache was correctly processing PHP files.

---

## Step 6: Remove the PHP Info File

Because `phpinfo()` exposes detailed system information, we had to delete it to protect our information.

I removed the file using:

```
sudo rm /var/www/html/info.php
```

---

## Step 7: Configure Apache to Prioritize index.php

By default, Apache serves `index.html` when a user visits the root of a website. 
Since this server will run PHP pages, we configured Apache to prioritize `index.php`.

First, I navigated to the Apache configuration directory:

```
cd /etc/apache2/mods-available/
```

Before making changes, I created a backup of the configuration file:

```
sudo cp dir.conf dir.conf.bak
```

Then I opened the original file for editing:

```
sudo edit dir.conf
```

The `DirectoryIndex` line was modified so `index.php` appears first:

```
DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
```

This change tells Apache to look for `index.php` before `index.html`.

---

## Step 8: Test Apache Configuration

Before reloading Apache, I tested the configuration syntax:

```
apachectl configtest
```

The output returned:

```
Syntax OK
```

This confirmed there were no configuration errors.

---

## Step 9: Reload Apache

After confirming the configuration was valid, Apache was reloaded to apply the changes.

```
sudo systemctl reload apache2
```

I checked the service status again:

```
systemctl status apache2
```

Apache was up and running fine.

---

## Step 10: Create a PHP Browser Detection Page

To further test PHP processing, I created a new PHP file in the web root.

```
cd /var/www/html
sudo edit index.php
```

The file included HTML and PHP code that identifies the visitor's browser and operating system.

I didn't understand the code, and I didn't have time to type it all out. So I copied and pasted from the lecture text. 

---

## Step 11: Test the PHP Page

From my local machine, I visited the server's IP address in a browser:

```
http://34.171.11.63
```

Apache now shows `index.php` because it is listed first in the `DirectoryIndex`.

The webpage displayed my correct browser and operating system, confirming that PHP was executing correctly on the server.

---

## Issues Encountered

I encountered no errors  during the installation or configuration process.
All of my commands executed successfully, and the results matched those demonstrated in the lecture material.

---

## Reflection

This exercise clarified the difference between client-side and server-side programming.
PHP executes on the server before the page is sent to the client. 
The browser never sees the PHP code itself. It only receives the generated HTML code.
 
Without the `libapache2-mod-php` module, Apache would not know how to interpret PHP files.

Editing the `DirectoryIndex` configuration showed how Apache determines which file to serve when a user visits the root of a site. 
Changing the order of the files changes the behavior of the server.

The `phpinfo()` test showed how much information about the system can be exposed through PHP. 
I learned that files like this should be removed after testing, since they could reveal sensitive information to attackers.

