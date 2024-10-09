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
sudo vgcreate my_volume_group /dev/nvme1n1p1 /dev/nvme2n1p1 /dev/nvme3n1p1
sudo lvcreate -n app-lv -L 14G my_volume_group
sudo lvcreate -n logs-lv -L 14G my_volume_group
```

Verify that the physical volume has been created `sudo pvs`

![VG_created_and_successfully_running](https://github.com/user-attachments/assets/706e6bd2-56f9-49b9-a250-1ad151b9539e)

### Format and Mount the Logical Volumes

```bash
sudo mkfs -t ext4 /dev/my_volume_group/app-lv
sudo mkfs -t ext4 /dev/my_volume_group/logs-lv
sudo mkdir -p /var/www/html /home/recovery/logs
sudo mount /dev/my_volume_group/app-lv /var/www/html
sudo rsync -av /var/log/. /home/recovery/logs/
sudo mount /dev/my_volume_group/logs-lv /var/log
```

![format_the_logical_volumes_with_ext4_filesystem](https://github.com/user-attachments/assets/f0dd225f-c9b8-414d-8827-f605999573e8)

#### Verify the setup by running sudo vgs

```bash
sudo vgs

```

Create 2 logical volumes; app-lv and logs-lv.

```bash
sudo lvcreate -n app-lv -L 14G webdata-vg
sudo lvcreate -n logs-lv -L 14G webdata-vg
```

![create_lvcreate_utility](./images/create_lvcreate_utility.png)
Verify that the logical volumes has been created `sudo lvs`

Verify the entire setup to be sure all has been configured properly

```bash
sudo vgdisplay -v #view complete setup - VG, PV, and LV
sudo lsblk
```

![verify_setup_using_sudo_vgdisplay_-v](./images/verify_setup_using_sudo_vgdisplay_-v.png)

format the logical volumes using ext4 filesystems

```bash
sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
```

Create a directory to store website file

```bash
sudo mkdir -p /var/www/html
```

Create another directory for the log files

```bash
sudo mkdir -p /home/recovery/logs
```

Mount the newly created directory for website files on the app logical volume we earlier created

```bash
sudo mount /dev/webdata-vg/apps-lv /var/www/html/
```

Back up all the files on the logs logical volume before mounting, this is done using rsync utility

```bash
sudo rsync -av /var/log/. /home/recovery/logs/
```

![rsync_utility_to_backup_files_in_the_log](./images/rsync_utility_to_backup_files_in_the_log.png)

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

Install php and all its dependencies

```bash
sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm

sudo yum module list php sudo yum module reset php

sudo yum module enable php:remi-7.4

sudo yum install php php-opcache php-gd php-curl php-mysqlnd

sudo systemctl start php-fpm

sudo systemctl enable php-fpm setsebool -P httpd_execmem 1
```

![install_httpd_php_php-mysqlnd_php-fpm_php-json](https://github.com/user-attachments/assets/ab0e1d05-1ffd-46bf-8e34-53c9d678d7d1)

Restart Apache

```bash
sudo systemctl restart httpd
```

![httpd_status](httpd_status.png)

Create an info.php page to test if your configuration is correct

```bash
sudo vi /var/www/html/info.php
```

write the following code to check php config

```bash
<?php
phpinfo();
?>
```

Visit your IPaddress/info.php
![info_php](info_php.png)
![alt text](access_to_redhat_locally_via_instance.png)

### 2. Download and Configure WordPress

```bash
wget http://wordpress.org/latest.tar.gz
tar xzvf latest.tar.gz
sudo cp -R wordpress/* /var/www/html/
sudo chown -R apache:apache /var/www/html/
```

### 3. Configure SELinux and Firewall for Apache

```bash
sudo setsebool -P httpd_can_network_connect=1
sudo firewall-cmd --zone=public --permanent --add-service=http
sudo firewall-cmd --reload
```

### 4. Finalize WordPress Installation

- Go to `http://<web-server-ip>/wordpress` to complete the WordPress setup in the browser.

![install_wordpress](install_wordpress.png)

Site title for the site.
Username: \***\*
Password: \*\***
Your Email: \*\*\*\*
Discourage search engines from indexing this site:

![wordpress_has_been_install](wordpress_has_been_install.png)

![wordpress_login](wordpress_login.png)

---

Congratulations! You have successfully deployed a WordPress website with a MySQL backend on AWS. Make sure to properly secure your instances and regularly backup your data.

![welcome_to_wordpress](welcome_to_wordpress.png)
