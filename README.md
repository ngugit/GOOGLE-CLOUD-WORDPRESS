//使用谷歌云GOOGLE CLOUD 搭配WORDPRESS网站方法
要点：
1、开启实例：在市场找到wordpress，一键部署，选小型的费用低的先测试着玩，保存好相关有IP和账号密码之类的。
2、设置静态IP: 路径网络->vpc网络->外部IP
3、域名绑定主机： 开启云端的Cloud DNS API->创建DNS ZONE，添加记录（A，CNAME)，copy自动生成NS记录，paste到域名服务商修改服务器地址。
 在WordPress后台， Settings > General. change the URL fields from your IP address to the domain name.
 
4、免费证书
1)Connect to WordPress via SSH
2)Install CertBot Client

wget https://dl.eff.org/certbot-auto && chmod a+x certbot-auto

3)Generate Certificates

./certbot-auto certonly --webroot -w /var/www/html/ -d 你的域名.com -d www.你的域名.com

4）Configure the Certificates

sudo nano /etc/apache2/sites-available/default-ssl.conf

打开后，改两个地方：
地方一：
开头localhost后面加上：

<Directory /var/www/html/>
Options Indexes FollowSymLinks MultiViews
AllowOverride All
Order allow,deny
allow from all
</Directory>

地方二：
在server certificate chain 上面加三行：

SSLCertificateFile "/etc/letsencrypt/live/你的域名.com/cert.pem"
SSLCertificateKeyFile "/etc/letsencrypt/live/你的域名.com/privkey.pem"
SSLCertificateChainFile "/etc/letsencrypt/live/你的域名.com/chain.pem"


然后，ctrl+o to save changes, followed by ctrl+x to exit 

5)Enable HTTPS Redirect

configure your Apache server to only serve the HTTPS version of your website. 

sudo nano /etc/apache2/sites-available/wordpress.conf

打开后，内容换为

<VirtualHost *:80>
ServerAdmin webmaster@localhost
DocumentRoot /var/www/html

ServerName www.你的域名.com
ServerAlias 你的域名.com
Redirect permanent / https://www.你的域名.com/

<Directory />
Options FollowSymLinks
AllowOverride None
</Directory>
<Directory /var/www/html/>
Options Indexes FollowSymLinks MultiViews
AllowOverride All
Order allow,deny
allow from all
</Directory>
ScriptAlias /cgi-bin/ /usr/lib/cgi-bin/
<Directory "/usr/lib/cgi-bin">
AllowOverride None
Options +ExecCGI -MultiViews +SymLinksIfOwnerMatch
Order allow,deny
Allow from all
</Directory>
ErrorLog ${APACHE_LOG_DIR}/error.log
# Possible values include: debug, info, notice, warn, error, crit,
# alert, emerg.
LogLevel warn
CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

保存退出

Free SSL Certificate Setup for WordPress on Google Cloud (Click-to-Deploy)
JANUARY 21, 2019 LERON AMIN 355 COMMENTS

Facebook2TweetLinkedInGoogle+Email

In this quick-start tutorial you will learn how to configure free, auto-renewing SSL certificates for WordPress websites that are hosted on Google Cloud compute engine using the Click-to-Deploy version of WordPress on Google Cloud.

Having SSL Security on your website will improve your Google search ranking.

If you’re using the Bitnami version of WordPress on Google Cloud, you can view that SSL certificate tutorial here.

Before getting started with this tutorial, you should have already:

Installed WordPress on Google Cloud
Set up a Domain Name for your WordPress website
Reserved a Static IP Address for your VM instance.
So let’s get started…

There are 8 steps in this tutorial:
1. Connect to WordPress via SSH

2. Install Certbot Client

3. Generate Certificates

4. Configure the Certificates

5. Enable HTTPS Redirect

6. Restart Apache Server

7. Update WordPress URLs

8. Configure SSL Auto-Renewal


1. Connect to WordPress via SSH
ssl certificates wordpress on google cloud access menu
Go to your Google Compute homepage and click the hamburger menu in the upper left-hand corner.
ssl certificate for wordpress on google cloud go to compute engine then vm instances
Go to your Compute Engine, then to VM instances to access your WordPress installation.

Click the SSH button to connect to your website’s server.

2. Install CertBot Client
ssl certificate setup wordpress google cloud click to deploy
Now that you’ve connected to your website’s Apache server, you are going to execute the command to install the Certbot client. The Certbot client is used to issue the SSL certificates that will be used later on in this tutorial.
wget https://dl.eff.org/certbot-auto && chmod a+x certbot-auto
After executing each of these commands, you can either stay in the same SSH window or exit and open a new one.


