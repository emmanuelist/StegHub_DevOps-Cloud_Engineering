# LAMP Stack Implementation on AWS

The LAMP stack is a popular open-source software stack commonly used for building dynamic websites and web applications. LAMP stands for:

- **Linux**: The operating system that provides the foundation for the other components. Known for its stability, security, and flexibility.
- **Apache**: The web server software that processes incoming web requests and serves content to users' browsers.
- **MySQL**: A relational database management system (RDBMS) used for storing and managing dynamic content.
- **PHP** (or Perl/Python): The programming language used to develop the dynamic functionality of web applications.

## Step 0: Preparing Prerequisites

To complete this project, you need the following:

1. **AWS Account**: Ensure you have an AWS account set up and verified.
2. **Ubuntu OS**: Since you're using Windows, you will install an Ubuntu virtual server using Windows PowerShell.

### Installing Ubuntu on Windows

1. **Check Installed Versions**:
   Open PowerShell and run:

   ```bash
   wsl -v
   ```

   This command should return your installed images.

2. **Install Ubuntu** (if not already installed):
   If Ubuntu is not installed, use the following command:
   ```bash
   wsl --install -d Ubuntu-20.04
   ```

### Accessing AWS EC2

1. **Log in to AWS Management Console**:
   Navigate to the EC2 service by clicking on **Services** > **Compute**.

2. **Create a Key Pair**:
   Before launching an instance, create a key pair that will allow you to securely connect to your instance.

3. **Launch an EC2 Instance**:
   - Go to the EC2 dashboard and click on the **Launch Instance** button.
   - Select **Ubuntu 20.04** as your Amazon Machine Image (AMI).
   - Follow the prompts to configure your instance, and make sure to note the public DNS or IP address for future connections.
   - Download your `.pem` key file.

### Connecting to Your EC2 Instance

1. **Open PowerShell** and navigate to your downloads folder:

   ```bash
   cd Downloads
   ```

2. **Change Permissions for the Key Pair**:
   If you encounter a "permission denied" error, run:

   ```bash
   chmod 400 StegHubLAMP.pem
   ```

3. **Connect to Your Instance**:
   Use the following command to connect (replace `<your-public-dns>` with your instance's public DNS):
   ```bash
   ssh -i "StegHubLAMP.pem" ubuntu@<your-public-dns>
   ```

### Configuring Security Group Rules

If you have issues with connecting via SSH, ensure that your security group allows inbound traffic:

1. **Navigate to Security Groups**:
   In the EC2 dashboard, click on **Security Groups** in the left-hand navigation pane.

2. **Edit Inbound Rules**:

   - Select the security group associated with your instance.
   - In the **Inbound** tab, click on **Edit inbound rules**.
   - Add a new rule for SSH:
     - **Type**: SSH
     - **Source**: Select **My IP** or specify a custom IP range.

3. **Save Changes**.

### Troubleshooting Connection Issues

If you experience difficulties with EC2 Instance Connect, consider the following:

- **Verify Security Group Rules**: Ensure inbound access on port 22 (SSH) is allowed.
- **Confirm Region Compatibility**: Check that your EC2 instance's region matches the EC2 Instance Connect service IP addresses.
- **Check VPC and Subnet Configuration**: Make sure your instance is in a VPC and subnet that allows traffic.
- **Verify Instance Status**: Ensure your EC2 instance is running and accessible.

### Conclusion

Congratulations! You have successfully set up your first Linux server in the cloud. Your current setup allows you to proceed with installing the LAMP stack and developing dynamic web applications.

### Next Steps

Now that your EC2 instance is up and running, you can proceed with installing the LAMP stack components (Apache, MySQL, and PHP) on your Ubuntu server to start building your web application.
