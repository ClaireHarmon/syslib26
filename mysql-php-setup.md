# MySQL Installation and PHP Integration

## Overview

This document records the process of installing and configuring MySQL and integrating it with Apache and PHP to complete the LAMP stack. 
The goal was to install MySQL, secure the installation, create a database and user account, populate a table with records, and connect PHP 
to the database to display the stored data through a webpage.

---

## Environment

- VM Provider: Google Cloud  
- OS: Ubuntu 24.04  
- Web Server: Apache2  
- PHP Version: PHP 8.3.6 
- Database Server: MySQL 8.0.45  
- Browser Used for Testing: Desktop web browser  

---

# System Preparation

Before installing MySQL, I updated the system packages.

```
sudo apt update
sudo apt upgrade
sudo apt autoremove
sudo apt clean
```

The update and upgrade completed successfully. Fewer than ten packages were upgraded, and no reboot was required. 
No unnecessary packages were removed and no errors occurred.

---

# Installing and Verifying MySQL

MySQL Community Server was installed using:

```
sudo apt install mysql-server
```

The installation completed successfully without errors.

I verified the installed version using:

```
apt policy mysql-server
mysql --version
```

Output confirmed that MySQL **8.0.45** was installed.

Next I verified that the service was running:

```
systemctl status mysql
```

The output indicated:

```
Loaded: loaded
Active: active (running)
```

This confirmed the MySQL server was installed and operational.

---

# Securing the MySQL Installation

To apply recommended security settings, I ran:

```
sudo mysql_secure_installation
```

Responses used during configuration:

- Validate passwords: **Y**
- Password policy: **LOW**
- Remove anonymous users: **Y**
- Disallow remote root login: **Y**
- Remove test database: **Y**
- Reload privilege tables: **Y**

The script completed successfully without errors.

---

# Creating a Database and User

I logged into MySQL as the root user:

```
sudo mysql -u root
```

Default databases were confirmed with:

```
show databases;
```

The following databases appeared:

- information_schema  
- mysql  
- performance_schema  
- sys  

Next I created a new user account:

```
create user 'opacuser'@'localhost' identified by 'this_is_a_test123';
```

Output:

```
Query OK, 0 rows affected
```

Then I created a new database and granted privileges to the user:

```
create database opacdb default character set utf8mb4 collate utf8mb4_0900_ai_ci;
grant all privileges on opacdb.* to 'opacuser'@'localhost';
```

Running `show databases;` confirmed the new database **opacdb** existed.

---

# Logging in as the New User

I exited MySQL and logged in as the new user:

```
mysql -u opacuser -p
```

After entering the password successfully, the prompt appeared as:

```
[(none)]>
```

I confirmed the database was accessible:

```
show databases;
```

Output included:

- information_schema  
- opacdb  
- performance_schema  

Then I selected the database:

```
use opacdb;
```

The prompt changed to:

```
[opacdb]>
```

---

# Creating and Populating Tables

I created a table called **books**:

```
create table books (
    id int unsigned not null auto_increment,
    author varchar(150) not null,
    title varchar(150) not null,
    copyright year not null,
    primary key (id)
);
```

The table was confirmed with:

```
show tables;
describe books;
```

Fields created:

| Field | Type |
|------|------|
| id | int unsigned |
| author | varchar(150) |
| title | varchar(150) |
| copyright | year |

The `id` field was configured as the **primary key with auto_increment**.

---

## Inserting Records

I inserted four records into the table:

```
insert into books (author, title, copyright) values
('Jennifer Egan', 'The Candy House', '2022'),
('Imbolo Mbue', 'How Beautiful We Were', '2021'),
('Lydia Millet', 'A Children\'s Bible', '2020'),
('Julia Phillips', 'Disappearing Earth', '2019');
```

Output:

```
Query OK, 4 rows affected
```

I confirmed the records using:

```
select * from books;
```

All four records appeared correctly. Additional practice queries (SELECT, ALTER, UPDATE, DELETE, and ORDER BY)
 were also executed successfully without errors.

---

# Installing PHP Support for MySQL

To allow PHP to communicate with MySQL, I installed the PHP MySQL module:

```
sudo apt install php-mysql
```

Then I restarted the services:

```
sudo systemctl restart apache2
sudo systemctl restart mysql
```

Both services restarted successfully.

---

# Creating Secure Database Credentials

To store database credentials securely, I created a file outside the public web directory.

```
cd /var/www
sudo touch login.php
sudo chmod 640 login.php
sudo chown :www-data login.php
```

File permissions:

```
-rw-r----- 1 root www-data login.php
```

The file contains:

```php
<?php // login.php
$db_hostname = "localhost";
$db_database = "opacdb";
$db_username = "opacuser";
$db_password = "this_is_a_test123";
?>
```

---

# Creating the PHP Database Page

Next I created a PHP script that retrieves information from the database.

```
cd /var/www/html
sudo edit opac.php
```

This script:

- loads credentials from `login.php`
- connects to MySQL
- executes prepared SQL queries
- outputs results as HTML

---

# Testing PHP Scripts

I verified the scripts from the command line.

```
sudo php -f /var/www/login.php
```

No output appeared, indicating no syntax errors.

Next:

```
sudo php -f /var/www/html/opac.php
```

The terminal displayed HTML output containing the database query results.

Finally, I opened the server's public IP address in a browser. The webpage successfully displayed the book records retrieved from the database.

---

# Reflection

I learned that installing and configuring MySQL is fairly straightforward, but it is important to verify that services are running and
 working correctly after installation. Creating databases, users, and tables from the command line helped me understand how 
 relational databases are structured and how SQL commands are used to manage data. I also learned how PHP connects to a MySQL database 
 and retrieves information to display on a webpage. Seeing the book records appear in the browser made the connection between the database 
 and the website much clearer.

I also saw why it is useful to store database credentials in a separate file and set proper file permissions so sensitive information is
 not publicly accessible.

If I repeated this setup, I would document commands immediately as I run them instead of summarizing afterward. This makes it easier to 
remember each step and helps with troubleshooting if something goes wrong.
