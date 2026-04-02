

# Cloudflare 优选IP 收集器

一个基于 Cloudflare Workers 的优选 CF IP 地址收集与测速工具，自动从多个公开来源收集 Cloudflare IP 地址，并提供可视化界面和测速功能。

## 🌟 功能特点
🎯 核心功能
1. IP采集功能
从多个来源网站自动采集Cloudflare IP地址

支持9个不同的数据源（ip.164746.xyz、ip.haogege.xyz等）

批量并行请求，提高采集效率

智能解析HTML、JSON、文本等多种格式

2. IP信息提取
延迟：从来源提取或测试获取延迟数据

带宽：提取带宽信息（MB/s）

运营商：自动识别电信、联通、移动或其他运营商

数据源：记录IP来源，便于追溯

3. 智能排序筛选
排序规则：带宽优先（降序）+ 延迟升序

优质IP筛选：每个运营商前4个，总共20个优质IP

运营商均衡：确保移动、电信、联通都有覆盖

4. 测速功能
使用cdnjs.cloudflare.com进行真实测速

测试延迟和带宽

批量测速，支持自定义测试数量

测速结果自动保存为优质IP

🌐 Web界面功能
主要操作按钮
立即更新：从所有来源重新采集IP

下载优质IP：下载纯文本格式的优质IP列表

开始测速：手动触发Worker测速

ITDog测速：跳转到ITDog网站进行第三方测速

刷新状态：刷新页面数据

IP展示
显示IP地址、运营商、延迟、带宽

按运营商分类统计

一键复制单个或全部IP

响应式设计，支持移动端

📊 数据处理
存储策略
cloudflare_ips_detail：存储完整IP信息（含延迟/带宽/运营商）

cloudflare_ips：仅存储IP地址列表

cloudflare_fast_ips：存储筛选后的优质IP

定时任务
支持定时自动更新（通过scheduled事件）

后台自动采集和更新IP数据




## 🚀 快速开始

### 前置要求

- Cloudflare 账户
- Workers 权限
- KV 命名空间（用于存储 IP 数据）

### 部署步骤

📦 部署步骤
通过 Web 界面部署（推荐）
登录 Cloudflare

访问 dash.cloudflare.com

登录您的账号

进入 Workers 页面

点击左侧菜单的 "Workers 和 Pages"

点击 "创建应用程序" → "从Hello World!开始"


配置 KV 命名空间

左侧菜单 → 存储和数据库 → Workers KV
点击 "创建命名空间"
名称：IP_STORAGE
点击 "创建"

绑定 KV 到 Worker

返回Workers 和 Pages 编辑页面 → 选择刚刚创建的项目 → 点击 "绑定" → “添加绑定" → 找到 "KV 命名空间"，填入以下内容

变量名称：IP_STORAGE

KV 命名空间：选择刚创建的 IP_STORAGE

点击 "添加绑定"

点击”编辑代码“，删除里面所有内容，然后复制github上“workers.js”的全部内容去”编辑代码“的框中

点击"部署"
点击”访问“，看是否能正常访问，能正常访问就可以返回做定时自动更新任务的设置。

## ⏰ 定时自动更新任务

点击 "Workers 和 Pages"

找到您刚刚创建的项目，点击 "设置" → "触发事件" → “添加” → "Cron 触发器"
选择执行 Worker 的频率，3小时或者6小时一次就差不多，然后点击“添加”
也可以选择“Cron 表达式”，然后填入时间，假如是每6小时执行一次自动更新就填入“0 */6 * * *”
以下是参考，
- 每6小时：0 */6 * * *
- 每3小时：0 */3 * * *
- 每1小时：0 * * * *
- 每天0点：0 0 * * *
- 每30分钟：*/30 * * * *

推荐：0 */5 * * *  (每4小时更新一次)

## 🌐 添加域名访问
步骤1：准备域名
确保您的域名已接入 Cloudflare（DNS 托管在 Cloudflare）

步骤2：添加路由
1. 登录 Cloudflare
2. 进入 Workers 和 Pages"
3. 点击您的 Worker 名称
4. 点击 "设置" → "域和路由" → “添加” → “自定义域” → 填入子域名 → “添加域”

## 📖 使用方法

### Web 界面

访问部署后的 Worker 地址即可使用完整功能：

- **查看 IP 列表**：浏览所有收集到的 Cloudflare IP 地址
- **一键测速**：批量测试所有 IP 的延迟，自动排序
- **导出数据**：下载 TXT 格式的 IP 列表
- **ITDog 集成**：复制 IP 列表到 ITDog 进行更详细的测试

### API 接口

- `GET /` - 主页面
- `GET /ips` 或 `GET /ip.txt` - 获取纯文本 IP 列表
- `GET /raw` - 获取原始 JSON 数据
- `POST /update` - 手动触发 IP 更新
- `GET /speedtest?ip=<ip>` - 测试指定 IP 的速度
- `GET /itdog-data` - 获取 ITDog 格式数据

## ⚙️ 配置说明

### 数据来源

项目从多个公开的 Cloudflare IP 数据源自动收集，包括：


- ip.164746.xyz
- ip.haogege.xyz
- api.uouin.com
- addressesapi.090227.xyz
- ip.flares.cloud
- v2rayssr.com
- cf.vvhan.com
- www.wetest.vip


### 环境变量

无需额外环境变量，所有配置通过代码管理。

## 🛠️ 开发

### 本地开发

```bash
# 安装依赖
npm install

# 启动本地开发服务器
npx wrangler dev

# 部署到生产环境
npx wrangler deploy
```

### 项目结构

```
├── cfip.js              # 主 Worker 代码
├── wrangler.toml        # Wrangler 配置
├── package.json         # 项目依赖
└── README.md           # 项目说明
```

## 📊 技术栈

- **运行时**：Cloudflare Workers
- **存储**：Cloudflare KV
- **前端**：原生 HTML/CSS/JavaScript
- **部署**：Wrangler



## ⚠️ 免责声明

本项目仅用于学习和研究目的，请勿用于商业用途或违反相关服务条款。使用者需自行承担相关风险。


如果这个项目对你有帮助，请给个 ⭐️ 支持一下！


