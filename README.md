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


