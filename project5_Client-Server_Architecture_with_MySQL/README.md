# Implementing Client-Server Architecture with MySQL on AWS EC2 Instances

This documentation provides a comprehensive guide on setting up a Client-Server Architecture using MySQL Database Management System (DBMS) on AWS EC2 instances. We’ll walk through creating and configuring two Linux-based virtual servers to demonstrate a basic client-server architecture using MySQL RDBMS.

## Overview of Client-Server Architecture

In a client-server setup, two or more computers communicate over a network where one computer (the Client) sends requests, and another computer (the Server) responds. For this setup, we use MySQL as the database server and connect it with a client to showcase database connectivity.

### Architecture Overview

In this architecture:

- **Server A** (MySQL Server) hosts the MySQL database and listens on port 3306.
- **Server B** (MySQL Client) sends requests to the MySQL server over a local network.

## Steps to Set Up Client-Server Architecture

### 1. Set Up EC2 Instances

Log into your AWS Console and create two EC2 instances (t2.micro type) with Ubuntu 24.04 LTS in the **us-east-1** region.

#### Instance Details

- **Instance A**: `mysql-server`
- **Instance B**: `mysql-client`

For each instance:

1. **Application & OS Images**: Select Ubuntu.
   ![Application & OS Images](https://github.com/user-attachments/assets/b9dafc8a-d999-4e17-b312-5da3023204a1)

2. **Key Pair**: Create a new key pair or select an existing one.
   ![Select Existing Key Pair](https://github.com/user-attachments/assets/c7b349bf-863b-4c57-813e-bcc10eef5d3f)

3. **Network Settings**: Create a security group with inbound rules as shown:

   - **SSH (Port 22)**: Allow from any IP address.
   - **MySQL (Port 3306)**: Allow only from the IP address of `mysql-client`.

   ![Security Group](https://github.com/user-attachments/assets/2e2db048-9f5f-4ece-96a4-25410523a7b8)

4. **Storage & Launch**: Configure storage as required and launch the instances.
   ![Configure Storage](https://github.com/user-attachments/assets/3017f67a-1ac2-44c3-9ad0-e4a101a5c6a0)

5. **Identifying the MySQL Client’s Private IP**
   Retrieve the MySQL client’s private IP from the EC2 console for configuring security rules on the MySQL server.
   ![private_addres_of_mysql_client](https://github.com/user-attachments/assets/9cf552a6-0f07-4116-8874-ed339b85082c)

### 2. Configure MySQL Server on Server A (mysql-server)

1. **Update Package Lists**:

   ```bash
   sudo apt update -y
   ```

   ![Update Server](https://github.com/user-attachments/assets/56b1f724-3f61-4f0d-8d3f-e8c46d73b8f0)

2. **Install MySQL Server**:

   ```bash
   sudo apt install mysql-server -y
   ```

   ![Install MySQL Server](https://github.com/user-attachments/assets/b6687b94-ca31-4e08-9f7d-bddfc136e2f2)

3. **Enable and Start MySQL**:

   ```bash
   sudo systemctl enable mysql
   sudo systemctl start mysql
   ```

   ![Enable MySQL](https://github.com/user-attachments/assets/4f7d3b6c-a5b7-45e4-8e16-67a7f7954b40)

4. **Configure Remote Connections**: Edit MySQL configuration to allow connections from remote clients.

   ```bash
   sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
   ```

   Replace:

   ```plaintext
   bind-address = 127.0.0.1
   ```

   with:

   ```plaintext
   bind-address = 0.0.0.0
   ```

   ![Configure MySQL for Remote Connections](https://github.com/user-attachments/assets/4f8837af-52a4-45c3-b2ba-50a27d659c81)

5. **Create MySQL User and Grant Privileges**:

   - Create a user that allows connections from `mysql-client` IP.

   ```sql
   CREATE USER 'emmanuelist'@'172.31.37.15' IDENTIFIED BY 'Password.1';
   GRANT ALL PRIVILEGES ON *.* TO 'emmanuelist'@'172.31.37.15';
   FLUSH PRIVILEGES;
   ```

   ![Create User](https://github.com/user-attachments/assets/94020905-7121-4c02-bd00-6b216f291fd3)

6. **Restart MySQL**:
   ```bash
   sudo systemctl restart mysql
   ```

### 3. Configure MySQL Client on Server B (mysql-client)

1. **Update Package Lists**:

   ```bash
   sudo apt update -y
   ```

   ![Update Client Instance](https://github.com/user-attachments/assets/05b1b6f9-0b4f-4b04-bc99-a5350edf020d)

2. **Install MySQL Client**:
   ```bash
   sudo apt install mysql-client -y
   ```
   ![Install MySQL Client](https://github.com/user-attachments/assets/2d03b934-ba9c-413e-ad09-becfd394ebdd)

### 4. Connect from MySQL Client to MySQL Server

- Use the MySQL client installed on `mysql-client` to connect to the MySQL server on `mysql-server` using its private IP address (e.g., `172.31.44.119`):

  ```bash
  mysql -h 172.31.92.86 -u emmanuelist -p
  ```

  ![Connect to MySQL Server](https://github.com/user-attachments/assets/9ff111cb-6849-4253-ab3f-f458badc33d3)

- Run basic SQL commands to verify connectivity.
  ```sql
  SHOW DATABASES;
  ```
  ![Show Databases](https://github.com/user-attachments/assets/21ba2e46-77de-4b95-8957-7c422a1911be)
