# OpenClash 配置指南

## 目录
- [合并插件设置和覆写设置（手动操作）](#合并插件设置和覆写设置手动操作)
- [自定义规则配置](#自定义规则配置)
- [订阅 INI 自定义模板](#订阅-ini-自定义模板)
- [开发者选项配置](#开发者选项配置)

## 合并插件设置和覆写设置（手动操作）

为确保 OpenClash 按照预设的自定义配置正确运行，以下插件设置和覆写设置中的选项**必须**按照指定内容进行手动配置：

### 插件设置中的手动操作

请在 OpenClash 的"插件设置"页面，确保以下选项配置正确：

*   **模式设置**:
    *   运行模式: **必须选择** 【Fake-IP（TUN-混合）模式【UDP-TUN，TCP-转发】】
    *   网络栈类型: **必须选择** 【Mixed（仅 Meta 内核）】
    *   代理模式: **必须选择** 【代理模式】
*   **大陆白名单订阅**:
    *   自动更新: **必须勾选**
    *   更新时间(每周): **必须选择** 【周五】
    *   更新时间(每天): **必须选择** 【18:00】
    *   大陆IP段更新 URL: **必须选择** 【Hackl0us-CN-CIDR-fastly-jsdelivr(18MB大文件，请确保足够的剩余空间)】
    *   大陆IPv6段更新 URL: **必须选择** 【Clang-CN-IPV6(默认)】
*   **GEO数据库订阅**:
    *   自动更新 GeoIP MMDB 数据库: **必须选择** 【Alecthw-Version(全部国家且且包含官方全部信息数据)】
    *   自动更新 GeoIP Dat 数据库: **必须选择** 【Loyalsoldier-testingcf-jsdelivr-Version(默认)】
    *   自动更新 GeoSite 数据库: **必须选择** 【Loyalsoldier-testingcf-jsdelivr-Version(默认)】
    *   更新时间(每周) (针对所有GEO数据库): **必须选择** 【周五】
    *   更新时间(每天) (针对所有GEO数据库): **必须选择** 【18:00】

### 覆写设置中的手动操作

请在 OpenClash 的"覆写设置"页面中，确保以下选项配置正确：

*   **DNS 设置**:
    *   自定义上游 DNS 服务器: **必须勾选**
    *   追加上游 DNS: **必须勾选**
    *   追加默认 DNS: **必须勾选**
    *   Fake-IP 持久化: **必须勾选**
    *   Fake-IP-Filter: **必须勾选** 并添加以下内容:
        ```
        geosite:cn
        geosite:category-games
        geosite:apple
        ```
    *   NameServer、FallBack、Default-NameServer: **必须全部不勾选**

*   **Meta 设置** (常规设置):
    *   启用 TCP 并发: **必须勾选**
    *   启用统一延迟: **必须勾选**
    *   Geodata 数据加载方式: **必须选择** 【标准模式】
    *   启用 GeoIP Dat 版数据库: **必须不勾选**
    *   启用流量（域名）探测: **必须勾选**
    *   探测（嗅探）纯 IP 连接: **必须勾选**
    *   自定义流量探测（嗅探）设置:
        *   `skip-dst-address`: **必须包含** 以下条目 (用于跳过对内网地址的嗅探):
            ```yaml
            # 对于目标ip跳过嗅探
            - 10.0.0.0/8
            ```

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
- 对于 VLAN Bypass 功能，请确保了解网络拓扑结构再进行配置
