# DevOps Tooling Website Solution

## Table of Contents

- Introduction
- [Project Overview](#project-overview)
- [Self-Study: Storage Concepts](#self-study-storage-concepts)
- Prerequisites
- [Architecture Overview](#architecture-overview)
- [Setting Up AWS EC2 Instances](#setting-up-aws-ec2-instances)
- [Configuring NFS Server](#configuring-nfs-server)
- [Configuring Database Server](#configuring-database-server)
- [Configuring Web Servers](#configuring-web-servers)
- [Deploying Tooling Website](#deploying-tooling-website)
- [Final Steps and Reflections](#final-steps-and-reflections)

## Introduction

## Project Overview

This project extends my previous work of deploying a scalable WordPress application using a 3-tier architecture on AWS. The enhancements include:

- **Centralized File Storage**: Introducing an NFS server for shared storage.
- **Multiple Web Servers**: Deploying multiple web servers connected to the NFS storage.
- **Dedicated Database Server**: Maintaining a separate database server.
- **DevOps Tooling Focus**: Shifting focus to hosting DevOps tools.
- **Enhanced Scalability**: Using NFS for flexible scaling.
- **Continued Use of LVM**: Leveraging LVM for dynamic storage management.

## Self-Study: Storage Concepts

### Network Storage Types

- **NAS**: File-level storage for easy file sharing.
- **SAN**: Block-level storage for high-performance applications.

### Data Storage Paradigms

- **Block Storage**: Suitable for OS and databases.
- **Object Storage**: Scalable for unstructured data.
- **Network File System (NFS)**: File-level protocol for remote file access.

### AWS Cloud Storage Services

- **Amazon EBS**: Block-level storage for EC2 instances.
- **Amazon S3**: Scalable object storage.
- **Amazon EFS**: Managed NFS for EC2.

## Prerequisites

- **AWS Account**: Permissions to create and manage EC2 instances, EBS volumes, and security groups.
- **Knowledge Base**: Basic Linux command line, AWS services, and networking concepts.
- **Tools**: SSH client, Git, and a modern web browser.
- **Security**: EC2 Key Pair for SSH access.

## Architecture Overview

This architecture provides:

- Scalability through multiple web servers.
- Centralized file storage via NFS.
- Separated database for better resource management.

## Setting Up AWS EC2 Instances

### Launch NFS Server

1. Navigate to EC2 dashboard in AWS Console.
2. Click "Launch Instance".
3. Choose "Red Hat Enterprise Linux 9.4" AMI.
4. Select t2.small instance type.
5. Configure instance details (VPC, subnet, etc.).
6. Add 3 x 15GB EBS volumes.
7. Configure security group.
8. Review and launch with your EC2 key pair.

![Launch_NFS_Server_instance](https://github.com/user-attachments/assets/04418586-edac-4143-8ad2-caec01a37018)

### Launch Web Servers (Repeat 3 times)

1. Follow similar steps as NFS server.
2. Choose t3.micro instance type.
3. No additional EBS volumes required.

### Launch Database Server

1. Follow similar steps as NFS server.
2. Choose t3.micro instance type.
3. Add 3 x 10GB EBS volumes.

### Verify Instances

1. Ensure all instances are in the "running" state.
2. Note down the private IP addresses of all instances.
3. Confirm that all instances are in the same subnet.

### Tag Instances

1. Add descriptive tags to each instance (e.g., "NFS Server", "Web Server 1", "DB Server").

### Configure Elastic IPs (Optional)

1. Allocate and associate Elastic IPs to web servers if public access is required.

## Configuring NFS Server

### EBS Setup

1. Check Volume Visibility:

   ```sh
   lsblk
   ```

   ![attached_volume](https://github.com/user-attachments/assets/9047225e-9962-4154-8e36-0ae8c7e8ad62)

2. Install LVM Tools:

   ```sh
   sudo dnf update
   sudo dnf install lvm2 nano
   ```

   ![install_lvm2](https://github.com/user-attachments/assets/d7e2edb6-2306-4e0f-9ff7-3a24b2a099f6)

3. Create partitions on EBS volumes:

   ```sh
   sudo gdisk /dev/nvme1n1
   sudo gdisk /dev/nvme2n1
   sudo gdisk /dev/nvme3n1
   ```

   ![first_volume_partition](https://github.com/user-attachments/assets/e62def04-22db-422e-8106-8619446bfbc3)

4. Create Physical Volumes:

   ```sh
   sudo pvcreate /dev/nvme1n1p1 /dev/nvme2n1p1 /dev/nvme3n1p1
   ```

   ![pvcreate_volume_for_use_in_a_Logical_volume_managerLVM](https://github.com/user-attachments/assets/8b716cd3-f36c-4db0-8057-beffc6e372ed)

5. Create Volume Group:

   ```sh
   sudo vgcreate webdata-vg /dev/nvme1n1p1 /dev/nvme2n1p1 /dev/nvme3n1p1
   ```

   ![create_new_volume_group](https://github.com/user-attachments/assets/0f96a98b-00e1-4342-a8f1-77decd3d4a4e)

6. Create Logical Volumes:

   ```sh
   sudo lvcreate -n lv-apps -L 14G webdata-vg
   sudo lvcreate -n lv-logs -L 14G webdata-vg
   sudo lvcreate -n lv-opt -L 14G webdata-vg
   ```

   ![create_a_new_logical_volume_(LV)_within_an_existing_volume_group_VG ](https://github.com/user-attachments/assets/46932863-90b5-46c6-81b0-4329b8657d04)

7. Create File system and Mount points for the LVs:

   ```sh
   sudo mkfs -t xfs /dev/webdata-vg/lv-apps
   sudo mkfs -t xfs /dev/webdata-vg/lv-logs
   sudo mkfs -t xfs /dev/webdata-vg/lv-opt
   ```

   ![format_disk_as_xfs](https://github.com/user-attachments/assets/90779e65-c833-43f1-8b19-7059a694a0ce)

8. Create the following mount points:
   ```sh
   sudo mkdir /mnt/apps /mnt/logs /mnt/opt
   ```
   ![create_mount_point_to_be_used_by_webservers_logs](https://github.com/user-attachments/assets/10c81560-5683-46b8-8ff3-3c5d94f97f58)

### Install and Configure NFS server

1. Install NFS server:

   ```sh
   sudo dnf update -y
   sudo dnf install nfs-utils -y
   sudo systemctl start nfs-server
   sudo systemctl enable nfs-server
   sudo systemctl status nfs-server
   ```

2. Set permission to the mount points:

   ```sh
   sudo chown -R nobody: /mnt/apps
   sudo chown -R nobody: /mnt/logs
   sudo chown -R nobody: /mnt/opt
   sudo chmod -R 777 /mnt/apps
   sudo chmod -R 777 /mnt/logs
   sudo chmod -R 777 /mnt/opt
   sudo systemctl restart nfs-server
   ```

3. Configure access to NFS for clients within same subnet:

   ```sh
   sudo nano /etc/exports
   ```

   Add the following:

   ```
   /mnt/apps 172.31.0.0/20(rw,sync,no_all_squash,no_root_squash)
   /mnt/logs 172.31.0.0/20(rw,sync,no_all_squash,no_root_squash)
   /mnt/opt 172.31.0.0/20(rw,sync,no_all_squash,no_root_squash)
   ```

4. Export the NFS shares:

   ```sh
   sudo exportfs -arv
   ```

   ![exportfs_-arv](https://github.com/user-attachments/assets/d1016365-baa8-497e-88b8-6c05d60b5097)

5. Check the NFS server port:

   ```sh
   rpcinfo -p | grep nfs
   ```

   ![rpcinfo_-p_grep_nfs](https://github.com/user-attachments/assets/a6da9275-8f9f-48b4-a8e4-92a4b132e245)

6. Add the following ports to the security group for the nfs-server on AWS console:

   - TCP 111
   - TCP 2049
   - UDP 111
   - UDP 2049

   ![custom_security-group](https://github.com/user-attachments/assets/a782920e-92fd-4bbb-89aa-be94f2c43abd)

## Configuring Database Server

### EBS Setup

1. Check Volume Visibility:

   ```sh
   lsblk
   ```

   ![attached_volume](https://github.com/user-attachments/assets/9047225e-9962-4154-8e36-0ae8c7e8ad62)

2. Install LVM Tools:

   ```sh
   sudo dnf update
   sudo dnf install lvm2 nano
   ```

3. Create partitions on EBS volumes:

   ```sh
   sudo gdisk /dev/xvdb
   sudo gdisk /dev/nvme1n1
   sudo gdisk /dev/nvme3n1
   ```

4. Create Physical Volumes:

   ```sh
   sudo pvcreate /dev/xvdb1 /dev/nvme1n1p1 /dev/nvme3n1p1
   ```

5. Create Volume Group:

   ```sh
   sudo vgcreate dbdata-vg /dev/xvdb1 /dev/nvme1n1p1 /dev/nvme3n1p1
   ```

6. Create Logical Volumes:

   ```sh
   sudo lvcreate -n db-lv -L 14G dbdata-vg
   sudo lvcreate -n log-lv -L 14G dbdata-vg
   ```

7. Create File system and Mount LVs:

   ```sh
   sudo mkfs -t ext4 /dev/dbdata-vg/db-lv
   sudo mkfs -t ext4 /dev/dbdata-vg/log-lv
   ```

8. Mount the LVs:

   ```sh
   sudo mkdir -p /db /home/recovery/logs
   sudo rsync -av /var/log/ /home/recovery/logs/
   sudo mount /dev/dbdata-vg/db-lv /db
   sudo mount /dev/dbdata-vg/log-lv /var/log
   sudo rsync -av /home/recovery/logs/ /var/log/
   ```

9. Persistent Mounts:
   ```sh
   sudo blkid
   sudo nano /etc/fstab
   ```
   Add the following:
   ```
   UUID=ac925709-a000-4cb5-926d-bc9c792c499c /db ext4 defaults 0 0
   UUID=44d455e2-38ff-414e-839e-39110887379d /var/log ext4 defaults 0 0
   ```
   ```sh
   sudo systemctl daemon-reload
   sudo mount -a
   ```

### AWS Security Group Configuration

1. MySQL Security Group:
   - Allowed traffic on port 3306 from the subnet CIDR block (172.31.0.0/20).
   - Opened SSH (port 22) for administrative access.

### Install and Configure MySQL Database Server

1. Install MySQL server:

   ```sh
   sudo dnf update
   sudo dnf install mysql-server -y
   ```

2. Initial Configuration:

   ```sh
   sudo systemctl start mysqld
   sudo systemctl enable mysqld
   sudo mysql
   ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Password.1';
   FLUSH PRIVILEGES;
   EXIT;
   sudo mysql_secure_installation
   ```

3. Create admin user for the tooling application:

   ```sh
   sudo mysql -u root -p
   CREATE DATABASE tooling;
   CREATE USER 'webaccess'@'172.31.%' IDENTIFIED WITH mysql_native_password BY 'Password.1';
   GRANT ALL PRIVILEGES ON tooling.* TO 'webaccess'@'172.31.%';
   FLUSH PRIVILEGES;
   EXIT;
   ```

4. Test the remote connection to the newly created database via web-server instance:
   ```sh
   sudo dnf update
   sudo dnf install mysql nano
   sudo mysql -h 172.31.3.89 -u webaccess -p
   ```

## Configuring Web Servers

### Installing NFS Client

1. Install the NFS client and Apache on all the three web servers:
   ```sh
   sudo dnf install nfs-utils nfs4-acl-tools -y
   sudo dnf install httpd git
   sudo systemctl start httpd
   sudo systemctl enable httpd
   sudo systemctl status httpd
   ```

### Mounting NFS Shares

1. Mount /var/www/ and target the NFS server:
   ```sh
   sudo mount -t nfs -o rw,nosuid 172.31.1.101:/mnt/apps /var/www
   df -h
   sudo nano /etc/fstab
   ```
   Add the following:
   ```
   172.31.1.101:/mnt/apps /var/www nfs defaults 0 0
   ```
   ```sh
   sudo systemctl daemon-reload
   sudo mount -a
   ```

### Installing PHP and Extensions

1. Enable Necessary Repositories:

   ```sh
   sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm
   sudo dnf install https://rpms.remirepo.net/enterprise/remi-release-9.rpm
   sudo dnf module switch-to php:remi-8.3
   sudo dnf module install php:remi-8.3
   sudo dnf install php php-opcache php-gd php-curl php-mysqlnd php-xml php-json php-mbstring php-intl php-soap php-zip
   ```

2. Start and enable PHP-FPM:

   ```sh
   sudo systemctl start php-fpm
   sudo systemctl enable php-fpm
   ```

3. Configure SELinux:

   ```sh
   sudo setsebool -P httpd_execmem 1
   sudo setsebool -P httpd_can_network_connect 1
   sudo setsebool -P httpd_use_nfs on
   ```

4. Verify PHP Installation:
   ```sh
   php --version
   php -m
   sudo mkdir /var/www/html
   sudo nano /var/www/html/info.php
   ```
   Add the following:
   ```php
   <?php
   phpinfo();
   ?>
   ```
   ```sh
   sudo systemctl restart httpd
   ```

## Deploying Tooling Website

1. Clone the PHP-based application from GitHub:

   ```sh
   sudo git clone https://github.com/emmanuelist/tooling.git
   cd tooling
   sudo mysql -h 172.31.3.89 -u webaccess -p tooling < tooling-db.sql
   ```

2. Add a new admin user into the tooling database:

   ```sh
   sudo mysql -h 172.31.3.89 -u webaccess -p tooling
   INSERT INTO `users` (`username`, `password`, `email`, `user_type`, `status`)
   VALUES ('myuser', MD5('password'), 'user@mail.com', 'admin', '1');
   ```

3. Update the database connection info in the `function.php` file:

   ```sh
   sudo nano /var/www/html/function.php
   ```

   Update the following line:

   ```php
   $db = mysqli_connect('172.31.3.89', 'webaccess', 'Password.1', 'tooling');
   ```

4. Restart Apache:

   ```sh
   sudo systemctl restart httpd
   ```

5. Visit the browser to view the application:
   ```sh
   http://<public ip of any of the web servers>/index.php
   ```

## Final Steps and Reflections

I ran into some connection issues initially but was able to make the setup work without disabling SELinux, maintaining it as a security layer.

![login](https://github.com/user-attachments/assets/6235a508-23bc-4c4a-bd33-7aa7e1969d26)
![dashboard](https://github.com/user-attachments/assets/b0460c3c-57fc-4270-9edd-1cfc07929c93)
