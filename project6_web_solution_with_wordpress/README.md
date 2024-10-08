# Web Solution with WordPress

In this project, you’ll set up a basic WordPress-based web solution using two EC2 instances running RedHat or CentOS. One instance will act as the Web Server and the other as the Database Server, with WordPress connected to a remote MySQL database.

## Project Overview

- **Web Server**: EC2 instance to host WordPress.
- **Database Server**: EC2 instance to host MySQL.
- **OS**: RedHat or CentOS.

---

## Prerequisites

- AWS EC2 account with permissions to create instances and EBS volumes.
- A key pair for SSH access to EC2 instances.
- Basic knowledge of Linux commands.

---

## Part 1: Configure Storage for the Web Server

### 1. Launch Web Server EC2 Instance and Attach Volumes

- Launch an EC2 instance with RedHat or CentOS to serve as your Web Server.
- Create and attach three EBS volumes, each with a size of 10 GiB, to the instance.

![add_volume_on_webserver_instance](./images/webserver_redhat.png)

### SSH into the Instance and Verify Volumes

```bash
ssh -i keypair.pem ec2-user@<public-ip>
```

### Then launch your instance.

Confirm your volumes are up and in use.
![add-volumes](./images/add_volume_on_webserver_instance.png)

### List attached volumes

```bash
lsblk
```

![lsblk_utility](https://github.com/user-attachments/assets/91038661-d5ef-4f40-be8a-8773672b9832)

### Partition the Volumes

```bash
sudo gdisk /dev/nvme1n1
```

- Type `n` to create a new partition, then accept the defaults.
- Repeat for the other volumes.

![sudo_gdisk](https://github.com/user-attachments/assets/96fd40b5-10df-4b7a-8853-68a59b438efa)

Install lvm2 package. Lvm2 is used for managing disk drives and other storage devices

```bash
sudo yum install lvm2
```

![install_lvm2](./images/install_lvm2.png)

Run `sudo lvmdiskscan` to check for available partitions.
Use the pvcreate utility tool to mark each of the volumes as physical volumes

```bash
sudo pvcreate /dev/nvme1n1p1
sudo pvcreate /dev/nvme2n1p1
sudo pvcreate /dev/nvme3n1p1
```

![VG_created_and_successfully_running](./images/VG_created_and_successfully_running.png)

### Configure Logical Volumes

```bash
sudo pvcreate /dev/nvme1n1p1 /dev/nvme2n1p1 /dev/nvme3n1p1
sudo vgcreate webdata-vg /dev/nvme1n1p1 /dev/nvme2n1p1 /dev/nvme3n1p1
sudo lvcreate -n app-lv -L 14G webdata-vg
sudo lvcreate -n logs-lv -L 14G webdata-vg
```

Verify that the physical volume has been created `sudo pvs`

![VG_created_and_successfully_running](https://github.com/user-attachments/assets/706e6bd2-56f9-49b9-a250-1ad151b9539e)

### Format and Mount the Logical Volumes

```bash
sudo mkfs -t ext4 /dev/webdata-vg/app-lv
sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
sudo mkdir -p /var/www/html /home/recovery/logs
sudo mount /dev/webdata-vg/app-lv /var/www/html
sudo rsync -av /var/log/. /home/recovery/logs/
sudo mount /dev/webdata-vg/logs-lv /var/log
```

![format_the_logical_volumes_with_ext4_filesystem](https://github.com/user-attachments/assets/f0dd225f-c9b8-414d-8827-f605999573e8)

---

## Part 2: Configure MySQL on the Database Server

### 1. Launch Database Server EC2 Instance and Configure Storage

- Launch a new EC2 instance with RedHat or CentOS as your Database Server.
- Follow the same steps as in Part 1 to create and mount volumes on the Database Server, but use the directory `/db` and logical volume `db-lv`.

![database_rehart](https://github.com/user-attachments/assets/dcc68fa4-5e59-4ccd-9fec-beddeac4e8af)

### 2. Install and Configure MySQL

```bash
sudo yum install -y mysql-server
sudo systemctl start mysqld
sudo systemctl enable mysqld
```

![mysql_db_running](https://github.com/user-attachments/assets/362ab232-7c01-44d6-819c-5a7cce8627f8)

### 3. Create a MySQL User for WordPress

```sql
CREATE DATABASE wordpress;
CREATE USER 'wordpress_user'@'<web-server-ip>' IDENTIFIED BY 'your_password';
GRANT ALL ON wordpress.* TO 'wordpress_user'@'<web-server-ip>';
FLUSH PRIVILEGES;
```

![show_databases;](https://github.com/user-attachments/assets/910f9442-1f5b-448c-93a2-f5c8cbd32ddc)

---

## Part 3: Install WordPress on the Web Server

### 1. Install Apache, PHP, and Dependencies

```bash
sudo yum install -y httpd php php-mysqlnd php-fpm
sudo systemctl start httpd
sudo systemctl enable httpd
```

![install_httpd_php_php-mysqlnd_php-fpm_php-json](https://github.com/user-attachments/assets/ab0e1d05-1ffd-46bf-8e34-53c9d678d7d1)

### 2. Download and Configure WordPress

```bash
wget http://wordpress.org/latest.tar.gz
tar xzvf latest.tar.gz
sudo cp -R wordpress/* /var/www/html/
sudo chown -R apache:apache /var/www/html/
```

![mount_wordpress_webserver](https://github.com/user-attachments/assets/d36ab0fa-ffd9-4f10-be3f-e192f0bf30de)

### 3. Configure SELinux and Firewall for Apache

```bash
sudo setsebool -P httpd_can_network_connect=1
sudo firewall-cmd --zone=public --permanent --add-service=http
sudo firewall-cmd --reload
```

### 4. Finalize WordPress Installation

- Go to `http://<web-server-ip>/wordpress` to complete the WordPress setup in the browser.

---

Congratulations! You have successfully deployed a WordPress website with a MySQL backend on AWS. Make sure to properly secure your instances and regularly backup your data.
