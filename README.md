//使用谷哥云GOOGLE CLOUD 搭配WORDPRESS网站方法
要点：
1、开启实例：在市场找到wordpress，一键部署，选小型的费用低的先测试着玩。
2、设置静态IP: 路径网络->vpc网络->外部IP
3、域名绑定主机： 开启云端的Cloud DNS API->创建DNS ZONE，添加记录（A，CNAME),把自动生成NS记录，到域名服务商修改服务器地址。
4、增加免费证书
