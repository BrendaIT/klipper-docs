# 树莓派CAN使用

> 当前文档不仅适用于树莓派，香橙派等等其他设备同理

?> **FLY-UTOC**介绍等内容在文档[CAN使用](/advanced/can.md)中

## 准备

1. 启动树莓派并确保其连接到网络
2. 使用你熟悉的SSH终端工具连接到树莓派
3. 如果登录到了root账户请切换到普通用户

## 系统配置

1. 执行下面的命令安装nano编辑器
```bash
sudo apt update && sudo apt install nano -y
```

2. 创建配置文件,全部复制粘贴到终端并回车
```bash
sudo /bin/sh -c "cat > /etc/network/interfaces.d/can0" << EOF
auto can0
iface can0 can static
    bitrate 500000
    up ifconfig $IFACE txqueuelen 256
EOF
```

?> 测试发现在部分设备中无法开机自动启用CAN，所以建议都执行下面操作

3. 开机自动启用CAN
```bash
sudo sed -i '/^exit\ 0$/i \ifconfig can0 down\nip link set can0 type can bitrate 500000\nip link set can0 txqueuelen 256\nifconfig can0 up' /etc/rc.local
```

4. 重启设备
```bash
sudo reboot
```

5. USB转CAN模块在树莓派无法即插即用

?> 即插即用只适用于Gemini系列主板，且系统版本≥V2.9

* 如果树莓派设备插拔过USB转CAN请重启设备或者执行下面的命令
```bash
sudo ifconfig can0 down
sudo ip link set can0 type can bitrate 500000
sudo ip link set can0 txqueuelen 256
sudo ifconfig can0 up
```

## 连接

* 使用Type-c数据线连接树莓派和**FLY-UTOC**
* 具体接线可查看文档[CAN使用](/advanced/can.md)

## 查看UUID

?> 网页查看ID只适用于Gemini系列主板，且系统版本≥V2.9

* 执行下面的命令来查找所有已连接的CAN设备

```bash
~/klippy-env/bin/python ~/klipper/scripts/canbus_query.py can0
```

* 出现**Found canbus_uuid=11aa22bb33cc**则查找到设备ID
* 其中**11aa22bb33cc**为设备UUID，可直接填入klipper配置文件
* 如果没有出现ID或报错请认真阅读文档并检查接线

## 其他

* 更多内容请查看文档[CAN使用](/advanced/can.md)