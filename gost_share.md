# gost - GO Simple Tunnel

科学上网,提高访问速度,隐藏真实IP,隐藏实际ip,端口号,内网穿透

## 端口转发：

### 本地端口转发

- listen          192.168.1.99

```gost -L=tcp://:9011/202.118.65.2:80 [-F=...]```

- host     

```curl 192.168.1.99:9011/app/login.html```

把发送到本地上9011端口的请求，(通过代理链)，转发到202.118.65.2:80。

### 远程端口转发

A -> C                                  False

A -> B , B -> C  ==>  A -> gost -> C    True

- listen          192.168.1.99

```gost -L=socks5://xiaoheihe:111111@:1234```

- proxy           172.17.0.4

```gost -L=socks5://forward:222222@:1234```

- gost

``` 
    gost -L=rtcp://:9017/202.118.65.2:80 
    -F=socks5://forward:222222@172.17.0.4:1234
    -F=socks5://xiaoheihe:111111@192.168.1.99:1234
```
把发送到192.168.1.99上9017端口的请求，(通过172.17.0.4)，转发到202.118.65.2:80。

- host

```curl 192.168.1.99:9017/app/login.html```

url: http://202.118.65.2:80/app/login.html


## 转发代理: 

<img src="https://ginuerzh.github.io/images/gost_03.png" />

- proxy 1

```gost -L=:9016```

- proxy 2

```gost -L=:9015```

- gost

```gost -L=:9014 -F=192.168.1.99:9015 -F=192.168.1.99:9016```

- host

```
    curl http://202.118.65.2:80/app/login.html -x "192.168.1.99:9014"
    // export http_proxy=http://192.168.1.99:9014
    // curl www.baidu.com

```

------

### 协议类型(Protocols)

支持的协议类型有：

#### http - HTTP

#### http2 - HTTP2  
```
http1.0 -> http1.1 : 持久连接，管道机制，分块传输编码，新增请求方式

http1.1 -> http2.0 : 二进制协议，完全多路复用，报头压缩，服务器推送

https = http + ssl/tls 
```
#### socks4 - SOCKS4 (2.4+)

#### socks4a - SOCKS4A (2.4+)

#### socks5 - SOCKS5
```
socks:      简单地传递数据包，不关心是何种应用协议，速度快

socks4:     支持tcp

socks4a:    增加了域名解析,允许客户端指定目标域名而不是IP地址

socks5:     支持tcp,udp,验证功能 
```
#### ss - Shadowsocks 
<img src="https://tumutanzi.com/wp-content/uploads/2015/01/what-is-shadowsocks.png" />

```
ss-local 和 ss-server 两端通过多种可选的加密方法进行通讯，经过 GFW 的时候是常规的TCP包，没有明显的特征码而且 GFW 也无法对通讯数据进行解密

通过流量混淆，隐秘解决了GFW通过分析流量特征从而干扰的问题
```

#### sni - SNI (2.5+)
```
Server Name Indication  用来改善服务器与客户端 SSL 和 TLS 的一个扩展

主要解决一台服务器只能使用一个证书(一个域名)的缺点:

    (当设置了SSL加密，服务器在读取HTTP请求里面的域名之前已经向客户端提交了证书，服务器不能决定呈现哪一个证书)

    在Client Hello阶段，通过SNI扩展，将域名信息提前告诉服务器，服务器根据域名取得对应的证书返回给客户端已完成校验过程。
```
#### forward - Forward

------

### 传输类型(Transports)

支持的传输类型有：

#### tcp - 原始TCP

#### tls - TLS (Secure Sockets Layer,SSL -> Transport Layer Security,TLS)
<img src="https://img-blog.csdn.net/20161122222513728" />    
```
基于TCP, 解决：加密，完整性，认证
```
#### mtls - Multiplex TLS，在TLS上增加多路复用功能 (2.5+)

#### ws - Websocket      
```
基于TCP，允许服务端直接向客户端推送数据而不需要客户端进行请求 解决：客户端推送时需要轮询，不停发送request
```
#### mws - Multiplex Websocket，在Websocket上增加多路复用功能 (2.5+)

#### wss - Websocket Secure，基于TLS加密的Websocket

#### mwss - Multiplex Websocket Secure，在基于TLS加密的Websocket上增加多路复用功能 (2.5+)

#### kcp - KCP (2.3+)    
```
基于UDP，快速可靠协议，能以比TCP浪费10%-20%的带宽的代价，换取平均延迟降低 30%-40%
```
#### quic - QUIC (2.4+)  
```
基于UDP，可靠，低等待时延(已连接过的情况下可无需握手，直接传送数据)，大胆的前向纠错方案（FEC包，纠错恢复）
```
#### ssh - SSH (2.4+)   
```
在不安全网络上提供安全远程登录及其它安全网络服务的协议
```
#### h2 - HTTP2 (2.4+)

#### h2c - HTTP2 Cleartext (2.4+)  HTTP2明文

#### obfs4 - OBFS4 (2.4+)    
```
obfs4proxy is a tool that attempts to circumvent censorship by transforming the Tor traffic                              between the client and the bridge.

obfs4proxy是一种试图通过转换客户端和网桥之间的Tor流量来规避审查的工具。混淆流量，隐藏访问信息

Tor, The onion route: 洋葱路由加密技术。匿名访问，防止追踪窃听
```
#### ohttp - HTTP Obfuscation (2.5+)




[HTTP,HTTP/2,SPDY,HTTPS](http://www.alloyteam.com/2016/07/httphttp2-0spdyhttps-reading-this-is-enough/)

[sni](https://blog.csdn.net/makenothing/article/details/53292335)

[vpn](https://blog.csdn.net/xianjian1990/article/details/78980018)

[GFW](https://cokebar.info/archives/253)













gost -L=xxx -F=xxx -F=xxx

read param, -F/L/D -> options, routes=[options1, options2]

```
    for route in routes:
        route.serve():
            chain = initChain():[chainNode1, chainNode2, ...]
                chainNode = parseChainNode():
                    Transport   -> Transporter
                    Protocol    -> Connector
                    
            for ns in ServeNode:
                node = ParseNode():
                    Transport   -> Listener(node.addr)
                    handlerOptions = {chain, users, whilelist, blacklist, node.addr ...}
                    Protocol    -> Handler(handlerOptions)

                srv = Server(Listen)
                srv.serve(Handler):
                    Handler.Handle(conn):
                        req = http.ReadRequest(bufio.NewReader(conn))
                        handleRequest(conn, req):
                            forwardRequest(conn, req, chain):
                                transport(conn, cc)
    ```


            
