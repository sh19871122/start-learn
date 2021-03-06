# jetbrains激活问题
```
关于较新版本的IDE激活后判断失效问题的处理
```

## jetbrains解决ibus输入法锁定的问题
```
1. Go to "Help | Edit Custom VM options..."
2. Add -Drecreate.x11.input.method=true to a new line
3. Restart IDE
```
## DNS地址
```
# NAT
DNSSERVER=192.168.81.2
不固定，只要几处配置一致即可
```

## 更改DNS
```
安装dnsmasq，将nameserver设置为$DNSSERVER（暂定，后续防火墙需要）

更改hosts
0.0.0.0 account.jetbrains.com
0.0.0.0 www.jetbrains.com
0.0.0.0 jetbrains.com

dnsmasq.conf文件参见同目录文件
```

## 更改dns
```
nmcli c e <interface>
set ipv4.dns 127.0.0.1
set ipv4.ignore-auto-dns yes
```

## 更改防火墙规则
```
firewalld-cmd操作：
    > sudo firewall-cmd --permanent --direct --add-rule ipv4 filter OUTPUT 0 -o ens33 -p udp ! -d $DNSSERVER --destination-port 53 -j DROP
    > sudo firewall-cmd --complete-reload

# 删除可能错误的配置
> sudo firewall-cmd --permanent --direct --get-all-rules
 ipv4 filter OUTPUT 0 -o ens33 -p udp '!' -d 114.114.114.114 --destination-port 53 -j DROP
> sudo firewall-cmd --permanent --direct --remove-rule ipv4 filter OUTPUT 0 -o ens33 -p udp '!' -d 114.114.114.114 --destination-port 53 -j DROP

若非firewalld服务，直接Iptables命令如下：
    > sudo iptables -A OUTPUT -o ens33 -p udp ! -d 114.114.114.114 --destination-port 53 -j DROP
```

## 说明
```
发现jetbrains较新版本均是直接请求dns，且程序内至少有1.1.1.1,1.0.0.1两个dns服务器列表用于查询account，www域名
```

## ref
    [ibus+jetbrains]("https://youtrack.jetbrains.com/issue/JBR-2444")
