参考链接: https://www.digitalocean.com/community/tutorials/how-to-set-up-an-openvpn-server-on-debian-11

./easyrsa build-ca要求密码时，切换为不需要密码的模式：./easyrsa build-ca nopass
./easyrsa sign-req server server可以使用--days参数修改有效期：./easyrsa --days=3650 sign-req server server
