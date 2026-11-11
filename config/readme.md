# config 设备配置模板说明

> 本目录是设计评审/实验室验证用的 Huawei VRP 示例，**不是可直接部署的运行配置**。各文件按[统一规划基线](../00-统一规划基线.md)对齐；目标型号、硬件端口、软件版本、命令语法及许可必须逐项确认。生产配置不得含占位密码或真实密钥。

## 模板与适用范围

| 文件 | 对应角色 | 用途 |
|---|---|---|
| `core/core-switch.cfg` | Core-A | 核心业务SVI/VRRP、DHCP Relay、OSPF、默认路由示例；端口号占位 |
| `core/core-b.cfg` | Core-B | Core-B实际地址、VRRP备用优先级与OSPF Router-ID |
| `aggregation/east-agg-01.cfg` | 东区汇聚模板 | 每个VLAN997 Transit地址不同；本区4台设备均需逐台实例化 |
| `aggregation/west-agg-01.cfg` | 西区汇聚模板 | 本区4台设备逐台实例化 |
| `aggregation/middle-agg-01.cfg` | 中区汇聚模板 | 本区2台设备逐台实例化 |
| `aggregation/new-agg-01.cfg` | 新区汇聚模板 | 本区4台设备逐台实例化 |
| `access/access-poe-template.cfg` | 楼层PoE接入 | AP端口hybrid/native VLAN998 + 本楼业务tag VLAN；双光纤上联 |
| `ac/ac6805.cfg` | AC6805 | AP发现、AP组、SSID/VAP、Portal/RADIUS参数示意 |
| `firewall/usg6000.cfg` | USG6000系列FW | 两条核心transit、DMZ、默认拒绝策略与NAT示意 |

## 全局参数基线

- 管理VLAN999：192.168.99.0/24，Core-A .1、Core-B .2、VIP .254、AC .10、汇聚 .11–.24、接入 .30–.232、FW管理 .240。
- AP管理VLAN998：192.168.64.0/19，VIP .1、核心实地址 .2/.3；Option43/DNS发现AC 192.168.99.10。
- OSPF Transit VLAN997：192.168.100.0/27，Core-A .1、Core-B .2、Agg .3–.16；仅核心—汇聚。
- DHCP/内部服务VLAN996：192.168.96.0/24，建议DHCP主备服务 .10/.11，核心通过Relay转发。
- 楼栋业务网：东1001–1013 / 10.10.1–13.0/24；西2001–2012 / 10.20.1–12.0/24；中3001–3002 / 10.30.1–2.0/24；新4001–4007 / 10.40.1–7.0/24。每栋网关VIP `.1`、Core-A `.2`、Core-B `.3`。
- 核心—FW三层链路：FW .1/Core-A .2 位于192.168.101.0/30；FW .5/Core-B .6 位于192.168.101.4/30。
- DMZ VLAN888 / 192.168.88.0/24，默认网关在FW `.1`；Portal/RADIUS/日志 `.10/.11/.12`。
- AC、区域汇聚和楼层接入的管理面默认路由指向VLAN999核心VIP `.254`；核心默认路由仍指向FW transit对端。FW .240位于VLAN999带内管理网，远端NMS使用经 `.254` 的更具体路由，并限制管理源。

## 部署前逐项修改/验证

1. 每台设备管理IP、OSPF Router-ID、准确区域/楼栋/AP VLAN映射；验证不重复。
2. 用真实设备 `display version`、硬件资料和接口标签映射接口；当前`XGE/GigabitEthernet`端口号只是示例。
3. 验证DHCP Relay服务器地址、主备切换、地址排除、Option43/DNS发现与合法的DHCP Snooping trust方向；不要在双核心上部署互不共享的同范围全局DHCP池。
4. 接入AP口按对应型号/软件验证hybrid/trunk命令：AP管理未标记在998，SSID业务带该栋VLAN标签；上联仅放行998、999和本栋业务VLAN。
5. 核心到汇聚trunk需放行VLAN997、998、999和本区实际业务VLAN；接入边缘不放行996/997/888或其他区域业务。
6. 普通Eth-Trunk/LACP只可在同一设备/已验证的堆叠或MLAG对端使用。双上联到两台独立汇聚时用MSTP或经评审的多机链路方案。
7. 在仿真/实验室验证VRRP、MSTP、OSPF、DHCP Relay、NAT、安全策略和回滚；在设备上核对硬件/软件命令语法后才能审批上线。
8. Firewall security policy默认拒绝；按应用端口/源/目的细化放行。禁止沿用过宽的`trust any -> untrust permit`示例。
9. 删除所有`<PLACEHOLDER>`、测试公网IP、示例凭据；RADIUS key由安全渠道下发且不得提交版本库。

## 型号/容量待确认

推荐候选设备见[统一规划基线](../00-统一规划基线.md)。CE6855完整SKU、AC6805≥4,619 AP的版本/License、USG6000子型号/安全吞吐以及DMZ/服务器实际接入方式目前未由资料证明；确认前配置只供审阅，不构成采购或上线承诺。
