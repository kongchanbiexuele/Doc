# openvpn环境搭建

> creatTime： 2024/6/4
> updateTime: 2024/6/4
> author:     shizx

## 环境准备

依赖安装：
```shell
sudo apt-get install openssl libssl-dev
sudo apt-get install lzop
```

openvpn和easy-rsa安装：
```shell
sudo apt-get install openvpn
sudo apt-get install easy-rsa
```

文档使用软件版本说明：
```shell
openssl           # OpenSSL 1.1.1f  31 Mar 2020
lzop              # lzop 1.04 LZO library 2.10
openvpn           # OpenVPN 2.4.12 x86_64-pc-linux-gnu [SSL (OpenSSL)] [LZO] [LZ4] [EPOLL] [PKCS11] [MH/PKTINFO] [AEAD] built on Aug 21 2023
easyrsa           # Easy-RSA 3
```

## 使用easyrsa为服务端创建证书和秘钥

1. 跳转到easy-rsa目录

    ```shell
    sudo su
    cd /usr/share/easy-rsa/
    ```
2. 执行`vim vars`编辑vars文件，添加环境变量（文件名一定要是vars）

    ```shell
    # vars文件内容
    export KEY_COUNTRY="CN"
    export KEY_PROVINCE="FuJian"
    export KEY_CITY="XiaMen"
    export KEY_ORG="MS"
    export KEY_EMAIL="mile@ms.com"
    export KEY_OU="MileSight"
    export KEY_NAME="ms"
    set_var EASYRSA_CA_EXPIRE       3650
    set_var EASYRSA_CERT_EXPIRE     3650
    set_var EASYRSA_CRL_DAYS        3650
    ```
3. 清除本地所有证书

    ```shell
    ./easyrsa clean-all
    ```
