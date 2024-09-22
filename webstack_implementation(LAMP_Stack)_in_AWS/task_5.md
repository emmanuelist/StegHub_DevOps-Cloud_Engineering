# Step 5: Enable PHP on the Website

By default, the Apache `DirectoryIndex` setting prioritizes `index.html` over `index.php`. This behavior is useful for temporary maintenance pages, allowing you to create an informative `index.html` file for visitors. Once maintenance is complete, you can rename or remove the `index.html` file to restore normal functionality.

To change the priority and allow `index.php` to take precedence, follow these steps:

## Installation Steps

### 1. Edit the Directory Index Configuration

Open the `dir.conf` file to modify the `DirectoryIndex` directive:

```bash
sudo vim /etc/apache2/mods-enabled/dir.conf
```

Find the following line:

```apache
# DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
```

Change it to:

```apache
DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
```

### 2. Reload Apache

Reload Apache to apply the changes:

```bash
sudo systemctl reload apache2
```

### 3. Create a PHP Test Script

Next, create a PHP test script to verify that Apache can handle and process PHP files. Create a new `index.php` file in your custom web root folder:

```bash
vim /var/www/projectlamp/index.php
```

Add the following content to the `index.php` file:

```php
<?php
phpinfo();
```

### 4. Refresh the Page

After saving the file, refresh your web browser and navigate to your site:

```
http://<YOUR_PUBLIC_IP>:80
```

You should see the PHP information page, which provides details about the server configuration from the perspective of PHP. This page is useful for debugging and ensuring your PHP settings are correctly applied.

### 5. Remove the Test Script

After checking the relevant server information, it’s best to remove the `index.php` file, as it contains sensitive information about your PHP environment and server configuration. You can recreate it later if needed:

```bash
sudo rm /var/www/projectlamp/index.php
```

### Conclusion

You have successfully enabled PHP on your website and verified its functionality. By modifying the `DirectoryIndex` setting, you've ensured that `index.php` will take precedence over `index.html`, allowing for dynamic content to be served by your application.
