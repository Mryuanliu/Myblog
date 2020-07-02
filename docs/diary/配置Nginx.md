# windows环境下使用nginx配置HTTPS

#### 前言

因为自己想做一个关于微信小程序的项目，所以需要配置一个HTTPS的服务器。


#### 开始
* 购买域名和服务器，以阿里云为例子。因为我之前已经购买过了，云服务器使用的是window系统。（后面会再搞个linux系统的）。至于这个购买，解析域名，备案这个过程，就全部跳过了。


* 申请SSL证书。

![](https://user-gold-cdn.xitu.io/2020/5/4/171defdf9e5a73b1?w=1302&h=722&f=png&s=105235)

* 紧接着，提交一些自己的个人信息，审核大概就一个小时就能发放证书，会通过邮件方式提示给你。

![](https://user-gold-cdn.xitu.io/2020/5/4/171deff31ab56bdd?w=1037&h=150&f=png&s=17180)

* 将SSL的证书下载下来，是个压缩包。里面放着后缀名为.key和.pem的文件。证书文件：以.pem为后缀或文件类型。密钥文件：以.key为后缀或文件类型。

* 下载Nginx，在远程服务器上解压。
![](https://user-gold-cdn.xitu.io/2020/5/4/171df0f848bfe3f9?w=1213&h=418&f=png&s=40079)
```
在CMD下 cd /nginx.exe所在目录   start nginx 
```
* 访问服务器域名，当你看到这个页面的时候说明你安装成功了。
![](https://user-gold-cdn.xitu.io/2020/5/4/171df17cb034307d?w=951&h=360&f=png&s=48733)
* 在conf文件夹下，新建一个cert目录，将下载下来的证书放在cert目录下。
![](https://user-gold-cdn.xitu.io/2020/5/4/171df11d770f9a33?w=592&h=132&f=png&s=8579)
* 修改一下nginx.conf文件中的配置。

```
# 以下属性中以ssl开头的属性代表与证书配置有关，其他属性请根据自己的需要进行配置。
server {
listen 443 ssl;   #SSL协议访问端口号为443。此处如未添加ssl，可能会造成Nginx无法启动。
server_name localhost;  #将localhost修改为您证书绑定的域名，例如：www.example.com。
root html;
index index.html index.htm;
ssl_certificate cert/domain name.pem;   #将domain name.pem替换成您证书的文件名。
ssl_certificate_key cert/domain name.key;   #将domain name.key替换成您证书的密钥文件名。
ssl_session_timeout 5m;
ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;  #使用此加密套件。
ssl_protocols TLSv1 TLSv1.1 TLSv1.2;   #使用该协议进行配置。
ssl_prefer_server_ciphers on;   
location / {
root html;   #站点目录。
index index.html index.htm;   
}
}  

```

* 最后执行下面两个命令
```
nginx -s stop
start nginx
```



* 最后如果网页地址栏出现小锁标志，表示证书安装成功。

后期改进：
* 将云服务器的操作系统换为Linux，重新部署一遍项目。
