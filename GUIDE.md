# 🚀 OwnCloud Setup on Ubuntu 24.04 LTS

This guide walks you through installing and configuring **OwnCloud** on **Ubuntu 24.04.3 LTS**, including the LAMP stack setup, database configuration, Apache integration, and optional test virtual hosts.  

---

## 1️⃣ Install the LAMP Stack

Adding propriate PPA to system:  

```bash
sudo apt update
sudo apt install software-properties-common
sudo LC_ALL=C.UTF-8 add-apt-repository ppa:ondrej/php    # Adding the DEB.SURY.ORG repository for PHP 7/4 installation
sudo apt update
```
Installing services^

```bash
sudo apt install apache2 mariadb-server libapache2-mod-php7.4    # Installing Apache2? MariaDB and dependencies
sudo apt install php7.4-gd php7.4-json php7.4-mysql php7.4-curl php7.4-mbstring php7.4-intl php7.4-mcrypt php-imagick php7.4-xml php7.4-zip    # Installing PHP 7.4 and modules
```

Verify services are running:  

```bash
sudo systemctl status apache2.service   # Check Apache web server status
sudo systemctl status mysql.service     # Check MySQL/MariaDB database status
```

---

## 2️⃣ Configure MySQL for OwnCloud

Log into MySQL:  

```bash
sudo mysql -u root -p   # Access the MySQL shell as root
```

Inside the MySQL shell, create a database and user for OwnCloud:  

```sql
CREATE DATABASE owncloud_db;   -- Create the OwnCloud database
CREATE USER 'ownclouduser'@'localhost' IDENTIFIED BY 'Password';  -- Create a new user
GRANT ALL PRIVILEGES ON owncloud_db.* TO 'ownclouduser'@'localhost'; -- Grant permissions
FLUSH PRIVILEGES;   -- Apply the changes
EXIT;   -- Exit the MySQL shell
```

---

## 3️⃣ Download and Install OwnCloud

Download the latest stable release:  

```bash
wget https://download.owncloud.com/server/stable/owncloud-complete-20250703.zip  # Get OwnCloud package
sudo apt update             # Update package list
sudo apt install unzip -y   # Install unzip utility
unzip owncloud-complete-20250703.zip   # Extract the OwnCloud archive
```

Move files into the Apache web root:  

```bash
sudo mv owncloud /var/www/html/   # Move OwnCloud into the web server directory
sudo chown -R www-data:www-data /var/www/html/owncloud   # Set ownership to Apache user
```

---

## 4️⃣ Configure Apache for OwnCloud

Create a new Apache configuration file:  

```bash
sudo nano /etc/apache2/sites-available/owncloud.conf   # Open a new config file for OwnCloud
```

Paste this configuration:  

```apache
<VirtualHost *:80>
    DocumentRoot /var/www/html/owncloud
    <Directory /var/www/html/owncloud>
        Options FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

Enable the new site and restart Apache:  

```bash
sudo a2ensite owncloud.conf    # Enable the OwnCloud site
sudo a2enmod env rewrite dir mime headers setenvif ssl    # Rewriting environment
sudo systemctl restart apache2.service   # Restart Apache to apply changes
```

👉 Optional: verify your Apache configuration is correct by checking this [gist](https://gist.github.com/Zeokat/3b5c1273a7da48e1ad94).  

---

## 5️⃣ Access OwnCloud

Find your server IP address:  

```bash
ip a   # Display network interfaces and IP addresses
```

In your browser, navigate to:  

```
http://<your_ip_address>/owncloud
```

Follow the setup wizard:  
- Create an **admin account**  
- Enter the database details (`owncloud_db`, `ownclouduser`, `Password`)  

---

## 6️⃣ (Optional) Create Additional Web Directories

You can also create new project directories for testing:  

```bash
sudo mkdir /var/www/kitpro1   # Create first project directory
sudo mkdir /var/www/kitpro2   # Create second project directory
```

---

## 7️⃣ Test Virtual Hosts with Simple HTML Pages

To confirm that Apache is serving content properly, we’ll create simple test pages in the two directories.  

### 🔹 kitpro1 Test Page  

```bash
sudo nano /var/www/kitpro1/index.html
```

Paste:  

```html
<html>
<head>
    <title>Welcome to your kitpro1!</title>
</head>
<body>
    <h1>Success! The kitpro1 virtual host is working!</h1>
</body>
</html>
```

### 🔹 kitpro2 Test Page  

```bash
sudo nano /var/www/kitpro2/index.html
```

Paste:  

```html
<html>
<head>
    <title>Welcome to your kitpro2!</title>
</head>
<body>
    <h1>Success! The kitpro2 virtual host is working!</h1>
</body>
</html>
```

---

### 🔹 Configure Virtual Hosts in Apache  

For **kitpro1**:  

```bash
sudo nano /etc/apache2/sites-available/kitpro1.conf
```

Paste:  

```apache
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/kitpro1
    ServerName kitpro1.local
    <Directory /var/www/kitpro1>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

For **kitpro2**:  

```bash
sudo nano /etc/apache2/sites-available/kitpro2.conf
```

Paste:  

```apache
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/kitpro2
    ServerName kitpro2.local
    <Directory /var/www/kitpro2>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

Enable and restart Apache:  

```bash
sudo a2ensite kitpro1.conf
sudo a2ensite kitpro2.conf
sudo systemctl restart apache2.service
```

---

### 🔹 Test in Browser  

Edit `/etc/hosts` so your system knows the local domain names:  

```bash
sudo nano /etc/hosts
```

Add:  

```
127.0.0.1   kitpro1.local
127.0.0.1   kitpro2.local
```

Open in browser:  

- `http://kitpro1.local` → shows **kitpro1 success page**  
- `http://kitpro2.local` → shows **kitpro2 success page**  

---

✅ Now your environment not only runs OwnCloud, but also demonstrates **custom Apache virtual hosts**.  
