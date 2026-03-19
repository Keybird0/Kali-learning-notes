# MAC绑定

## 简介
> 管理员常误认为MAC绑定是一种安全机制
> 可以限制关联的客户端MAC地址

## 绕过方式
```
macchanger -m 7C:DD:90:EB:1D:02 wlan1mon

假设这里7C:DD:90:EB:1D:02是用airodump检测到的成功连接上AP的Station地址
```

## 注意
> 在同一局域网下,通信是依赖mac地址的,所以两个相同的mac地址会造成通信的一些障碍,不过可以绕过mac地址绑定限制.
