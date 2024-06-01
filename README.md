# WEB STACK IMPLEMENTATION IN AWS

This project demonstrates the implementation of a LAMP (Linux, Apache, MySQL, PHP) stack on an AWS EC2 instance. The LAMP stack is a popular open-source web development platform that uses Linux as the operating system, Apache as the web server, MySQL as the relational database management system, and PHP as the scripting language.

## Table of Contents

- [AWS Account Setup and Provisioning an Ubuntu Server](#aws-account-setup-and-provisioning-an-ubuntu-server)
- [Installing Apache and Updating the Firewall](#installing-apache-and-updating-the-firewall)
- [Installing MySQL](#installing-mysql)
- [Installing PHP](#installing-php)
- [Creating a Virtual Host for Your Website Using Apache](#creating-a-virtual-host-for-your-website-using-apache)
- [Enabling PHP on the Website](#enabling-php-on-the-website)
- [Testing](#testing)
- [Contributing](#contributing)

## AWS Account Setup and Provisioning an Ubuntu Server

### Steps

1. Signed up for an AWS account.
2. Logged in as IAM user.
3. In the VPC console, created Security Group.
4. Launched an EC2 instance:
    - Selected the Ubuntu free tier instance.
    - Configured instance ( security group, key pair ).
    - Launched the instance.

5. SSH into the instance using Windows Terminal:
    - Navigated to the location of the key-pair:
        ```sh
        cd Downloads
        ```
    - Connected to the instance using its Public DNS:
        ```sh
        ssh -i your-key-pair.pem ubuntu@your-ec2-instance-public-dns
        ```

## Installing Apache and Updating the Firewall

### Steps

1. Update the package list and install Apache:
    ```sh
    sudo apt update
    sudo apt install apache2
    ```

2. Verify Apache2 is running as a service:
    ```sh
    sudo systemctl status apache2
    ```

3. Open port 80 on the Ubuntu instance to allow access from the internet.
4. Access the Apache2 service locally in the Ubuntu shell:
    ```sh
    curl http://localhost:80
    curl http://127.0.0.1:80
    ```

5. Test that Apache HTTP server can respond to requests from the Internet by opening a browser and typing the public IP of the Ubuntu instance:
    ```sh
    http://your-ec2-instance-public-dns:80
    ```

## Installing MySQL

### Steps

1. Install MySQL server:
    ```sh
    sudo apt install mysql-server
    ```

2. Log in to the MySQL console:
    ```sh
    sudo mysql
    ```

3. Run a security script to lock down access to your database system:
    ```sql
    ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Mahmoud';
    ```

4. Test that login to MySQL console works:
    ```sh
    sudo mysql -p
    ```

## Installing PHP

### Steps

1. Install PHP and necessary modules:
    ```sh
    sudo apt install php libapache2-mod-php php-mysql
    ```

2. Confirm PHP version:
    ```sh
    php -v
    ```

At this point, your LAMP stack is completely installed and fully operational.

## Creating a Virtual Host for Your Website Using Apache

### Steps

1. Create the directory for `projectlamp`:
    ```sh
    sudo mkdir /var/www/projectlamp
    sudo chown -R $USER:$USER /var/www/projectlamp
    ```

2. Create and open a new configuration file in Apache’s `sites-available` directory:
    ```sh
    sudo vi /etc/apache2/sites-available/projectlamp.conf
    ```

3. Add the following configuration:
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

4. Save and close the file.

5. Enable the new virtual host:
    ```sh
    sudo a2ensite projectlamp
    sudo a2dissite 000-default
    sudo apache2ctl configtest
    sudo systemctl reload apache2
    ```

6. Create an `index.html` file to test the virtual host:
    ```sh
    echo 'Hello LAMP from hostname $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) with public IP $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4)' | sudo tee /var/www/projectlamp/index.html
    ```

## Enabling PHP on the Website

### Steps

1. Edit the `/etc/apache2/mods-enabled/dir.conf` file:
    ```sh
    sudo vim /etc/apache2/mods-enabled/dir.conf
    ```

2. Change the order in which the `index.php` file is listed within the `DirectoryIndex` directive:
    ```apache
    DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
    ```

3. Reload Apache:
    ```sh
    sudo systemctl reload apache2
    ```

4. Create a PHP script to test that PHP is correctly installed:
    ```sh
    sudo vim /var/www/projectlamp/index.php
    ```

5. Add the following PHP code:
    ```php
    <?php
    phpinfo();
    ?>
    ```

6. Save and close the file, then refresh the page to see changes.

## Testing

To verify that your LAMP stack is working correctly, you can:

1. Access the PHP info page to confirm PHP is working:
    ```sh
    http://your-ec2-instance-public-dns/index.php
    ```

2. Test MySQL by logging in and creating a database:
    ```sh
    sudo mysql -u root -p
    CREATE DATABASE test_db;
    SHOW DATABASES;
    ```

3. Deploy a simple PHP application that connects to the MySQL database.

## Contributing

Contributions are welcome! Please fork this repository and submit a pull request for any enhancements or bug fixes.