3. Generate Certificates
ssl certificate setup wordpress google cloud click to deploy
In this step you are going to execute the command below in order to tell the Certbot client to create SSL certificates for your website. Remember to replace 1pagezen.com with your own domain name.
./certbot-auto certonly --webroot -w /var/www/html/ -d 1pagezen.com -d www.1pagezen.com
ssl certificate setup wordpress google cloud click to deploy
After executing these commands, your certificates will be generated. Take note of the directory where your certificates are installed.

4. Configure the Certificates
ssl certificate setup wordpress google cloud click to deploy
The next step is to configure your certificates. First, execute the command below to open your default-ssl.conf file for editing.
sudo nano /etc/apache2/sites-available/default-ssl.conf

ssl certificate setup wordpress google cloud platform compute engine click-to-deploy
At the top of the default-ssl.conf file, paste the following lines of code in order to tell your server to direct network traffic to HTTPS port 443.
<Directory /var/www/html/>
Options Indexes FollowSymLinks MultiViews
AllowOverride All
Order allow,deny
allow from all
</Directory>
ssl certificate setup wordpress google cloud click to deploy
The next step is to use the down-arrow key to scroll down towards the bottom of the default-ssl.conf file. Place a # sign next to the existing snakeoil certificates, and paste the path to your three SSL certificate files as shown in the command below, making sure to replace 1pagezen.com with your own domain name.
SSLCertificateFile "/etc/letsencrypt/live/1pagezen.com/cert.pem"
SSLCertificateKeyFile "/etc/letsencrypt/live/1pagezen.com/privkey.pem"
SSLCertificateChainFile "/etc/letsencrypt/live/1pagezen.com/chain.pem"
After adding this code, enter ctrl+o to save changes, followed by ctrl+x to exit back to your home screen.


5. Enable HTTPS Redirect
ssl certificate setup wordpress google cloud click to deploy
The next thing you will do is configure your Apache server to only serve the HTTPS version of your website. To do this, you will edit your wordpress.conf file, which you can access by executing the command below.
sudo nano /etc/apache2/sites-available/wordpress.conf
wordpress conf file configurations
Inside of the wordpress.conf file, delete the existing 3 lines of code at the top of the file. Then, copy and paste the code below into the file - remembering to replace 1pagezen.com with your own domain name.
<VirtualHost *:80>
ServerAdmin webmaster@localhost
DocumentRoot /var/www/html

ServerName www.1pagezen.com
ServerAlias 1pagezen.com
Redirect permanent / https://www.1pagezen.com/

<Directory />
Options FollowSymLinks
AllowOverride None
</Directory>
<Directory /var/www/html/>
Options Indexes FollowSymLinks MultiViews
AllowOverride All
Order allow,deny
allow from all
</Directory>
ScriptAlias /cgi-bin/ /usr/lib/cgi-bin/
<Directory "/usr/lib/cgi-bin">
AllowOverride None
Options +ExecCGI -MultiViews +SymLinksIfOwnerMatch
Order allow,deny
Allow from all
</Directory>
ErrorLog ${APACHE_LOG_DIR}/error.log
# Possible values include: debug, info, notice, warn, error, crit,
# alert, emerg.
LogLevel warn
CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
After adding the code to your wordpress.conf file, enter ctrl+o to save your changes and ctrl+x to exit the wordpress.conf file.


6） Restart the Apache Server

分别运行
sudo a2ensite default-ssl
sudo a2enmod ssl
sudo service apache2 restart

7）Free SSL Certificate Setup for WordPress on Google Cloud (Click-to-Deploy)
JANUARY 21, 2019 LERON AMIN 355 COMMENTS

Facebook2TweetLinkedInGoogle+Email

In this quick-start tutorial you will learn how to configure free, auto-renewing SSL certificates for WordPress websites that are hosted on Google Cloud compute engine using the Click-to-Deploy version of WordPress on Google Cloud.

Having SSL Security on your website will improve your Google search ranking.

If you’re using the Bitnami version of WordPress on Google Cloud, you can view that SSL certificate tutorial here.

Before getting started with this tutorial, you should have already:

Installed WordPress on Google Cloud
Set up a Domain Name for your WordPress website
Reserved a Static IP Address for your VM instance.
So let’s get started…

There are 8 steps in this tutorial:
1. Connect to WordPress via SSH

2. Install Certbot Client

3. Generate Certificates

4. Configure the Certificates

5. Enable HTTPS Redirect

