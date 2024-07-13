# debian12上安装WireGuard的流程
[configure-wireguard-vpn-debian-12](https://reintech.io/blog/configure-wireguard-vpn-debian-12)

# 可用配置
## 服务端防火墙
防火墙推荐全部打开，具体命令如下：
```
sudo iptables -F
sudo iptables -t nat -F
sudo iptables -t mangle -F
sudo iptables -t raw -F
sudo iptables -X
sudo iptables -t nat -X
sudo iptables -t mangle -X
sudo iptables -t raw -X
sudo iptables -P INPUT ACCEPT
sudo iptables -P FORWARD ACCEPT
sudo iptables -P OUTPUT ACCEPT
sudo iptables -t nat -P PREROUTING ACCEPT
sudo iptables -t nat -P INPUT ACCEPT
sudo iptables -t nat -P OUTPUT ACCEPT
sudo iptables -t nat -P POSTROUTING ACCEPT
sudo iptables -t mangle -P PREROUTING ACCEPT
sudo iptables -t mangle -P INPUT ACCEPT
sudo iptables -t mangle -P FORWARD ACCEPT
sudo iptables -t mangle -P OUTPUT ACCEPT
sudo iptables -t mangle -P POSTROUTING ACCEPT
sudo iptables -t raw -P PREROUTING ACCEPT
sudo iptables -t raw -P OUTPUT ACCEPT
```

将防火墙配置永久保存，可以使用如下命令：
```
apt-get install iptables-persistent
```

查看防火墙配置，可以使用如下命令：
```
iptables -L -v -n # 列出所有 `filter` 表中的规则
iptables -t nat -L -v -n # 列出所有 `nat` 表中的规则
```

## 服务端配置
服务端配置如下：
```
[Interface]
Address = 10.200.200.1/24
ListenPort = 51820
PrivateKey = <server private key>
PreUp = iptables --table nat --append POSTROUTING --jump MASQUERADE --out-interface eth0 && true
PreDown = iptables --table nat --delete POSTROUTING --jump MASQUERADE --out-interface eth0 && true
PostDown = wg-quick down wg0

[Peer]
PublicKey = <client public key>
AllowedIPs = 10.200.200.2/32
```

## 客户端配置
客户端配置文件如下：

```
[Interface]
PrivateKey = <client private key>
Address = 10.200.200.2/32
dns = 8.8.8.8

[Peer]
PublicKey = <server public key>
Endpoint = <server ip:server port>
AllowedIPs = 0.0.0.0/0
```

可以通过如下命令生成二维码，供客户端扫描：
```
qrencode -t ansiutf8 < client.conf`
```
