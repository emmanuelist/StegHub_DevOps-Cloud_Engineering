# LEMP Stack Implementation Guide

The LEMP stack (Linux, Nginx, MySQL, PHP) is a powerful web server platform for hosting web applications. In this guide, we will set up a LEMP stack on an Ubuntu server, walking through all the steps required to get the stack running.

## Introduction

Developed by Igor Sysoev in October 2004, NGINX was created to solve Apache’s concurrency issues. NGINX is an open-source web server known for its high performance, stability, low resource usage, and scalability.

Organizations using NGINX include Microsoft, IBM, Google, Adobe, LinkedIn, and Facebook.

### Benefits of NGINX

- **Handles heavy traffic:** NGINX can manage spikes in traffic efficiently.
- **Lightweight and customizable:** It is easy to configure and has a strong track record of stability.
- **Mitigates security risks:** NGINX can also defend against DDoS attacks.
- **Scalable:** Its efficient coding style avoids bottlenecks and downtime.

---

## Prerequisites

1. **AWS Account:** You will need an AWS account to set up an EC2 instance running Ubuntu Server.
2. **Git Bash:** This terminal is necessary for SSH access to your AWS instance.

### Step 0: Setting Up the Environment

- **Launch an EC2 instance:**
  Create a new key pair, then launch an EC2 instance using Ubuntu Server as your OS.

![Launch Instance](https://github.com/user-attachments/assets/5e6dee65-333d-4b70-b6cb-3b1072dd5fdb)

- **Connect via SSH:** Once the instance is up and running, use Git Bash to SSH into your server using the PEM file.

```bash
chmod 400 emmanuelist_ec2.pem
ssh -i emmanuelist_ec2.pem ubuntu@13.51.168.11
```

![SSH using Git Bash](https://github.com/user-attachments/assets/0c03a77b-fb72-42a1-9517-5011638b2ca9)

---

## Step 1: Installing Nginx

To install Nginx, follow these steps:

```bash
sudo apt-get update
sudo apt-get install nginx
```

Check that Nginx is running with:

```bash
sudo systemctl status nginx
```

![Install Nginx](https://github.com/user-attachments/assets/b916897d-5a27-44bd-b242-9bd2fee89143)

You can confirm that Nginx is working locally:

```bash
curl http://localhost:80
```

Then, test it externally by navigating to `http://<your_public_ip>:80` in your browser.

![Welcome to Nginx](https://github.com/user-attachments/assets/eb87cf0f-b95b-4815-9a50-3a667745958c)

---

## Step 2: Installing MySQL

Next, install MySQL on the server with:

```bash
sudo apt install mysql-server
```

![Install MySQL](https://github.com/user-attachments/assets/c26e6161-301f-4408-a210-e361a2e1f05c)

After installation, secure your MySQL setup by running:

```bash
sudo mysql_secure_installation
```

You can then log into MySQL:

```bash
sudo mysql
```

Set a password for the root user:

```bash
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';
```

![MySQL Secure Installation](https://github.com/user-attachments/assets/60673012-df92-4d4d-b957-1199ddcdf2f0)

---

## Step 3: Installing PHP

Install PHP alongside necessary MySQL extensions:

```bash
sudo apt install php-fpm php-mysql
```

![PHP Installation](https://github.com/user-attachments/assets/2280220b-3ab1-4206-92ed-539d2c17ce1b)

---

## Step 4: Configuring Nginx to Use PHP Processor

Next, configure Nginx to work with PHP. First, create a web root directory for your project:

```bash
sudo mkdir /var/www/projectLEMP
sudo chown -R $USER:$USER /var/www/projectLEMP
```

Create an Nginx configuration file:

```bash
sudo nano /etc/nginx/sites-available/projectLEMP
```

Add the following configuration to the file:

```nginx
server {
   listen 80;
   server_name projectLEMP www.projectLEMP;
   root /var/www/projectLEMP;

   index index.html index.htm index.php;

   location / {
       try_files $uri $uri/ =404;
   }

   location ~ \.php$ {
       include snippets/fastcgi-php.conf;
       fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
   }

   location ~ /\.ht {
       deny all;
   }
}
```

Activate the configuration:

```bash
sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
```

Disable the default Nginx host:

```bash
sudo unlink /etc/nginx/sites-enabled/default
```

Reload Nginx:

```bash
sudo systemctl reload nginx
```

![Nginx Configuration](https://github.com/user-attachments/assets/fa1f5d91-4f9a-4c8f-8c79-c93070a4f664)

Create a test `index.html` file:

```bash
sudo echo 'Hello LEMP from hostname' > /var/www/projectLEMP/index.html
```

![Screenshot 2024-09-23 175308](https://github.com/user-attachments/assets/f5d59f78-8196-45ab-a746-8f72b036a362)

---

## Step 5: Retrieving Data from MySQL with PHP

Create a test database and user:

```bash
sudo mysql -u root -p
CREATE DATABASE example_db;
CREATE USER 'example_user'@'%' IDENTIFIED BY 'beautiful34';
GRANT ALL ON example_db.* TO 'example_user'@'%';
```

Next, create a table:

```bash
CREATE TABLE example_db.todo_list (
    item_id INT AUTO_INCREMENT,
    content VARCHAR(255),
    PRIMARY KEY(item_id)
);
```

Insert some data:

```bash
INSERT INTO example_db.todo_list (content) VALUES ("My first important item");
```

Now create a PHP script to display the data:

```bash
nano /var/www/projectLEMP/todo_list.php
```

Paste the following code:

```php
<?php
$user = "example_user";
$password = "beautiful34";
$database = "example_db";
$table = "todo_list";

try {
    $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
    echo "<h2>TODO</h2><ol>";
    foreach($db->query("SELECT content FROM $table") as $row) {
        echo "<li>" . $row['content'] . "</li>";
    }
    echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}
?>
```

![ToDo List](https://github.com/user-attachments/assets/5f0d4739-b019-44f0-af40-8525b45f9f79)

Navigate to `http://<your_public_ip>/todo_list.php` to view your todo list.

---

## Troubleshooting

### Bad Gateway Error

If you see a "502 Bad Gateway" error, it could be because of a mismatch between PHP versions in Nginx configuration. Update the PHP version in your Nginx config to match the installed version.