4. 创建CA证书

    ```shell
    # 生成带密码的CA证书
    ./easyrsa build-ca
    # 生成不带密码的CA证书
    ./easyrsa build-ca nopass
    ```

    执行完成后会有如下打印：

    ![20240607093121](https://cdn.jsdelivr.net/gh/kongchanbiexuele/Pic/Doc/20240607093121.png)
    
    回车跳过后，打印证书路径：/usr/share/easy-rsa/pki/ca.crt
5. 创建服务端证书

    - 创建服务端证书请求文件

    ```shell
    # 生成不带密码的服务端证书请求文件
    ./easyrsa gen-req server nopass
    # 生成带密码的服务端证书请求文件
    ./easyrsa gen-req server
    ```
    - 创建服务端证书

    ```shell
    # 执行这一步的时候，如果创建ca证书时有指定密码，这里要输入对应的密码
    ./easyrsa sign-req server server
    ```

    ![20240607100452](https://cdn.jsdelivr.net/gh/kongchanbiexuele/Pic/Doc/20240607100452.png)
6. 根据vars配置文件创建2048位的密钥

    ```shell
    ./easyrsa gen-dh
    ```

7. 收集证书

    easy-rsa生成的证书都在/usr/share/easy-rsa/路径下，我们上述步骤创建的是pki证书，路径为/usr/share/easy-rsa/pki，需要将证书移动到openvpn目录（/etc/openvpn/keys）下方便操作。

    ```shell
    cp -p /usr/share/easy-rsa/pki/ca.crt             /etc/openvpn/keys
    cp -p /usr/share/easy-rsa/pki/issued/server.crt  /etc/openvpn/keys
    cp -p /usr/share/easy-rsa/pki/private/server.key /etc/openvpn/keys
    cp -p /usr/share/easy-rsa/ta.key                 /etc/openvpn/keys
    cp -p /usr/share/easy-rsa/pki/dh.pem             /etc/openvpn/keys
    ```

## 配置openvpn服务端

1. 进入openvpn修改配置文件[`;`是注释掉该行配置]

    ```shell
    cd /etc/openvpn
    vim sever.conf
    ```

    配置文件：
    ```shell
    # 服务端口
    port 1195
    # 使用的传输协议
    proto udp
    # 路由模式，桥接模式用dev tap
    dev tun
    # 保活设置
    ;keepalive 10 120
    # 加密算法设置
    cipher AES-256-CBC
    # 设置连接到vpn的两个设备可以自己通信不用通过服务端路由
    client-to-client
    # 证书路径
    ca keys/ca.crt
    cert keys/server.crt
    key keys/server.key
    dh keys/dh.pem
    # openvpn虚拟出的服务器网段
    server 10.9.0.0 255.255.255.0
    ifconfig-pool-persist /var/log/openvpn/ipp.txt
    # OPVPN服务器所在的网段
    push "route 192.168.63.0 255.255.255.0"
    # 设置一个证书可以让多个客户端使用
    duplicate-cn
    # 设置使用tls-auth，服务端为tls-auth xxx/ta.key 0，客户端为tls-auth xxx/ta.key 1
    ;tls-auth keys/ta.key 0
    # key-direction和tls-auth同理
    ;key-direction 0
    # 设置最大client数量
    max-clients 100
    # 保持密钥和安全参数不变
    persist-key
    # 保持虚拟网络接口不变
    persist-tun
    # 日志路径配置
    status /var/log/openvpn/openvpn-status.log
    log         /var/log/openvpn/openvpn.log
    log-append  /var/log/openvpn/openvpn.log
    # 日志等级
    verb 3
    # 开启传输数据压缩
    ;comp-lzo
    # 客户端断开连接时通知服务端，仅UDP可用（通常在服务端配置文件中见到）
    ;explicit-exit-notify 1
    # 配置用户名密码登录
    ;auth-user-pass-verify /etc/openvpn/checkpsw.sh via-env
    # 是否禁用对客户端证书的验证
    ;verify-client-cert none
    # 将用户名作为通用名称使用
    ;username-as-common-name
    # 设置安全级别，3为最不安全的，可以允许运行高安全级别的用户定义脚本和插件
    script-security 3
    ```
2. 启动openvpn服务

    ```shell
    openvpn --cd /etc/openvpn/ --daemon --config server.conf
    ```
    确认VPN服务启动正常
    ![20240607114416](https://cdn.jsdelivr.net/gh/kongchanbiexuele/Pic/Doc/20240607114416.png)

## 创建openvpn客户端

1. 创建客户端证书[流程同创建服务端证书]

    ```shell
    # 移动到easyrsa目录
    cd /usr/share/easy-rsa/
    # 创建客户端证书请求文件
    ./easyrsa gen-req client nopass
    # 创建客户端证书
    ./easyrsa sign-req client client
    # 收集客户端证书到自定义目录，我这里为了方便还是放到openvpn下
    cp -p /usr/share/easy-rsa/pki/ca.crt             /etc/openvpn/client
    cp -p /usr/share/easy-rsa/pki/issued/client.crt  /etc/openvpn/client
    cp -p /usr/share/easy-rsa/pki/private/client.key /etc/openvpn/client
    ```
2. 配置client.ovpn文件【命名任意】，其实和服务端的server.conf是差不多的，都是配置文件

    ```shell
    client
    proto udp
    dev tun
    ;keepalive 10 120
    cipher AES-256-CBC
    remote 192.168.63.74 1195
    # 连接失败后无限重试
    resolv-retry infinite
    # 客户端本地启动时随机指定端口，否则需要指定固定端口
    nobind
    persist-key
    persist-tun
    # 这里设置证书和秘钥路径
    ca ca.crt
    cert client.crt
    key client.key
    remote-cert-tls server
    # 同服务端tls-auth
    ;tls-auth ta.key 1
    # 开启传输数据压缩
    ;comp-lzo
    verb 3
    # 设置用户名密码文件
    ;auth-user-pass pass.txt
    ```
3. 打包客户端ovpn文件

    ```shell
    mkdir clientovpn
    cp client.ovpn clientovpn/
    cp ca.crt     clientovpn/
    cp client.crt clientovpn/
    cp client.key clientovpn/
    ```

    然后使用openvpn GUI打开client.ovpn文件就可以了。如果连接失败，修改client.ovpn文件中指定的证书路径。
