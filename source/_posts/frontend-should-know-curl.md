title: 前端也应该懂 curl
date: 2015-08-27 12:22:43
tags: curl
---
有一天，pm询问我，生产环境上有一个 report 系统无法从客户端访问，到底是什么问题。
<!--more-->

我怀疑是 report 的服务器挂掉了，于是我打开浏览器，输入服务器的host，然后网页显示打不开。 

然后，我自信的告诉 pm ， 应该是服务器上没有部署或者对应的服务器挂掉了，得去问问运维的人。 然后我发了一封邮件给运维的组。

过了一会，我收到来自一个资深同事的回复,他贴了一段 curl 的信息：

```
* Hostname was NOT found in DNS cache
*   Trying xxxxx ...
* Connected to web-proxy.houston.hp.com (xxxxx) port 8080 (#0)
* Establish HTTP proxy tunnel to xxx:443
> CONNECT xxx:443 HTTP/1.1
> Host: xxx:
> User-Agent: curl/7.35.0
> Proxy-Connection: Keep-Alive
> 
< HTTP/1.1 200 Connection established
< 
* Proxy replied OK to CONNECT request
* successfully set certificate verify locations:
*   CAfile: none
  CApath: /etc/ssl/certs
* SSLv3, TLS handshake, Client hello (1):
* SSLv3, TLS handshake, Server hello (2):
* SSLv3, TLS handshake, CERT (11):
* SSLv3, TLS handshake, Server finished (14):
* SSLv3, TLS handshake, Client key exchange (16):
* SSLv3, TLS change cipher, Client hello (1):
* SSLv3, TLS handshake, Finished (20):
* SSLv3, TLS change cipher, Client hello (1):
* SSLv3, TLS handshake, Finished (20):
* SSL connection using AES128-SHA
* Server certificate:
*        subject: C=US; ST=CA; L=Sunnyvale; O=Hewlett Packard; OU=HP Web Services; OU=PremiumSSL Wildcard; CN=*.xxx
*        start date: 2013-09-07 00:00:00 GMT
*        expire date: 2014-09-07 23:59:59 GMT
*        issuer: C=GB; ST=Greater Manchester; L=Salford; O=COMODO CA Limited; CN=COMODO High-Assurance Secure Server CA
*        SSL certificate verify result: certificate has expired (10), continuing anyway.
> GET /xxx/xxx/xxx?:embed=no&:customViews=yes&devid=94663018-baa2-11e3-99da-1cc1de6f6c7e&token=bearer%200%2FxjdNJ%2FFTTeG3TSfxU93hYA HTTP/1.1
> User-Agent: curl/7.35.0
> Host: xxx
> Accept: */*
> 
< HTTP/1.1 403 Forbidden
* Server nginx/1.6.2 is not blacklisted
< Server: nginx/1.6.2
< Date: Thu, 27 Aug 2015 04:05:15 GMT
< Content-Length: 0
< Connection: keep-alive
< X-HP-DEBUG: xxx :bearer 0/xjdNJ/FTTeG3TSfxU93hYA 403:Permission denied|
< 
* Connection #0 to host xxx left intact
```

然后他告诉我们，因为服务器的反向代理的黑名单机制，所以我们的访问都被拒绝了。 再然后， 问题修复。

我深深的被 curl 这个命令折服了。简简单单的一条命令，但是却可以将网络的状况，路由的跳转一一告诉我们，让我们知道，问题大概出在哪里了。 而不是像我之前的做法，只是知道服务器有问题了，但是却并不明白问题是什么。 

作为偏前端的开发，大多数时候，我们都不会接触到后台的服务器，不会了解到简简单单一个http请求，到底辗转多少次，才到达最终的服务。也不明白，看似一般的服务器不响应，其实可能有很多不同的原因。我们也确实没有精力和机会去探索后台服务器的拓扑结构。 但是， curl 命令给了我们这样的能力，可以从最熟悉的http开始，了解后台的构成，机制。 不仅仅是可以解决类似的问题。 而且，也可以开拓视野。仅仅只关注自己业务的部分，很多时候，是远远不够的。

[curl 参考](http://curl.haxx.se/docs/manpage.html)