//使用谷歌云GOOGLE CLOUD 搭配WORDPRESS网站方法 </br>
要点：</br>
1、开启实例：在市场找到wordpress，一键部署</br>
2、设置静态IP: 路径网络->vpc网络->外部IP</br>
3、域名绑定主机： 开启云端的Cloud DNS API->创建DNS ZONE，添加记录（A，CNAME)，copy自动生成NS记录，paste到域名服务商修改服务器地址。</br>
 在WordPress后台， Settings > General. change the URL fields from your IP address to the domain name.</br>
4、免费证书  方法略，有最新的 </br>  
5、Update WordPress URLs，在setting 里将http改为https

////2019-4-19
补充一下网站搭好的邮箱问题。邮件服务器如果用网易企业免费版，在GOOGLE设MX，提示记录无效。可以利用cloudflare,设置MX
