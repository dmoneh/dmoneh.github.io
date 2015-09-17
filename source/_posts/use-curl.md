title: curl 简单入门
date: 2015-09-10 11:31:28
tags: curl
---
### 1.什么是 curl
curl 官方的说法是一种用来在客户端和服务器之间做数据传输的命令行。简单的说，你可以用下面这个命令，将百度首页的内容下载下来：
```
$ curl http://baidu.com

<html>
<meta http-equiv="refresh" content="0;url=http://www.baidu.com/">
</html>

```
以上就是下载的内容，如果你懂html的话，可以发现，百度的首页会自动跳转到 www.baidu.com 这个新地址。

<!--more-->

如果很不幸你需要代理才能上网（比如在公司内网的时候），那你只需要稍微改改这个命令：
```
$ curl -x http://proxy.com http://baidu.com
```
-x 表示启用代理，后面跟的自然就是代理的地址。

如果你想把内容下载成一个文件，也可以：
```
$ curl -x http://proxy.com -o baidu.html http://baidu.com
```

-o 表示输出下载内容到一个文件，后面跟的自然就是文件名了。成功执行命令，目录中就会出现这个baidu.html

前面所说的都是从网上下载东西，看起来好像没什么用。是啊，有浏览器，我没还用这个干嘛呢？

可是有一些东西，浏览器是没法做到的。
比如，你需要访问一个开源api，你必须传递一些http header，这个时候，浏览器就没办法了。这还只是get请求，如果是post，put呢？

再比如，你需要要测试一些api，需要上传json数据，或者文件，这个时候，即使是postman，也无法又快又好的完成任务。

如果你了解curl，然后你可以预先写一些脚本，方便自己的同时，也可以分享给其他人？何乐而不为呢？

### 2. 更多用法

* 灵活的header

  可以随意的指定header ,一个

    ```
    $ curl -H "name:Joyce"
    ```
    或者多个
    ```
    $ curl -H "name:Joyce" -H "age:100"
    ```
    或者引用本地文件内容,比如token
    ```
    $curl -H "Authorization:${cat token.txt}"
    ```

* 随意指定http method

  如果你不指定，默认就是GET。

  这是POST
  ```
  $ curl -XPOST
  ```
  -X表示指定method

* 发送数据

  可以将数据post或者put到远端。
  ```
  $ curl -XPUT http://des.com -d xxx
  ```
  -d 后面就是你想要put到http://des.com的数据，需要注意的是，对于一些数据，可能需要预先指定传输格式：
  比如，对于图片，用 --data-binary，表示传输二进制数据。

  ```
  $ curl -XPUT http://des.com --data-binary @icon.jpg
  ```

  这里的 @ 表示从指定文件中获取数据。

  对于json数据，除了指定 content-type之外，还要注意一下json格式。

  ```
  $ curl -XPOST -H "Content-Type: application/json" http://des.com -d '{"name":"silentsong"}'
  ```
  上面json中的key和value都必须用双引号包装起来，整个json数据必须包含在单引号之中。


 * form 支持

   curl支持上传一个form表单。比如最常见的登录表单

   ```
    $ curl  -XPUT -F name=slientsong -F password=123 http://dmoneh.github.io/silentsong/login
   ```

   有时候，表单中会有上传文件

   ```
   $ curl -XPUT -F file=@upload.json http://dmoneh.github.io/silentsong/files
   ```


 * 探查http交互情况

  ```
  $ curl -v -x http://myproxy:8080 http://baidu.com

   * Adding handle: conn: 0x652be0
   * Adding handle: send: 0
   * Adding handle: recv: 0
   * Curl_addHandleToPipeline: length: 1
   * - Conn 0 (0x652be0) send_pipe: 1, recv_pipe: 0
   * About to connect() to proxy myproxy port 8080
   *   Trying 10.0.0.1...
   * Connected to myproxy (10.0.0.1) port 808
   > GET http://baidu.com/ HTTP/1.1
   > User-Agent: curl/7.30.0
   > Host: baidu.com
   > Accept: */*
   > Proxy-Connection: Keep-Alive
   >
   < HTTP/1.1 200 OK
   < Date: Wed, 09 Sep 2015 05:39:38 GMT
   * Server Apache is not blacklisted
   < Server: Apache
   < Cache-Control: max-age=86400
   < Expires: Thu, 10 Sep 2015 05:39:38 GMT
   < Last-Modified: Tue, 12 Jan 2010 13:48:00 GMT
   < ETag: "51-4b4c7d90"
   < Accept-Ranges: bytes
   < Content-Length: 81
   < Content-Type: text/html
   < Proxy-Connection: Keep-Alive
   < Connection: Keep-Alive
   < Age: 83195
   <
   <html>
   <meta http-equiv="refresh" content="0;url=http://www.baidu.co
   </html>
   * Connection #0 to host myproxy left intact

  ```

  仔细看一下上面的信息，> 表示从本地发出的http 头信息，< 表示远端返回的头信息，* 表示 curl额外提供的信息（非标准的http信息）。

  我们可以看到代理的一部分工作，接受我们的请求，返回请求数据。相当于一个网关。


### 3. 总结

curl 是一个很有用的工具，一般人我不告诉的哦。

下一步打算翻译一下 [curl man page](http://curl.haxx.se/docs/manpage.html),做一些页面美化和排版，以方便使用。

敬请关注。




