# config 设备配置模板说明
> ⚠️重要：本目录全部为**模板配置文件，不能直接上线使用**，部署前必须修改对应参数。

## 文件对应设备列表
| 配置文件名                       | 对应设备          | 简要说明                                       |
| -------------------------------- | ----------------- | ---------------------------------------------- |
| `ac/ac6805.cfg`                  | AC6805无线控制器  | 全网AP管理、Portal认证、射频配置模板           |
| `access/access‑poe‑template.cfg` | 楼栋POE接入交换机 | 接入通用模板，所有楼栋接入交换机以此为模板修改 |
| `aggregation/east‑agg‑01.cfg`    | 东区汇聚交换机    | 东区片区汇聚设备                               |
| `aggregation/west‑agg‑01.cfg`    | 西区汇聚交换机    | 西区片区汇聚设备                               |
| `aggregation/middle‑agg‑01.cfg`  | 中区汇聚交换机    | 中区片区汇聚设备                               |
| `aggregation/new‑agg‑01.cfg`     | 新区汇聚交换机    | 新区片区汇聚设备                               |
| `core/core‑switch.cfg`           | 核心交换机        | 全网网关、OSPF、DHCP配置                       |
| `firewall/usg6000.cfg`           | USG6000防火墙     | 出口防火墙NAT、安全域、域间策略模板            |

## 部署前必须修改的通用参数
1. **管理IP地址**：每台设备管理VLAN999 IP，不能冲突
2. **OSPF Router‑ID**：每台三层设备Router‑ID全网唯一，不能重复
3. **VLAN与Trunk允许通过VLAN列表**：根据实际楼栋数量裁剪VLAN范围
4. **接口编号**：根据真实硬件接口修改G0/0/、Eth‑Trunk接口编号
5. **密码**：console密码、stelnet密码，模板内为占位符，上线替换为真实密码
6. **互联对端IP**：三层点到点互联地址，和对端设备保持一致

## 分设备注意事项
### core‑switch.cfg
- 修改所有VLANIF网关IP；DHCP地址池网段；OSPF宣告网段；VRRP虚拟IP。

### aggregation 汇聚交换机
- Trunk放行VLAN：只放行本片区业务VLAN + VLAN999管理VLAN，不要放行其他片区VLAN。
- 修改上联核心的Eth‑Trunk成员接口。

### access‑poe‑template.cfg（接入交换机模板）
- 修改Access端口所属业务VLAN（对应本楼栋VLAN ID）
- 修改上行Trunk接口，只放行本楼栋业务VLAN + VLAN999
- POE功率模式根据实际AP数量调整。

### ac6805.cfg
- 修改AC管理IP；DHCP Option43参数；Portal服务器IP、Radius服务器IP。

### usg6000.cfg
- 修改Trust/Untrust/DMZ各接口IP；NAT地址池；回程静态路由下一跳。

## 使用建议
1. 在eNSP/HCL模拟器导入调试，确认业务通之后，再部署真实硬件。
2. 修改完成另存为新cfg，不要直接覆盖模板文件。