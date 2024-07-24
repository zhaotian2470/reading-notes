在Debian 12上安装和配置Shadowsocks可以通过以下步骤完成：

### 安装 Shadowsocks

1. **更新包列表和安装依赖项**

```bash
sudo apt update
sudo apt install -y python3-pip
```

2. **安装 Shadowsocks**

使用 `pip` 安装 Shadowsocks：

```bash
sudo pip3 install git+https://github.com/shadowsocks/shadowsocks.git@master
```

### 配置 Shadowsocks

1. **创建配置文件**

首先，创建一个目录来存放配置文件：

```bash
sudo mkdir -p /etc/shadowsocks
```

然后，使用文本编辑器创建配置文件（例如：`/etc/shadowsocks/shadowsocks.json`）：

```bash
sudo nano /etc/shadowsocks/shadowsocks.json
```

在文件中输入以下配置：

```json
{
    "server": "0.0.0.0",
    "server_port": 8388,
    "local_address": "127.0.0.1",
    "local_port": 1080,
    "password": "your_password",
    "timeout": 300,
    "method": "aes-256-gcm"
}
```

将 `"your_password"` 替换为你自己的密码，可以根据需要调整其他配置项。

### 启动 Shadowsocks

1. **手动启动**

```bash
sudo ssserver -c /etc/shadowsocks/shadowsocks.json
```

2. **设置 Shadowsocks 开机启动**

创建一个 systemd 服务文件：

```bash
sudo nano /etc/systemd/system/shadowsocks.service
```

在文件中输入以下内容：

```ini
[Unit]
Description=Shadowsocks
After=network.target

[Service]
ExecStart=/usr/local/bin/ssserver -c /etc/shadowsocks/shadowsocks.json
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

保存并关闭文件，然后重新加载 systemd 服务配置：

```bash
sudo systemctl daemon-reload
```

启动并启用 Shadowsocks 服务：

```bash
sudo systemctl start shadowsocks
sudo systemctl enable shadowsocks
```

### 验证 Shadowsocks

你可以通过以下命令检查 Shadowsocks 服务是否正在运行：

```bash
sudo systemctl status shadowsocks
```

### 客户端配置

在你的客户端设备上配置 Shadowsocks 客户端，使用你在 `shadowsocks.json` 中设置的服务器地址、端口、密码和加密方法。

这样，你就成功在 Debian 12 上安装并配置了 Shadowsocks。
