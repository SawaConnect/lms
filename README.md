### lms

### How to install moodle in ubuntu 18.04

###  The software used in this guide are:

  - OS: Ubuntu 18.04
  - Web server: Nginx 1.14
  - PHP Engine: PHP v7.2
  - Database: MariaDB 10.1
  - Moodle: Moodle v3.8
  - Subomain: lms.sawaconnect.com
  - SSL: Let’s Encrypt

---

## 1. Install Nginx

`apt update`
`apt upgrade -y`
`apt install nginx -y`

---

## 2. Install MariaDB

#### Install MariaDB database:

`apt install mariadb-server -y`

#### Secure MariaDB installation:

`mysql_secure_installation`

#### Edit MariaDB configuration:

`nano /etc/mysql/mariadb.conf.d/50-server.cnf`


#### Add the following configuration under [mysqld]:

  `default_storage_engine = innodb`
  `innodb_file_per_table = 1`
  `innodb_file_format = Barracuda`
  `innodb_large_prefix = 1`

#### Restart MariaDB:

  `systemctl restart mariadb`
  `systemctl status mariadb`

#### Login to MariaDB:

  `mysql -u root -p`

#### Creating a database for Moodle:

  `CREATE DATABASE moodle DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;`
  `CREATE USER 'usrmoodle'@'localhost' IDENTIFIED BY 'secret';`
  `GRANT ALL PRIVILEGES ON moodle.* TO 'usrmoodle'@'localhost';`
  `FLUSH PRIVILEGES;`
  `exit`

---

### 3. Install PHP-FPM

`apt install php-fpm php-common php-pspell php-curl php-gd php-intl php-mysql php-xml php-xmlrpc php-ldap php-zip php-soap php-mbstring -y`

#### php.ini configuration:

`nano /etc/php/7.2/fpm/php.ini`


#### Change the php.ini configuration options as follow:

  `memory_limit = 256M`
  `upload_max_filesize = 64M`
  `post_max_size = 64M`
  `max_execution_time = 360`
  `max_input_time = 360`
  `cgi.fix_pathinfo = 0`
  `date.timezone = Asia/Dubai`
  
#### Restart PHP-FPM:

  `systemctl restart php7.2-fpm`
  `systemctl status php7.2-fpm`
  
 
---

### 4. Download Moodle

#### Download Moodle v3.10:

  `wget https://download.moodle.org/download.php/direct/stable310/moodle-3.10.tgz`

  `tar xzvf moodle-3.10.tgz`

#### Create folders for Moodle:

  `mkdir -p /var/www/moodle/data`
  
  `mv moodle /var/www/moodle/web`
  
  `chown -R www-data:www-data /var/www/moodle`
  
  `chmod -R 755 /var/www/moodle`

#### Configure the Nginx server block for lms.sawaconnect.com

  `cd /etc/nginx/conf.d`

  `nano lms.sawaconnect.com.conf`	


Add the configuration below:
  
  server {
    listen 80;
    server_name lms.sawaconnect.com;
    root /var/www/moodle/web;
    index index.php index.html index.htm;
 
    location / {
    	try_files $uri $uri/ /index.php?$query_string;       
    }
 
    location /dataroot/ {
    	internal;
    	alias /var/www/moodle/data;
    }
 
    location ~ [^/]\.php(/|$) {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.2-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
 
   }	

#### Test and restart Nginx:
        ` nginx -t `
        ` systemctl restart nginx	`
        ` systemctl status nginx `

---

### 5. Install SSL Let’s Encrypt

#### Add certbot repository:

    ` apt install software-properties-common -y `
    ` add-apt-repository universe `
    ` add-apt-repository ppa:certbot/certbot `
    ` apt update `
 
#### Install certbot for Nginx:
  
  ` apt install certbot python3-certbot-nginx -y `

### Request SSL for the subdomain lms.sawaconnect.com:

certbot --nginx -d lms.sawaconnect.com



sudo mariabackup --backup --user=root --password=NAHla@102 --target-dir=/root/dbBackup/preupgrade_backup

sudo mariabackup --prepare --target-dir=/root/dbBackup/preupgrade_backup

  













