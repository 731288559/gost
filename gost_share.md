## 本地端口转发：隐藏实际ip,端口号
gost -L=tcp://:9010/192.168.1.99:8000 [-F=...]
将本地TCP端口9010上的数据(通过代理链)转发到192.168.1.99:8000上。

---

## 远程端口转发：内网穿透，科学上网
A -> C                                  False
A -> B , B -> C  ==>  A -> gost -> C    True

### server:
gost -L=socks5://xiaoheihe:123456@:1234

### client  (docker 1: 5eadff0ae852):
gost -L=rtcp://:9016/202.118.65.2:80 -F=socks5://xiaoheihe:123456@192.168.1.99:1234 [-F=scheme://][user:pass@host]:port
把192.168.1.99上的9016端口的信息，转发到本地202.118.65.2:80。

### user    (docker 2: 2e69e0c4c1f8):
curl 192.168.1.99:9016/app/login.html;JSESSIONID=6122c867-5487-430f-b654-7f3d311f1108


url: http://202.118.65.2:80/app/login.html;JSESSIONID=6122c867-5487-430f-b654-7f3d311f1108

---

- gost -L=xxx -F=xxx -F=xxx
- read param, -F/L/D -> options, routes=[options1, options2]
- ```python
    for route in routes:
        route.serve():
            chain = initChain():[chainNode1, chainNode2, ...]
                chainNode = parseChainNode():
                    Transport   ->Transporter
                    Protocol    ->Connector
            for ns in ServeNode:
                node = ParseNode():
                    Transport   ->Listener(node.addr)
                    handlerOptions = {chain, users, whilelist, blacklist, node.addr ...}
                    Protocol    ->Handler(handlerOptions)
                srv = Server(Listen)
                srv.serve(Handler):
                    Handler.Handle(conn):
                        req = http.ReadRequest(bufio.NewReader(conn))
                        handleRequest(conn, req):
                            forwardRequest(conn, req, chain):
                                transport(conn, cc)
    ```


            