6. Restart Apache Server

7. Update WordPress URLs

8. Configure SSL Auto-Renewal


1. Connect to WordPress via SSH
ssl certificates wordpress on google cloud access menu
Go to your Google Compute homepage and click the hamburger menu in the upper left-hand corner.
ssl certificate for wordpress on google cloud go to compute engine then vm instances
Go to your Compute Engine, then to VM instances to access your WordPress installation.

Click the SSH button to connect to your website’s server.

2. Install CertBot Client
ssl certificate setup wordpress google cloud click to deploy
Now that you’ve connected to your website’s Apache server, you are going to execute the command to install the Certbot client. The Certbot client is used to issue the SSL certificates that will be used later on in this tutorial.
wget https://dl.eff.org/certbot-auto && chmod a+x certbot-auto
After executing each of these commands, you can either stay in the same SSH window or exit and open a new one.


3. Generate Certificates
ssl certificate setup wordpress google cloud click to deploy
In this step you are going to execute the command below in order to tell the Certbot client to create SSL certificates for your website. Remember to replace 1pagezen.com with your own domain name.
./certbot-auto certonly --webroot -w /var/www/html/ -d 1pagezen.com -d www.1pagezen.com
ssl certificate setup wordpress google cloud click to deploy
After executing these commands, your certificates will be generated. Take note of the directory where your certificates are installed.

4. Configure the Certificates
ssl certificate setup wordpress google cloud click to deploy
The next step is to configure your certificates. First, execute the command below to open your default-ssl.conf file for editing.
sudo nano /etc/apache2/sites-available/default-ssl.conf

ssl certificate setup wordpress google cloud platform compute engine click-to-deploy
At the top of the default-ssl.conf file, paste the following lines of code in order to tell your server to direct network traffic to HTTPS port 443.
<Directory /var/www/html/>
Options Indexes FollowSymLinks MultiViews
AllowOverride All
Order allow,deny
allow from all
</Directory>
ssl certificate setup wordpress google cloud click to deploy
The next step is to use the down-arrow key to scroll down towards the bottom of the default-ssl.conf file. Place a # sign next to the existing snakeoil certificates, and paste the path to your three SSL certificate files as shown in the command below, making sure to replace 1pagezen.com with your own domain name.
SSLCertificateFile "/etc/letsencrypt/live/1pagezen.com/cert.pem"
SSLCertificateKeyFile "/etc/letsencrypt/live/1pagezen.com/privkey.pem"
SSLCertificateChainFile "/etc/letsencrypt/live/1pagezen.com/chain.pem"
After adding this code, enter ctrl+o to save changes, followed by ctrl+x to exit back to your home screen.


5. Enable HTTPS Redirect
ssl certificate setup wordpress google cloud click to deploy
The next thing you will do is configure your Apache server to only serve the HTTPS version of your website. To do this, you will edit your wordpress.conf file, which you can access by executing the command below.
sudo nano /etc/apache2/sites-available/wordpress.conf
wordpress conf file configurations
Inside of the wordpress.conf file, delete the existing 3 lines of code at the top of the file. Then, copy and paste the code below into the file - remembering to replace 1pagezen.com with your own domain name.
<VirtualHost *:80>
ServerAdmin webmaster@localhost
DocumentRoot /var/www/html

ServerName www.1pagezen.com
ServerAlias 1pagezen.com
Redirect permanent / https://www.1pagezen.com/

<Directory />
Options FollowSymLinks
AllowOverride None
</Directory>
<Directory /var/www/html/>
Options Indexes FollowSymLinks MultiViews
AllowOverride All
Order allow,deny
allow from all
</Directory>
ScriptAlias /cgi-bin/ /usr/lib/cgi-bin/
<Directory "/usr/lib/cgi-bin">
AllowOverride None
Options +ExecCGI -MultiViews +SymLinksIfOwnerMatch
Order allow,deny
Allow from all
</Directory>
ErrorLog ${APACHE_LOG_DIR}/error.log
# Possible values include: debug, info, notice, warn, error, crit,
# alert, emerg.
LogLevel warn
CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
After adding the code to your wordpress.conf file, enter ctrl+o to save your changes and ctrl+x to exit the wordpress.conf file.


6. Restart the Apache Server
ssl certificate setup wordpress google cloud platform compute engine bitnami
In order for your certificate changes to take effect, you need to update/restart you Apache server by executing all three of the commands listed below.
sudo a2ensite default-ssl
sudo a2enmod ssl
sudo service apache2 restart


7. Update WordPress URLs

在setting 里将http改为https
