# 华为 V173 光猫使能工具与教程

用于华为 V173（F30 系列主光猫，XG-PON / 10G EPON）的维修使能、补全 Shell、开启 Telnet，以及后续改公版、改 SN/MAC、切换光模。

## 配套教程

完整操作步骤、判断依据和排障表在这篇文章：

[华为 V173 光猫使能教程：补全 Shell、改公版与修改 boardinfo](https://xiaozou123.cn/huawei-v173-ont-enable-huawei-interface/)

文章里的每一步都和本仓库的文件对应，建议先看完教程再动手。

## 适用固件与工具版本

- 实测机器：华为 V173（F30 系列主光猫），固件 `V5R022C10S538`，即 R022。
- 使能工具只认 `ONT_V100R002C00SPC253.exe`。可用的工具版本落在 `V100R001C00` 的 1xx 与 `V100R002C00` 的 2xx 两档；`ONT_V100R002C00SPC500.exe` 这类 5xx 版本，以及 `ONT组播配置工具V3-V5 2.0`，在这台机器上实测无效。
- 无效的表现是：点【启动】后光猫指示灯不闪，网卡没有流量，工具也不报错。别以为机器坏了，换回 253 立刻能写进去。
- 补全包要跟固件代际对上。R021 的机器不要刷 R022 的包，跨代刷写是变砖的主要来源。

## 文件清单

| 文件 | 大小 | 用途 |
|---|---|---|
| `ONT_V100R002C00SPC253.exe` | 9,101,312 | 维修使能工具，刷 `R22_shell.bin` 用 |
| `R22_shell.bin` | 1,808,512 | R22 补全 Shell 维护包 |
| `HW_Dollar2.exe` | 161,280 | 编辑 `hw_boardinfo`，2.2.0.2104 |
| `tftpd64.exe` | 343,040 | TFTP 服务端，取回/回传 boardinfo |
| `Huawei_Config_Crypto.exe` | 1,794,560 | 华为配置文件加解密，处理 `hw_ctree.xml` 时用 |

### SHA256

```
c882032e2dcd1a3ea2fc5e359e7c22a94d024fb0ae7e7be2f1cefd341796f8ec  ONT_V100R002C00SPC253.exe
5a72ddf7ed9e3b8c248c8e7b501bb4fca7affa28474fa2d57882d122ee331f62  R22_shell.bin
d3110469d8cccc7ca479105393e047faef74c74cfb34d174deece84153ac1cc8  HW_Dollar2.exe
2f5de838cf3079723f1697e5fac2f75fad4b98cd81b165f87cdfa99ded22d801  tftpd64.exe
8f005af234f73a456f5e4a127ed4624c40992445caf264d5f7566cbf3ee80808  Huawei_Config_Crypto.exe
```

`ONT_V100R002C00SPC253.exe` 是实测唯一能在这批 R022 固件上写入的工具。原始的 `ONT组播配置工具V3-V5 2.0.exe`、`华为光猫ONT使能2.0.exe`、`ONT_V100R002C00SPC500.exe` 这几个版本点【启动】后光猫指示灯不闪、日志也不报错，看着像工具卡住，实际是不认固件，本仓库不再保留。

## 使用顺序

1. 装好 Windows Telnet 客户端，电脑网卡设 `192.168.1.2`。
2. 用 `ONT_V100R002C00SPC253.exe` 刷 `R22_shell.bin`，灯全闪后停住，断电重启。
3. telnet `192.168.1.1`，进 shell 执行 `EquipMode.sh on`。
4. 网卡改 `192.168.100.2`，用 `tftpd64.exe` 取回 `hw_boardinfo` 和 `hw_boardinfo.bak`。
5. 用 `HW_Dollar2.exe` 修改，V5 模式保存，回传覆盖。
6. `EquipMode.sh off` 后重启。

## 相关仓库

同品牌其它机型，命令体系或刷机思路相近，排障时可以互相参照；每个仓库开头都对应一篇文章：

- [华为 F30 工具包](https://github.com/dellzou/Huawei_F30_Flashing_Software_and_Tutorial)：改 SN、改 MAC、取超密
- [华为 H1s-3 / H2-3 工具包](https://github.com/dellzou/Huawei_H1s3_H2-3_Flashing_Software_and_Tutorial)：四条入口、确认码与改地区
- [华为 HN8145X6 工具包](https://github.com/dellzou/Huawei_HN8145X6_Flashing_Software_and_Tutorial)：补全 AllShell、改光模
- [华为 HN8145XR 工具包](https://github.com/dellzou/Huawei_HN8145XR_Flashing_Software_and_Tutorial)：主猫改公版、从猫组网
- [华为 HS8546V5 工具包](https://github.com/dellzou/Huawei_HS8546_Flashing_Software_and_Tutorial)：13 个分区与 jffs2 备份恢复
- [华为 K662D 工具包](https://github.com/dellzou/Huawei_K662D_Flashing_Software_and_Tutorial)：从猫改光猫模式
- [华为 V173-50 工具包](https://github.com/dellzou/Huawei_V173-50_Flashing_Software_and_Tutorial)：R24 装备包与 OBSCTool 日志
- [华为 V175 工具包](https://github.com/dellzou/Huawei_V175_Flashing_Software_and_Tutorial)：R23 固件包开 Telnet

## 注意

- 这些工具会发原始组播包、监听端口、修改网络配置，被杀毒软件报毒是常态。建议放在虚拟机或冰点还原环境里运行。
- 本仓库只有使能工具和补全包，**没有完整固件、没有地区包**。改回运营商界面只能靠事先备份。
- 仅用于自己拥有或获得授权的设备。改 SN/MAC 涉及设备标识，换机克隆前建议先向运营商报备。
- 刷机有风险，可能造成设备损坏或失去保修，操作前请确认已备份 `hw_boardinfo`、`hw_boardinfo.bak`、`hw_ctree.xml` 和分区。
