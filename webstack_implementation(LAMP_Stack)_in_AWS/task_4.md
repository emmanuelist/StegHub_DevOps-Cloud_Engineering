# Step 4: Create a Virtual Host for the Website Using Apache

In this step, we will set up a virtual host for our website using Apache. The default directory for serving the Apache default page is `/var/www/html`. We'll create a new document directory for our project alongside the default one.

## Installation Steps

### 1. Create the Project Directory

First, create a directory for the project using the `mkdir` command:

```bash
sudo mkdir /var/www/projectlamp
```

### 2. Assign Directory Ownership

Next, assign ownership of the new directory to the current system user by using the `$USER` environment variable:

```bash
sudo chown -R $USER:$USER /var/www/projectlamp
```

### 3. Create the Virtual Host Configuration

Install `vim` if it's not already installed, and then create a new configuration file in Apache’s `sites-available` directory:

```bash
sudo vim /etc/apache2/sites-available/projectlamp.conf
```

Add the following basic configuration to the file:

```apache
<VirtualHost *:80>
    ServerName projectlamp
    ServerAlias www.projectlamp
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

### 4. Verify the New Configuration

Check that the new configuration file is listed in the `sites-available` directory:

```bash
sudo ls /etc/apache2/sites-available
```

You should see output similar to this:

```
000-default.conf  default-ssl.conf  projectlamp.conf
```

### 5. Enable the New Virtual Host

Enable the new virtual host with the following command:

```bash
sudo a2ensite projectlamp
```

### 6. Disable the Default Site

To prevent Apache’s default configuration from overwriting the virtual host, disable the default site:

```bash
sudo a2dissite 000-default
```

### 7. Check for Syntax Errors

Ensure that your configuration does not contain any syntax errors by running:

```bash
sudo apache2ctl configtest
```

### 8. Reload Apache

Reload Apache for the changes to take effect:

```bash
sudo systemctl reload apache2
```

### 9. Create an Index File

At this point, the new website is active, but the web root `/var/www/projectlamp` is still empty. Create an `index.html` file to test if the virtual host works as expected:

```bash
echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) | sudo tee /var/www/projectlamp/index.html
```

### 10. Access the Website

Open your web browser and navigate to your public IP address to access the website:

```
http://<YOUR_PUBLIC_IP>:80
```

### Conclusion

You have successfully created a virtual host for your website using Apache. The temporary `index.html` file serves as a landing page until you replace it with an `index.php` file. Remember to rename or remove the `index.html` file once you have set up your application, as it will take precedence over `index.php` by default.
