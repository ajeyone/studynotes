# HTTP 报文

报文分为请求和响应。

## 请求
```
[method] [url] [http_version]
[header]
[header]
...0-n

[body]
```

## 响应
```
[http_version] [status_code] [reason_phrase]
[header]
[header]
...0-n

[body]
```

## HTTP 协议原文
> ```
> HTTP-message      = Request | Response
>
> generic-message   = start-line
>                     *(header CRLF)
>                     CRLF
>                     message-body
>
> start-line        = request-line | status-line
> ```

> ```
> request-line      = method SP request-uri SP http-version
>
> request           = request-line
>                     *((general-header | request-header | entity-header) CRLF)
>                     CRLF
>                     message-body
>
> status-line       = http-version SP status-code SP reaseon-phrase
>
> response          = status-line
>                     *((general-header | response-header | entity-header) CRLF)
>                     CRLF
>                     message-body
> ```

## GET 和 POST 的区别
1. GET 把要传递给服务器的参数或数据拼在 URL 中，例如`url?a=1&b=2&c=3`；而 POST 把参数或数据写在消息体中。
2. 因此 GET 会将参数暴露在地址栏中，而 POST 传递的参数都在 BODY 中。
3. 语义上 GET 是从服务器获取东西，是读操作，不会改变服务器内容。
4. 语义上 POST 是发送给服务器数据，是写操作，会改变服务器内容。

## 常见的状态码：
- 200 OK 成功
- 301 Moved Permanently 已经永久移动，通常会附上 Location 头来指示移动到哪里了
- 304 Not Modified 条件请求的响应，表示资源没有改变
- 404 Not Found 资源不存在

# HTTP 缓存

缓存的目的就是用快速存储设备存放低速存储设备中的内容，以空间换时间，达到节约带宽和流量，快速响应的目的。

## 新鲜度检查

第一次访问某个资源肯定是没有缓存可用，直接下载资源，于是有一个 Response，它的 Header 会携带缓存控制相关的信息。例如 `cache-control:max-age` 表示过了多长时间后才需要验证资源是否过期。`cache-control:max-age` 指示的是相对时间，单位是秒。

为什么要有个时间呢，因为不可能随时都去服务器端检测资源有没有过期。而是在这段时间内不用去服务器检测是否过期。

所谓新鲜度检查就是检查当前时间有没有在“保质期”内，是直接使用缓存，还是需要去服务器端请求资源，

## 再验证
再验证就是问服务器这个资源有没有改动，我需不需要重新下载。

问的过程就是请求了，这种带着询问条件的，叫做条件请求。有很多种 header 可以用来构建条件请求：
- If-Modified-Since：从 XXX 时刻开始，如果有修改过，就返回我数据。这个 XXX 要设置为缓存的那次 Response 中的 Last-Modified 的值。很直观，先知道了本地缓存的资源是什么时刻生成的，再通过这个时刻去查后来有没有修改。
- If-None-Match：Last-Modified 的时间单位是秒，有可能秒不够表达这个资源快速的修改，那么就要用到一个 Etag，它也是服务器返回的 Reponse 中携带的，可以理解为一个数据指纹，只要服务器端的数据有任何的改动 Etag 的值就会改变。If-None-Match 表示如果不匹配上次的 Etag，就返回我数据。

一个是用时间，一个是用版本号

如果验证
