# 🔐 Apache SSL Setup with IP to Domain Redirect on Ubuntu 24.04
This configuration sets up an Apache web server with SSL using a custom certificate and private key. It also ensures that any direct access via server IP is redirected to the domain name over HTTPS.

### Key Features:

* SSL enabled using custom .crt and .key files.

* All traffic to http and https via IP is redirected to https://yourdomain.com.

* Clean virtual host configuration for both domain and IP access.

* Tested on Ubuntu 24.04 with Apache 2.4.

### Files Included:

* yourdomain-ssl.conf: Apache virtual host for serving the domain over HTTPS.

* 000-default.conf: Default catch-all configuration to redirect IP access to the domain.

### 🛠 Step 1: Prerequisites

Ensure Apache and SSL module are installed and enabled:

```shell
sudo apt update
sudo apt install apache2
sudo a2enmod ssl
sudo systemctl restart apache2
```
### 📁 Step 2: Place Your SSL Files

Let’s say your files are:

* Certificate: yourdomain.crt

* Private Key: yourdomain.key

Place them in /etc/ssl/yourdomain/:

```shell
sudo mkdir -p /etc/ssl/yourdomain
sudo cp yourdomain.crt /etc/ssl/yourdomain/
sudo cp yourdomain.key /etc/ssl/yourdomain/
```

### 🧾 Step 3: Create or Edit Apache Virtual Hosts

Let’s say your domain is yourdomain.com.

```shell
sudo nano /etc/apache2/sites-available/yourdomain-ssl.conf
```
Paste this (change domain and paths accordingly):
```shell
<VirtualHost *:443>
    ServerName yourdomain.com
    ServerAlias www.yourdomain.com

    DocumentRoot /var/www/html

    SSLEngine on
    SSLCertificateFile /etc/ssl/yourdomain/yourdomain.crt
    SSLCertificateKeyFile /etc/ssl/yourdomain/yourdomain.key

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
Enable the site:

```shell
sudo a2ensite yourdomain-ssl.conf
```
### 🔁 Step 4: Redirect IP to Domain

💡 Create a catch-all virtual host to redirect IP access

```shell
<VirtualHost *:80>
    ServerName _default_
    Redirect permanent / https://yourdomain.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName _default_

    SSLEngine on
    SSLCertificateFile /etc/ssl/yourdomain/yourdomain.crt
    SSLCertificateKeyFile /etc/ssl/yourdomain/yourdomain.key

    Redirect permanent / https://yourdomain.com/
</VirtualHost>
``` 
You can also create a new file (e.g., default-ssl.conf) if you prefer to separate this.

### ✅ Step 5: Enable SSL and Restart Apache

```shell
sudo a2enmod rewrite
sudo a2enmod ssl
sudo systemctl reload apache2
sudo systemctl restart apache2
```
### 🧪 Step 6: Test It
Visit https://yourdomain.com — it should load securely.

Visit https://<your-ip> — it should redirect to https://yourdomain.com.



