---
title: 关于NGINX转发时的URI规范化
date: 2020-06-02 16:12:47
tags:
  - nginx
---

## 问题描述

当前经过 encodeURI，多数 MVC 框架已经能够正常工作而无需担心特殊字符集的问题。

而如果直接在路径上携带入[]|等字符，MVC 框架会根据自己的情况返回错误的 http 状态码

而一般的 http client 也会自行将 uri 编码，在服务端与客户端都已经做好对应的操作的前提下，仍有可能因为特殊字符导致不可访问的情况。

当使用 nginx 进行转发的时候，如果转发服务带上 request_uri，哪怕是/，如

```
http://example.com/
```

那么 nginx 解析请求后会直接将 uri 进行转发，此时特殊字符将直接被传递到目标服务，引发错误

> If the proxy_pass directive is specified with a URI, then when a request is passed to the server, the part of a normalized request URI matching the location is replaced by a URI specified in the directive:
> 如果使用 URI 指定了 proxy_pass 指令，那么当请求传递到服务器时，与该位置匹配的规范化请求 URI 的一部分将被该指令中指定的 URI 代替：

```
set $modified_uri $request_uri;

if ($modified_uri ~ "^/([\w]{2})(/.*)") {
  set $modified_uri $1;
}

proxy_pass http://example$modified_uri;
```

## 解决方法

### nginx 反向代理不使用/

```
http://example.com
```

nginx 对 uri 会进行规范化再进行传递，从而避免问题。

> If proxy_pass is specified without a URI, the request URI is passed to the server in the same form as sent by a client when the original request is processed, or the full normalized request URI is passed when processing the changed URI:
> 如果指定 proxy_pass 时不带 URI，则将请求 URI 以与客户端在处理原始请求时发送的格式相同的形式传递给服务器，或者在处理更改的 URI 时传递完整的规范化请求 URI：

### rewrite

```nginx
location /foo {
    rewrite  ^  $request_uri;            # get original URI
    rewrite  ^/foo(/.*)  /bar$1  break;  # drop /foo, put /bar， 此处如果转发目标无需前缀，可直接填$1
    return 400;   # if the second rewrite won't match
    proxy_pass    http://localhost:8080$uri;
}
```

### 目标服务自身的变更(Spring Boot)

```java
@Component
public class MyTomcatWebServerCustomizer
        implements WebServerFactoryCustomizer<TomcatServletWebServerFactory> {

    @Override
    public void customize(TomcatServletWebServerFactory factory) {
        factory.addConnectorCustomizers(new TomcatConnectorCustomizer() {
            @Override
            public void customize(Connector connector) {
                connector.setAttribute("relaxedQueryChars", "yourvaluehere");
            }
        });
    }
}
```
