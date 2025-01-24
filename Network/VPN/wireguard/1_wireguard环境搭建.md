# 1_wireguard环境搭建

## 1. wireguard简介

## 2. wireguard server安装

### 2.1. wireguard server for windows

### 2.2. wireguard server for linux

> TODO

## 3. wireguard client安装

### 3.1. wireguard client for windows

### 3.2. wireguard client for linux

## 4. 配置

### 4.1. wireguard server配置

```ini
[Interface]
PrivateKey = gFb5s8Sf2UpXiHe/krBqGblz7LXaYxnamxAm70B5zUE=
ListenPort = 51821
Address = 192.168.63.86/24
PostUp = powershell -Command "New-NetIPAddress -IPAddress 192.168.63.86 -PrefixLength 24 -InterfaceAlias 'WireGuardTunnel'"
PostUp = powershell -Command "New-NetRoute -DestinationPrefix 0.0.0.0/0 -InterfaceAlias 'WireGuardTunnel' -NextHop 192.168.63.1"
PostDown = powershell -Command "Remove-NetIPAddress -IPAddress 192.168.63.86 -Confirm:$false"
PostDown = powershell -Command "Remove-NetRoute -DestinationPrefix 0.0.0.0/0 -NextHop 192.168.63.1 -Confirm:$false"


[Peer]
PublicKey = Auo6kg4z9ASpeJuX9QzXINdFy9eEn3R5KXJ1NOs2lGs=
AllowedIPs = 10.0.0.2/24

[Peer]
PublicKey = OSV2NkmwfIu+L3LlOVdJydUaw/ztLtRbSlmihIEZ3xA=
AllowedIPs = 10.0.0.3/24

```


```ini
[Interface]
PrivateKey = GOgUZdCD19OAgikaGc7ztDJVzkd1uocqk85w5syDWVI=
Address = 10.0.0.2/32
DNS = 114.114.114.114

[Peer]
PublicKey = S4wUWeP3wg6vQcEGaKiHHYKP6cmTzCYlubHXe32d5BI=
AllowedIPs = 0.0.0.0/0
Endpoint = 192.168.63.73:51820
PersistentKeepalive = 25



[Interface]
PrivateKey = +L3JgrYAW6c4I53CFSwudhloA6A+OZV14DDliCXb2Gw=
Address = 10.0.0.4/32
DNS = 114.114.114.114

[Peer]
PublicKey = S4wUWeP3wg6vQcEGaKiHHYKP6cmTzCYlubHXe32d5BI=
AllowedIPs = 0.0.0.0/0
Endpoint = 192.168.63.86:51821
PersistentKeepalive = 25
```

# 添加 WireGuard 接口
ip link add wg0 type wireguard
ip address add 10.0.0.4/32 dev wg0
echo QMeTpmIC2NXoxuYUskI8VYodUOKvqhnv4SWOLMkUimE= | tee /etc/wireguard/privatekey > /dev/null
chmod 600 /etc/wireguard/privatekey
wg set wg0 private-key /etc/wireguard/privatekey
wg set wg0 peer S4wUWeP3wg6vQcEGaKiHHYKP6cmTzCYlubHXe32d5BI= endpoint 192.168.63.74:51821 allowed-ips 10.0.0.0/24 persistent-keepalive 25
ip link set down dev wg0
ip link set up dev wg0

iproute add 192.168.63.74:51821 via 192.168.63.1
iproute add 10.0.0.0/24 dev wg0
iproute add 128.0.0.0/1 dev wg0