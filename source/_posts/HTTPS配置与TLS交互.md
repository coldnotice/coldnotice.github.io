---
title: HTTPS配置与TLS交互
date: 2019-3-25 22:05:07
tags: 网络
---

https与tls相关

<!-- more -->

------------------------------------------------------------------------------------------------------------------------------------------


# 一. 配置HTTPS

## 自建 CA

1. ca私钥：`openssl genrsa -out /etc/pki/CA/private/cakey.pem 2048`
2. 自签证书：`openssl req -new -x509 -key /etc/pki/CA/private/cakey.pem -out /etc/pki/CA/cacert.pem -days 3650`
3. 辅助文件：
   `touch /etc/pki/CA/index.txt`
   `echo 01>/etc/pki/CA/serial`

## 申请证书

- 证书关键数据字段：有效期；要连接的主机名
- countryName/stateOrProvinceName/organizationName 三个字段必须与CA自签证书字段相同
- commonName字段必须提供，用于确定是否在与正确主机通信
- validity有效期字段

1. 私钥：`openssl genrsa -out /etc/pki/tls/private/httpstest.key 2048`
2. 证书签名请求：`openssl req -new -key /etc/pki/tls/private/httpstest.key -days 365 -out /etc/pki/tls/httpstest.csr`
3. 将csr传给CA: `scp /etc/pki/tls/httpstest.csr root@172.16.111.100:/tmp/` 
4. CA进行证书签名：`openssl ca -in /tmp/httpstest.csr -out/etc/pki/CA/certs/httpstest.crt -days 365`
5. 将签名证书发回服务器主机

## 配置 https

1. 私钥位置：/etc/pki/tls/private/httpstest.key
2. 证书位置：/etc/pki/tls/certs/httpstest.crt
3. 配置文件：/etc/httpd/conf.d/ssl.conf
4. 几个参数：
   DocumentRoot：网站根目录
   ServerName：主机名
   SSLCertificateFile：证书位置
   SSLCertificateKeyFile：私钥位置

# 二. TLS交互

![](/images/tls_interaction.jpg)

参考：https://www.jianshu.com/p/7158568e4867

