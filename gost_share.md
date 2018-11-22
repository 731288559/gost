## 端口转发：隐藏实际ip,端口号，内网穿透

### 本地端口转发

- listen          192.168.1.99

```gost -L=tcp://:9011/202.118.65.2:80 [-F=...]```

- host     

```curl 192.168.1.99:9011/app/login.html```


- 远程端口转发

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
把192.168.1.99上的9017端口的信息，(通过172.17.0.4)，转发到202.118.65.2:80。

- host

```curl 192.168.1.99:9017/app/login.html```

url: http://202.118.65.2:80/app/login.html


### 转发代理: 科学上网
-

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









gost -L=xxx -F=xxx -F=xxx

read param, -F/L/D -> options, routes=[options1, options2]

- ```
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


            

