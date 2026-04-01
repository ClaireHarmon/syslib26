# OPAC/Cataloging Documentation & Reflection

## Introduction

An Online Public Access Catalog (OPAC) is a user-facing system that allows patrons to search, retrieve, and view bibliographic records 
stored in a library’s database. It serves as the discovery layer of an Integrated Library System (ILS), connecting users to materials like books, 
journals, and other media. In this project, a basic OPAC was developed using a LAMP stack (Linux, Apache, MySQL, PHP). The OPAC provides a simple web 
interface that allows users to search the database and retrieve matching records. While minimal, it demonstrates how an ILS works and its purpose: 
storing, querying, and displaying bibliographic data.

## Relational Database Structure

Relational databases store data in structured tables that are connected through relationships. In this project, bibliographic records are stored in a 
MySQL database (`opacdb`) within a table called `books`. Each row in the table represents a single record, and each column represents a specific piece of data, such as author, title, publisher, and copyright date.

The `books` table uses an `id` field as a primary key. This ensures that each record is unique and can be referenced reliably. 
Other fields use appropriate data types, such as `VARCHAR` for text and `DATE` for publication information. 
Choosing correct data types is important because it affects how data can be stored, searched, and filtered.

The OPAC interacts with this database by retrieving data using SQL queries. When a user submits a search through the web interface, the PHP script 
sends a query to MySQL, which returns matching records. These results are then displayed in the browser. This demonstrates how relational databases 
allow efficient searching and filtering of structured data.

The cataloging module interacts with the database in the opposite way. Instead of retrieving data, it inserts new records into the `books` table. 
When a user submits the cataloging form, the PHP script takes the input values and uses an SQL `INSERT` statement to store them in the database.

Together, these components show how relational databases support both data entry and data retrieval. The OPAC provides access to stored records, 
while the cataloging module allows new records to be added, demonstrating the core functionality of a basic library system.

## Step-by-Step Setup

### Database Connection

The PHP scripts connect to the MySQL database using credentials stored in a separate file (`login.php`). This file contains the hostname, database name, username, and password. Each PHP file uses `require_once` to load these credentials and establish a connection using the `mysqli` object. This allows the scripts to securely interact with the database without exposing credentials in multiple files.

---

### Cataloging Module (Data Entry)

The cataloging module consists of an HTML form (`index.html`) and a PHP script (`insert.php`). The HTML form collects user input for author, title, publisher, and copyright date. When the form is submitted, the data is sent to `insert.php` using the POST method.

The PHP script validates the input and uses a prepared SQL `INSERT` statement to add the record to the `books` table. Prepared statements are used to prevent SQL injection and ensure safer data handling. If the insertion is successful, the script confirms that a new record was created.

---

### OPAC Search and Retrieval

The OPAC consists of an HTML page (`mylibrary.html`) and a PHP script (`search.php`). The HTML page contains a search form where users can enter 
keywords and a date range. 
When the form is submitted, the data is sent to `search.php`.

The PHP script processes the input and constructs a prepared SQL query that searches the `books` table. It uses wildcard matching (`LIKE`) to allow 
partial matches and filters results based on the provided date range. The results are then displayed in an HTML table, showing the matching records.

---

### Making the System More Real-World

To make this system more realistic, additional features would be needed:

- More complex database structure (multiple related tables instead of one)  
- Support for standardized metadata formats (like MARC or Dublin Core)  
- Improved user interface to make things look nicer and more professional 
- More advanced search options (filters, sorting, etc.)  
- Stronger security 

These improvements would make the system closer to a full integrated library system (ILS).

---

### Configuration Steps

Several configuration steps were required to ensure the system worked correctly:

- Installing Apache, PHP, and MySQL to create the LAMP stack  
- Creating the `opacdb` database and `books` table  
- Installing the PHP MySQL module (`php-mysql`)  
- Restarting Apache and MySQL after changes  
- Setting correct file permissions and ownership (e.g., `www-data`)  
- Configuring Apache authentication using `.htaccess` and `htpasswd` for the cataloging module  

These steps ensured that the web server, database, and PHP scripts could communicate properly and securely.

## Key Details

Several small but important details were necessary to ensure the system functioned correctly.

One important detail was the use of correct data types in the database. 
For example, using `VARCHAR` for text fields like author and title allows flexible input, while using the `DATE` data type for copyright ensures 
that date filtering works properly in search queries. If the wrong data types were used, certain queries, especially those involving date ranges, 
would not function as expected.

Another key detail was the use of prepared statements in PHP. These prevent "SQL injection", which was a new term for me, and ensure that user input 
is handled safely. Without prepared statements, the system would be vulnerable to anyone's input.

File placement and permissions were also important. The `login.php` file was stored outside the public web directory and given restricted 
permissions so that sensitive database credentials were not exposed. Additionally, files needed to be readable by the Apache user (`www-data`)
 for the system to function.

Apache configuration was another important detail. The `.htaccess` file required the correct `AllowOverride` setting in `apache2.conf`, and the 
directory configuration needed to be properly enabled. A small mistake, such as a commented-out configuration block, prevented authentication from 
working until it was corrected.

Finally, restarting services after making changes (such as Apache or MySQL) was necessary to apply configurations. Forgetting this step can lead to
 confusion when changes appear not to work.

## Using Documentation

While working through this project, I primarily followed the lecture text and videos, 
but I also referenced additional documentation when needed to better understand certain parts of the code and setup. For example, I reviewed basic 
PHP syntax and MySQL query structure to understand how prepared statements and database connections were working.

Writing out the PHP code helped reinforce how the different components interact, especially how user input is passed from an HTML form to a PHP script 
and then used in an SQL query. In some cases, I looked up specific functions to understand their purpose and behavior.

One gap I encountered was understanding Apache configuration, particularly how `.htaccess` files work and how `AllowOverride` settings affect them. 
This was not immediately clear from the lecture alone, and I had to troubleshoot the issue when authentication did not work as expected. 
Through this process, though, I learned how Apache applies configuration rules and how to verify whether `.htaccess` is being read.

Overall, using documentation alongside the lecture materials helped clarify how each part of the system works together and made it easier to 
troubleshoot issues when something did not behave as expected.
