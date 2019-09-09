# HTTP

## 报文

请求报文：请求行，请求头，请求体

![img](https://github.com/CyC2018/CS-Notes/raw/master/notes/pics/HTTP_RequestMessageExample.png)

响应报文：响应行，响应头，相应体

![img](https://github.com/CyC2018/CS-Notes/raw/master/notes/pics/HTTP_ResponseMessageExample.png)

## 首部

分为4种：

通用首部：cache-control，connection，location，date，

请求首部：Host，user-agent，Accept-language，if-Match，if-Range

响应首部：location，vary

实体首部：content-type，last-modified，expires，content-length，content-language

## HTTP方法

get，post，put（幂等），delete

#### get VS post

get：获取服务端数据，参数放在请求头中，有长度的限制

post：跟新服务端数据，数据放在请求体中，不安全，不幂等



## 状态码

200，304，404，500

## 应用

连接：短链接，长连接，流水线

### cookie VS session

两者都是用来弥补HTTP无状态的，可以保存状态信息

cookie：存储在客户端，response.setCookie()，通过请求头发送给服务器

session：保存状态信息在服务端，通过sessionID来获取状态

## HTTPS

HTTP安全性问题：

- 明文传输，内容有可能会被窃听
- 不验证身份，通信方身份可能是伪造
- 无法验证报文的完整性，保温有可能被篡改

对称加密：加密解密都用同一个密钥，无法安全传输

非对称加密：公钥，私钥，公钥传送给对方，让对方加密，自己通过私钥解密

### HTTP混合加密

先非对称加密传输对称加密的密钥，之后通过对称加密进行数据传输

![img](https://github.com/CyC2018/CS-Notes/raw/master/notes/pics/How-HTTPS-Works.png)

### 认证

数值证书认证机构：主要是用来验证服务端身份

服务器会向机构申请公开密钥，机构在验证完公开密钥之后，会把数字证书和公开密钥进行绑定。

之后服务器向客户端发数据，会携带公开密钥，客户端会把公开密钥拿到认证机构确认身份，在进行通信

### 完整性

加密+授权认证

## restful：面向资源

- 看到url就知道资源
- 看到http动词就知道操作
- 看到HTTPStatus就知道结果



## 给老婆解释什么是restful

level0-面向前台

```json
{
    addOrder:{
    	"orderName":"latte"
	}
}
```

level1-面向资源

```json
/orders
{
    addOrder:{
        "orderName":"latte"
    }
}
```

level2-打上标签

```json
POST /orders
{
    "orderName":"latte"
}
```

level3-完美服务

```json
POST /orders
{
    "orderName":"latte"
    "rel":"delete"
    "url":"order/12345"
}
```

SOAP: simple object access protocol

简单对象访问协议：面向行为和处理的	