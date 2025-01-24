# network issues

## 1. localhost被发送到DNS服务器进行解析

现象：使用 ssh反向隧道时，localhost解析失败，反向隧道能收到报文但实际上没把报文转发到被代理端口。

原因：localhost本身就是一个特殊域名，域名解析先走hosts文件，再走dns解析，因为hosts文件不存在localhost 到127.0.0.1的映射，导致走了DNS，从而域名解析失败。