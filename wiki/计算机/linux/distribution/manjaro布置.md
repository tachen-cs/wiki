

---

> TODO 搬移


### 网络命令

```
arp = ip neighbor
ifconfig = ip address, ip link
netstat = ss
route = ip route
```

ifconfig等命令的package：
```
pacman -S net-tools dnsutils inetutils iproute2
```


### ibus配置

配置文件在`~/.config/ibus`

.xprofile：
```
export GTK_IM_MODULE=ibus
export XMODIFIERS=@im=ibus
export QT_IM_MODULE=ibus
ibus-daemon -d -x
```

默认输入法为第一个，顺序在dconf中配置：`desktop/ibus/general/engines-order`。

### 安装字体



```
mkdir /usr/share/fonts/<font_name>
cd /usr/share/fonts/<font_name>
mkfontscale
mkfontdir
fc-cache -fv
```

