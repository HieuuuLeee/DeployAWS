# GUIDE TO CREATING AND DEPLOYING WEB APP UP TO AWS VIRTUAL
#### LINK WEB APP: http://52.77.228.217/
## 1. CREATE AWS ACCOUNT
## 2. VERIFY ACCOUNT

## CONNECT AWS INSTANCE BY SSH KEY
```sh
ssh -i cong-nghe-moi.pem ubuntu@52.77.228.217
```

## INSTALL ENVIRONMENT FOR VIRTUAL MACHINE (UBUNTU) ON AWS:
#### Step 1: Install Apache2 HTTP Server
##### ICE HRM requires a webserver and the most popular webserver in use today is Apache2. So, go and install Apache2 on Ubuntu by running the commands below:
```sh
sudo apt update
sudo apt install apache2
```
##### Next, run the commands below to stop, start and enable Apache2 service to always start up with the server boots.
```sh
sudo systemctl stop apache2.service
sudo systemctl start apache2.service
sudo systemctl enable apache2.service
```
#### Step 2: Install MariaDB
##### ICE HRM also requires a database server.. and MariaDB database server is a great place to start. To install it run the commands below.
```sh
sudo apt-get install mariadb-server mariadb-client
```
##### After installing MariaDB, the commands below can be used to stop, start and enable MariaDB service to always start up when the server boots..

##### Run these on Ubuntu 20.04 LTS
```sh
sudo systemctl stop mariadb.service
sudo systemctl start mariadb.service
sudo systemctl enable mariadb.service
```

##### When prompted, answer the questions below by following the guide.

###### Enter current password for root (enter for none): Just press the Enter
###### Set root password? [Y/n]: Y
###### New password: Enter password
###### Re-enter new password: Repeat password
###### Remove anonymous users? [Y/n]: Y
###### Disallow root login remotely? [Y/n]: Y
###### Remove test database and access to it? [Y/n]:  Y
###### Reload privilege tables now? [Y/n]:  Y
##### Restart MariaDB server
```sh
sudo systemctl restart mysql.service
```
#### Step 3: Install PHP 7.2 and Related Modules
##### PHP 7.2 may not be available on Ubuntu default repositories… in order to install it, you will have to get it from third-party repositories.

##### Run the commands below to add the below third party repository to upgrade to PHP 7.2
```sh
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:ondrej/php
```
##### Then update and upgrade to PHP 7.2
```sh
sudo apt update
```
##### Run the commands below to install PHP 7.2 and related modules.
```sh
sudo apt install php7.2 php7.2-common php7.2-mbstring php7.2-xmlrpc php7.2-soap php7.2-gd php7.2-xml php7.2-intl php7.2-mysql php7.2-cli php7.2 php7.2-ldap php7.2-zip php7.2-curl
```
##### After install PHP, run the commands below to open FPM PHP default file.
```sh
sudo nano /etc/php/7.2/apache2/php.ini
```
##### Then make the change the following lines below in the file and save.
```sh
file_uploads = On
allow_url_fopen = On
memory_limit = 256M
upload_max_filesize = 100M
date.timezone = America/Chicago
```
#### Step 4: Create ICE HRM Database
##### Now that you’ve install all the packages that are required, continue below to start configuring the servers. First run the commands below to create ICE HRM database.

##### Run the commands below to logon to the database server. When prompted for a password, type the root password you created above.
```sh
sudo mysql -u root -p
```
##### Then create a database called icehrm

##### CREATE DATABASE icehrm;

##### Create a database user called icehrmuser with new password

##### CREATE USER 'icehrmuser'@'localhost' IDENTIFIED BY 'new_password_here';

##### Then grant the user full access to the database.

##### GRANT ALL ON icehrm.* TO 'icehrmuser'@'localhost' IDENTIFIED BY 'user_password_here' WITH GRANT OPTION;

##### Finally, save your changes and exit.

FLUSH PRIVILEGES;
EXIT;
Step 5: Download ICE HRM Latest Release
Next, visit ICE HRM site and download the latest version.

After downloading, run the commands below to extract the download file into Apache2 root directory.

cd /tmp && wget https://github.com/gamonoid/icehrm/releases/download/v23.0.0.OS/icehrm_v23.0.0.OS.zip
unzip icehrm_v23.0.0.OS.zip
sudo mv icehrm_v23.0.0.OS /var/www/html/icehrm
Then run the commands below to set the correct permissions for Concrete5 to function.

sudo chown -R www-data:www-data /var/www/html/icehrm/
sudo chmod -R 755 /var/www/html/icehrm/
Step 6: Configure Apache2
Finally, configure Apahce2 site configuration file for ICE HRM. This file will control how users access ICE HRM content. Run the commands below to create a new configuration file called icehrm.conf

sudo nano /etc/apache2/sites-available/icehrm.conf

Then copy and paste the content below into the file and save it. Replace the highlighted line with your own domain name and directory root location.

<VirtualHost *:80>
     ServerAdmin admin@example.com
     DocumentRoot /var/www/html/icehrm
     ServerName example.com
     ServerAlias www.example.com

     <Directory /var/www/html/icehrm/>
        Options +FollowSymlinks
        AllowOverride All
        Require all granted
     </Directory>

     ErrorLog ${APACHE_LOG_DIR}/error.log
     CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
Save the file and exit.

Step 7: Enable the ICE HRM
After configuring the VirtualHost above, enable it by running the commands below

sudo a2ensite icehrm.conf
sudo a2enmod rewrite
Step 8 : Restart Apache2
To load all the settings above, restart Apache2 by running the commands below.

sudo systemctl restart apache2.service

Then open your browser and browse to the server domain name followed by install. You should see Concrete5 setup wizard to complete. Please follow the wizard carefully.

http://example.com
Then follow the on-screen instruction and type in the database connection info you created above… then click Install Application…