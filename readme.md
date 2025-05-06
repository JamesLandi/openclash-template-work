# OpenClash 配置指南

## 目录
- [自定义规则配置](#自定义规则配置)
- [订阅 INI 自定义模板](#订阅-ini-自定义模板)
- [开发者选项配置](#开发者选项配置)

## 自定义规则配置

### 步骤 1: 访问 OpenClash 自定义规则设置
1. 登录 OpenWrt 管理界面
2. 进入 **服务** > **OpenClash** 
3. 选择 **复写设置** > **规则设置**

### 步骤 2: 配置自定义规则
1. 在 **自定义规则的优先框** 中输入自定义规则文件的内容
2. 可以参考项目中的 `自定义规则.yaml` 文件作为模板
3. 确保规则格式正确，包含必要的 `rules` 和 `sub-rules` 部分
4. 点击保存应用配置

### 步骤 3: 确认规则生效
1. 在 OpenClash 主页面查看规则状态
2. 检查日志以确保没有语法错误
3. 测试规则是否按预期工作

## 订阅 INI 自定义模板

### 步骤 1: 访问订阅转换设置
1. 登录 OpenWrt 管理界面
2. 进入 **服务** > **OpenClash**
3. 选择 **配置订阅** > **订阅转换**

### 步骤 2: 添加自定义 INI 模板
1. 找到 **自定义订阅转换** 部分
2. 选择 **添加** 或 **修改** 自定义配置
3. 在配置中填入 INI 模板内容，参考项目中的 `main.ini` 文件
4. 配置包含以下关键部分：
   - 规则集配置 (ruleset)
   - 策略组配置 (custom_proxy_group)
   - 节点组配置
   - 规则生成器设置

### 步骤 3: 应用配置
1. 保存自定义 INI 模板
2. 确保在订阅转换中选择了刚才保存的模板
3. 更新订阅以应用新模板
4. 检查 OpenClash 状态以确保配置生效

## 注意事项
- 确保 YAML 和 INI 文件格式正确，避免语法错误
- 修改规则后需要重新启动 OpenClash 或更新配置使其生效
- 定期备份您的自定义配置文件

## 开发者选项配置

### 广告过滤功能

#### 步骤 1: 访问开发者选项设置
1. 登录 OpenWrt 管理界面
2. 进入 **服务** > **OpenClash** 
3. 选择 **覆写设置** > **开发者选项**

#### 步骤 2: 添加广告过滤脚本
1. 在开发者选项的编辑框中添加以下脚本：
```bash
#!/bin/sh  
. /usr/share/openclash/log.sh  
. /lib/functions.sh  
# This script is called by /etc/init.d/openclash  
# Add your custom firewall rules here, they will be added after the end of the OpenClash iptables rules  
LOG_OUT "Tip: Start Add Custom Firewall Rules..."

# 以下是广告过滤规则拉取脚本  
LOG_OUT "拉取 anti-AD 广告过滤规则…"  
curl -s https://anti-ad.net/anti-ad-for-dnsmasq.conf -o /tmp/dnsmasq.d/anti-ad-for-dnsmasq.conf  
# 广告过滤规则拉取脚本结束

# 清理 DNS 缓存  
LOG_OUT "清理 DNS 缓存…"  
/etc/init.d/dnsmasq reload

exit 0
```

2. 该脚本将自动拉取 anti-AD 广告过滤规则并应用到您的网络中
3. 脚本会在 OpenClash 启动时自动执行

### Bypass 特定 VLAN 流量（来源：https://leohearts.com/archives/k662c-vlan.html）

#### 步骤 1: 配置 VLAN Bypass
1. 在开发者选项的编辑框中添加以下配置：
```bash
# bypass某些vlan的流量
bypass_iifname="eth0.8"
nft insert rule inet fw4 openclash iifname $bypass_iifname counter return
nft insert rule inet fw4 openclash_mangle iifname $bypass_iifname counter return
nft insert rule inet fw4 openclash_mangle_v6 iifname $bypass_iifname counter return
```

2. 将 `eth0.8` 替换为您要绕过的 VLAN 接口名称
3. 此配置将使指定 VLAN 的流量绕过 OpenClash 代理

#### 步骤 2: 应用配置
1. 保存开发者选项设置
2. 重启 OpenClash 服务以应用更改
3. 验证特定 VLAN 的流量是否成功绕过代理

### 注意事项
- 开发者选项中的脚本具有较高的权限，请谨慎修改
- 确保添加的命令语法正确，错误的配置可能导致 OpenClash 无法正常启动
- 对于 VLAN Bypass 功能，请确保您了解网络拓扑结构再进行配置
