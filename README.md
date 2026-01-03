# 鹰眼监控系统 - README

<div align="center">
  <img src="logo.jpg" alt="鹰眼监控" width="120" height="120" style="border-radius: 20px;">
  
  # 🦅 鹰眼监控 (HawkEye Monitor)
  
  **币安市场实时报警系统 | Binance Real-time Alert System**
  
  [![Python](https://img.shields.io/badge/Python-3.10+-blue.svg)](https://python.org)
  [![Telegram](https://img.shields.io/badge/Telegram-Bot-blue.svg)](https://core.telegram.org/bots)
  [![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
  [![Version](https://img.shields.io/badge/Version-1.3-orange.svg)](https://github.com/yourusername/hawkeye-monitor)
  
  [🌐 官网](https://yingyan.lol) · [🤖 Telegram Bot](https://t.me/yingyanjk_bot) · [💬 交流群](https://t.me/+mMYvl04GeTIwODdl)
  
  <img src="https://img.shields.io/badge/监控交易对-500+-brightgreen" />
  <img src="https://img.shields.io/badge/报警延迟-<1s-brightgreen" />
  <img src="https://img.shields.io/badge/运行时间-24%2F7-brightgreen" />
  
</div>

---

## 📖 简介

鹰眼监控是一个 **7×24 小时**运行的币安市场实时监控系统，通过 Telegram 机器人推送报警消息。

支持监控价格异动、现货合约差价、资金费率、成交量暴增、**巨量挂单**等多种市场异常情况，帮助交易者及时捕捉市场机会。

### ✨ 核心特性

- 🐋 **巨量挂单监控** - 实时检测订单簿巨额挂单，发现巨鲸动向
- ⚡ **升级穿透机制** - 同级别过滤噪音，级别升级立即报警
- 🌙 **夜间模式** - 自动切换重复提醒，紧急唤醒不错过行情
- 📊 **多维度监控** - 价格、差价、费率、成交量、订单簿全覆盖
- 🎯 **智能过滤** - 白名单/黑名单、成交额筛选、冷却机制
- 👥 **多用户支持** - 每个用户独立配置，互不影响

---

## 🚀 快速开始

### 环境要求

- Python 3.10+
- Telegram Bot Token

### 安装

```bash
# 克隆项目
git clone https://github.com/wfce/yingyanjiankong.git
cd yingyanjiankong

# 创建虚拟环境
python -m venv venv
source venv/bin/activate  # Linux/Mac
# 或 venv\Scripts\activate  # Windows

# 安装依赖
pip install -r requirements.txt
```

### 配置

```bash
# 复制环境变量模板
cp .env.example .env

# 编辑 .env 文件
nano .env
```

```env
# 必填：Telegram Bot Token
TELEGRAM_BOT_TOKEN=your_bot_token_here

# 可选：管理员用户ID（多个用逗号分隔）
ADMIN_USER_IDS=123456789,987654321

# 可选：SMTP 邮件配置
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your_email@gmail.com
SMTP_PASSWORD=your_app_password
```

### 运行

```bash
# 直接运行
python main.py

# 或使用 Docker
docker-compose up -d
```

---

## 📁 项目结构

```
hawkeye-monitor/
├── main.py              # 主程序入口
├── config.py            # 配置管理、用户管理
├── models.py            # 数据模型定义
├── binance_client.py    # 币安 API 客户端
├── alert_engine.py      # 报警引擎
├── notifier.py          # 通知系统（Telegram/Email）
├── telegram_bot.py      # Telegram 机器人
├── requirements.txt     # Python 依赖
├── docker-compose.yml   # Docker 配置
├── Dockerfile           # Docker 镜像
├── .env.example         # 环境变量模板
├── data/                # 用户数据目录
│   └── users.json       # 用户配置存储
└── README.md
```

---

## 🔔 报警类型

| 类型 | 图标 | 说明 | 默认阈值 |
|------|------|------|----------|
| 价格暴涨 | 🟢 | 短期内价格急剧上涨 | 1分钟 ±6% |
| 价格暴跌 | 🔴 | 短期内价格急剧下跌 | 1分钟 ±6% |
| 巨量买单 | 🐋🟢 | 订单簿出现巨额买单 | 动态阈值 |
| 巨量卖单 | 🐋🔴 | 订单簿出现巨额卖单 | 动态阈值 |
| 合约溢价 | ⬆️ | 合约价格高于现货 | ±2.5% |
| 现货溢价 | ⬇️ | 现货价格高于合约 | ±2.5% |
| 成交量暴增 | 📊 | 成交量异常放大 | 12倍 |
| 资金费率 | 💰 | 永续合约费率异常 | ±0.25% |

### 🐋 巨量挂单动态阈值

根据代币24h成交额自动调整检测阈值：

| 市值类型 | 24h成交额 | 绝对阈值 | 相对阈值 |
|----------|-----------|----------|----------|
| 小市值 | <$10M | ≥$500K | 或 20% |
| 中市值 | $10M-$100M | ≥$2M | 或 10% |
| 大市值 | $100M-$1B | ≥$5M | 或 5% |
| 超大市值 | >$1B | ≥$10M | 或 2% |

### ⚡ 四级报警等级

| 等级 | 图标 | 价格阈值 | 巨量挂单阈值 |
|------|------|----------|--------------|
| INFO | ℹ️ | ±6% | 基础阈值 |
| WARNING | ⚠️ | ±9% | ≥$5M 或 10% |
| CRITICAL | 🚨 | ±15% | ≥$20M 或 20% |
| EXTREME | 🔥 | ±30% | ≥$50M 或 50% |

---

## ⚡ 升级穿透机制

传统的冷却机制会导致在冷却期内错过重要行情。鹰眼采用**智能升级穿透**：

```
10:00  +9%  警告   ✓ 发送    # 首次触发
10:02  +10% 警告   ✗ 过滤    # 同级别，冷却中
10:03  +18% 严重   ⚡ 穿透    # 级别升级，立即发送！
10:04  +16% 严重   ✗ 过滤    # 同级别，冷却中
10:05  +35% 极端   ⚡ 穿透    # 再次升级，立即发送！
```

**效果**：减少噪音干扰的同时，确保不错过重要行情。

---

## 🤖 机器人命令

### 基础命令

| 命令 | 说明 |
|------|------|
| `/start` | 开始使用 |
| `/menu` | 打开控制面板 |
| `/status` | 系统状态 |
| `/config` | 查看当前配置 |
| `/help` | 帮助信息 |

### 排行榜

| 命令 | 说明 |
|------|------|
| `/top` | 排行榜菜单 |
| `/gainers` | 涨幅榜 |
| `/losers` | 跌幅榜 |
| `/volume` | 成交额榜 |
| `/spread` | 差价榜 |
| `/funding` | 资金费率榜 |
| `/price BTC` | 查询价格 |

### 报警管理

| 命令 | 说明 |
|------|------|
| `/pending` | 待确认报警 |
| `/confirm` | 确认报警列表 |
| `/confirm all` | 确认全部报警 |

### 监控设置

| 命令 | 说明 |
|------|------|
| `/watch` | 监控模式设置 |
| `/whitelist add BTC ETH` | 添加白名单 |
| `/whitelist del BTC` | 移除白名单 |
| `/blacklist add SHIB` | 添加黑名单 |
| `/blacklist del SHIB` | 移除黑名单 |
| `/minvol 10M` | 设置最低成交额 |
| `/minvol off` | 关闭成交额筛选 |

### 报警设置

| 命令 | 说明 |
|------|------|
| `/profile` | 灵敏度设置 |
| `/mode` | 报警模式设置 |
| `/night` | 夜间模式设置 |
| `/timezone` | 时区设置 |
| `/email xxx@email.com` | 设置邮箱 |

---

## ⚙️ 灵敏度配置

### 🟢 保守版
适合长期持有者，报警较少

| 参数 | 阈值 |
|------|------|
| 1分钟涨跌 | ±10% |
| 5分钟涨跌 | ±15% |
| 差价阈值 | ±4% |
| 巨量挂单 | ≥$1M |
| 冷却时间 | 10分钟 |

### 🟡 适中版（推荐）
适合大多数用户

| 参数 | 阈值 |
|------|------|
| 1分钟涨跌 | ±6% |
| 5分钟涨跌 | ±9% |
| 差价阈值 | ±2.5% |
| 巨量挂单 | ≥$500K |
| 冷却时间 | 5分钟 |

### 🔴 激进版
适合活跃交易者，报警频繁

| 参数 | 阈值 |
|------|------|
| 1分钟涨跌 | ±3.5% |
| 5分钟涨跌 | ±5% |
| 差价阈值 | ±1.5% |
| 巨量挂单 | ≥$300K |
| 冷却时间 | 2分钟 |

---

## 🌙 夜间模式

自动检测夜间时段，切换为**重复提醒**模式：

- 🔔 自动重复发送报警，直到确认收到
- 📧 可选自动添加邮件通知
- ⏰ 可自定义夜间时段（默认 23:00 - 07:00）
- 🔁 可配置重复间隔和次数

---

## 🐳 Docker 部署

### docker-compose.yml

```yaml
version: '3.8'

services:
  hawkeye:
    build: .
    container_name: hawkeye-monitor
    restart: unless-stopped
    environment:
      - TELEGRAM_BOT_TOKEN=${TELEGRAM_BOT_TOKEN}
      - ADMIN_USER_IDS=${ADMIN_USER_IDS}
      - SMTP_HOST=${SMTP_HOST}
      - SMTP_PORT=${SMTP_PORT}
      - SMTP_USER=${SMTP_USER}
      - SMTP_PASSWORD=${SMTP_PASSWORD}
    volumes:
      - ./data:/app/data
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"
```

### Dockerfile

```dockerfile
FROM python:3.11-slim

WORKDIR /app

# 安装依赖
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# 复制代码
COPY . .

# 创建数据目录
RUN mkdir -p /app/data

# 运行
CMD ["python", "main.py"]
```

### 启动

```bash
# 构建并启动
docker-compose up -d

# 查看日志
docker-compose logs -f

# 停止
docker-compose down
```

---

## 📦 依赖

```txt
# requirements.txt
python-telegram-bot>=20.0
aiohttp>=3.9.0
websockets>=12.0
loguru>=0.7.0
aiosmtplib>=3.0.0
python-dotenv>=1.0.0
```

---

## 📊 系统架构

```
┌─────────────────────────────────────────────────────────────┐
│                      鹰眼监控系统                            │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐     │
│  │   Binance   │───▶│    Alert    │───▶│  Notifier   │     │
│  │   Client    │    │   Engine    │    │  (TG/Email) │     │
│  └─────────────┘    └─────────────┘    └─────────────┘     │
│        │                   │                   │            │
│        │ WebSocket         │ 报警逻辑          │ 推送       │
│        ▼                   ▼                   ▼            │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐     │
│  │  价格数据   │    │  用户配置   │    │  Telegram   │     │
│  │  订单簿     │    │  冷却状态   │    │    Bot      │     │
│  │  24h统计    │    │  升级穿透   │    │   用户交互  │     │
│  └─────────────┘    └─────────────┘    └─────────────┘     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 数据流

1. **BinanceClient** - 通过 WebSocket 实时接收价格、成交量数据
2. **AlertEngine** - 检测异常、判断冷却、升级穿透、生成报警
3. **Notifier** - 发送 Telegram/Email 通知
4. **TelegramBot** - 处理用户命令、配置管理

---

## 🔧 开发

### 本地开发

```bash
# 安装开发依赖
pip install -r requirements-dev.txt

# 运行测试
pytest tests/

# 代码格式化
black .
isort .

# 类型检查
mypy .
```

### 添加新的报警类型

1. 在 `models.py` 中添加 `AlertType` 枚举值
2. 在 `config.py` 中添加阈值配置
3. 在 `alert_engine.py` 中添加检测逻辑
4. 在 `models.py` 的 `Alert.to_telegram_message()` 中添加消息格式

---

## 📝 更新日志

### v1.3 (2025-01)
- 🐋 **新增** 巨量挂单监控（买单/卖单）
- 🐋 **新增** 动态阈值（根据市值自动调整）
- 📊 **新增** 订单簿深度数据获取
- ⚙️ **新增** 巨量挂单开关

### v1.2 (2025-01)
- ⚡ **新增** 升级穿透机制
- 🌙 **新增** 夜间模式（自动重复提醒）
- 💎 **新增** 成交额筛选
- 🔇 **新增** 快捷静音（1小时/24小时）
- 🌍 **新增** 时区设置

### v1.1 (2025-01)
- 👥 **新增** 多用户支持
- ⚙️ **新增** 三档灵敏度配置
- 📧 **新增** 邮件通知
- 📊 **新增** 排行榜功能

### v1.0 (2025-01)
- 🎉 首次发布
- 📈 价格监控
- 📐 差价监控
- 💰 资金费率监控

---

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

1. Fork 本仓库
2. 创建特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 提交 Pull Request

---

## 📄 许可证

本项目采用 MIT 许可证 - 查看 [LICENSE](LICENSE) 文件了解详情。

---

## ⚠️ 免责声明

- 本项目仅供学习和研究使用
- 不构成任何投资建议
- 使用本系统产生的任何损失，作者不承担责任
- 请遵守当地法律法规

---

## 🙏 致谢

- [python-telegram-bot](https://github.com/python-telegram-bot/python-telegram-bot)
- [Binance API](https://binance-docs.github.io/apidocs/)
- [Loguru](https://github.com/Delgan/loguru)

---

<div align="center">
  
  **如果觉得有用，请给个 ⭐ Star！**
  
  Made with ❤️ by [YingYan Team](https://yingyan.lol)
  
</div>
```
