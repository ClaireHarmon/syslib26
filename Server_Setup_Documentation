# LAMP Stack Setup Documentation

## Overview

A LAMP stack is a collection of open-source software used to host dynamic websites and web applications. The acronym stands for:

- **Linux** (operating system)
- **Apache** (web server)
- **MySQL** (database server)
- **PHP** (server-side scripting language)

This stack is commonly used because it is free, widely supported, and provides all the components needed to build and serve dynamic, data-driven websites.

---

# Apache Installation and Verification

## Install Apache

```
sudo apt update
sudo apt install apache2
```

## Verify Apache Status

```
systemctl status apache2
```

Result: Apache was **active (running)**.

## Test in Browser

Navigated to:

```
http://34.171.11.63
```

The default Apache page loaded, confirming the server was working.

---

# PHP Installation and Configuration

## Install PHP

```
sudo apt install php libapache2-mod-php
sudo systemctl restart apache2
```

## Verify PHP Version

```
php -v
```

Confirmed PHP was installed.

## Test PHP with phpinfo()

```
cd /var/www/html
sudo edit info.php
```

Contents:

```php
<?php
phpinfo();
?>
```

Visited:

```
http://34.171.11.63/info.php
```

The PHP configuration page displayed, confirming PHP was working with Apache.

## Remove Test File for Security

```
sudo rm /var/www/html/info.php
```

## Configure Apache Directory Index

```
cd /etc/apache2/mods-available
sudo cp dir.conf dir.conf.bak
sudo edit dir.conf
```

Updated:

```
DirectoryIndex index.php index.html
```

Then tested and reloaded:

```
apachectl configtest
sudo systemctl reload apache2
```

---

# MySQL Installation and Configuration

## Install MySQL

```
sudo apt install mysql-server
```

## Verify Installation

```
mysql --version
systemctl status mysql
```

Result: MySQL was installed and active/running.

---

## Secure MySQL

```
sudo mysql_secure_installation
```

Selections:

- Validate passwords: Yes  
- Policy: LOW  
- Remove anonymous users: Yes  
- Disable remote root login: Yes  
- Remove test database: Yes  
- Reload privileges: Yes  

---

## Create Database and User

Login as root:

```
sudo mysql -u root
```

Create user:

```
create user 'opacuser'@'localhost' identified by 'tXXXXXXXX';
```

Create database:

```
create database opacdb;
```

Grant privileges:

```
grant all privileges on opacdb.* to 'opacuser'@'localhost';
```

---

## Create Table and Insert Data

```
use opacdb;
```

Create table:

```
create table books (
    id int unsigned not null auto_increment,
    author varchar(150) not null,
    title varchar(150) not null,
    copyright year not null,
    primary key (id)
);
```

Insert records:

```
insert into books (author, title, copyright) values
('Jennifer Egan', 'The Candy House', '2022'),
('Imbolo Mbue', 'How Beautiful We Were', '2021'),
('Lydia Millet', 'A Children\'s Bible', '2020'),
('Julia Phillips', 'Disappearing Earth', '2019');
```

Verify:

```
select * from books;
```

---

# Connecting PHP and MySQL

## Install PHP MySQL Module

```
sudo apt install php-mysql
sudo systemctl restart apache2
sudo systemctl restart mysql
```

---

## Create Secure Login File

```
cd /var/www
sudo touch login.php
sudo chmod 640 login.php
sudo chown :www-data login.php
```

Contents:

```php
<?php
$db_hostname = "localhost";
$db_database = "opacdb";
$db_username = "opacuser";
$db_password = "your_password";
?>
```

---

## Create PHP Web Page

```
cd /var/www/html
sudo edit opac.php
```

This script:

- connects to MySQL using credentials
- runs SQL queries
- displays results as HTML

---

# Verification of Full Stack

## Command Line Testing

```
sudo php -f /var/www/html/opac.php
```

Result: HTML output displayed with database data.

## Browser Testing

Visited:

```
http://34.171.11.63
```

The webpage displayed:

- book titles  
- authors  
- publication years  

This confirmed that Apache, PHP, and MySQL were working together.

---

# Challenges Encountered

- Initially entered the MySQL password incorrectly when logging in as `opacuser`.  
  - Resolved by re-entering the correct password.

No other major errors occurred during setup.

---

# Reflection

I learned that setting up each part of the LAMP stack is not very difficult, but it is important to verify each step along the way. 
Checking that services were running and testing them in the browser as I went helped to confirm everything was working.

Creating databases and tables helped me understand how data is organized in MySQL. I have started working with SQL in another class, so I was happy
to see something familiar!
Connecting PHP to the database made it clear how websites can display dynamic content.

I also learned that configuration changes, like modifying the DirectoryIndex or setting file permissions, affect how the system behaves and can improve 
security.

If I repeated this setup, I would more thoroughly document commands and results as I go so I can easily retrace my steps if something goes wrong.
Thankfully, nothing went truly wrong with my setup, and I was able to follow the video and lecture text. 

