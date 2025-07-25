---
categories:
- 默认分类
date: '2025-03-15T10:50:22'
description: ''
draft: false
image: ''
slug: ubuntu-ssl
tags:
- ubuntu
- https
- ssl
title: ubuntu部署ssl免费证书
---

## 证书安装

在进行证书安装前，需要将域名的 DNS 指定到你的 ubuntu 服务器节点上，ubuntu 安装 Let’s Encrypt 证书步骤如下

安装 certbot 工具

```shell
apt update
apt install certbot python3-certbot-nginx
```

运行 certbot 命令进行证书安装

对于 nginx

```shell
certbot --nginx -d mydomain.com -d www.mydomain.com
```

对于 apache

```shell
certbot --apache -d mydomain.com -d www.mydomain.com
```

如果不填写邮箱的话，可以指定 certbot 参数 --register-unsafely-without-email，生成证书的输出如下

```shell
root@ubuntu:~# certbot --nginx -d mydomain.com -d www.mydomain.com --register-unsafely-without-email 
Saving debug log to /var/log/letsencrypt/letsencrypt.log

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Please read the Terms of Service at
https://letsencrypt.org/documents/LE-SA-v1.5-February-24-2025.pdf. You must
agree in order to register with the ACME server. Do you agree?
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o: 
(Y)es/(N)o: y
Account registered.
Requesting a certificate for mydomain.com and www.mydomain.com

Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/mydomain.com/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/mydomain.com/privkey.pem
This certificate expires on 2025-06-13.
These files will be updated when the certificate renews.
Certbot has set up a scheduled task to automatically renew this certificate in the background.

Deploying certificate
Successfully deployed certificate for mydomain.com to /etc/nginx/nginx.conf
Successfully deployed certificate for www.mydomain.com to /etc/nginx/nginx.conf
Congratulations! You have successfully enabled HTTPS on https://mydomain.com and https://www.mydomain.com

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
If you like Certbot, please consider supporting our work by:
 * Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
 * Donating to EFF:                    https://eff.org/donate-le
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
```


## 自动续订证书

Let's Encrypt 证书的有效期为 90 天，需要定期续订避免过期，Certbot 会自动设置一个系统定时器，以自动续订证书

可以使用以下命令来测试证书续订

```shell
certbot renew --dry-run
```

输出信息如下

```shell
root@ubuntu:~# certbot renew --dry-run 
Saving debug log to /var/log/letsencrypt/letsencrypt.log

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Processing /etc/letsencrypt/renewal/mydomain.com.conf
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Simulating renewal of an existing certificate for mydomain.com and www.mydomain.com

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Congratulations, all simulated renewals succeeded: 
  /etc/letsencrypt/live/mydomain.com/fullchain.pem (success)
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
```

