//2019-4-19
补充一下网站搭好的邮箱问题。邮件服务器如果用网易企业免费版，在GOOGLE上不太会设MX，提示记录无效。可以利用cloudflare,设置MX，搞定了。。

//使用谷歌云GOOGLE CLOUD 搭配WORDPRESS网站方法 </br>
要点：</br>
1、开启实例：在市场找到wordpress，一键部署，选小型的费用低的先测试着玩，保存好相关有IP和账号密码之类的。</br>
2、设置静态IP: 路径网络->vpc网络->外部IP</br>
3、域名绑定主机： 开启云端的Cloud DNS API->创建DNS ZONE，添加记录（A，CNAME)，copy自动生成NS记录，paste到域名服务商修改服务器地址。</br>
 在WordPress后台， Settings > General. change the URL fields from your IP address to the domain name.</br>
 4、免费证书 </br>
1)Connect to WordPress via SSH </br>
2)Install CertBot Client </br>

wget https://dl.eff.org/certbot-auto && chmod a+x certbot-auto

3)Generate Certificates </br>

./certbot-auto certonly --webroot -w /var/www/html/ -d 你的域名.com -d www.你的域名.com

4）Configure the Certificates </br>

sudo nano /etc/apache2/sites-available/default-ssl.conf
</br>

打开后，改两个地方：</br>
地方一：
开头localhost后面加上：</br>

<Directory /var/www/html/>
Options Indexes FollowSymLinks MultiViews
AllowOverride All
Order allow,deny
allow from all
</Directory>

地方二：</br>
在server certificate chain 上面加三行：

SSLCertificateFile "/etc/letsencrypt/live/你的域名.com/cert.pem"
SSLCertificateKeyFile "/etc/letsencrypt/live/你的域名.com/privkey.pem"
SSLCertificateChainFile "/etc/letsencrypt/live/你的域名.com/chain.pem"


然后，</br>
ctrl+o to save changes, followed by ctrl+x to exit 

5)Enable HTTPS Redirect

configure your Apache server to only serve the HTTPS version of your website. 

sudo nano /etc/apache2/sites-available/wordpress.conf

打开后，内容换为</br>

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

6） Restart the Apache Server

分别运行
sudo a2ensite default-ssl
sudo a2enmod ssl
sudo service apache2 restart

7)Update WordPress URLs

在setting 里将http改为https
