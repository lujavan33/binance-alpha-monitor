# Binance Alpha Monitor

币安 Alpha 首发上新自动监控系统。实时抓取币安公告，AI 分析项目叙事和投资机构，自动评级后推送到 Telegram。

## 功能

- 📡 **公告监控** — 每 30 秒轮询币安官方公告 API，零延迟发现新项目
- 🧠 **AI 分析** — Claude Sonnet 自动提取叙事方向、投资机构、是否币安亲儿子
- 📊 **代币经济** — CoinGecko 自动查询 FDV、流通市值、开盘价、流通比例
- 🏆 **智能评级** — S/A/B/C 四级评级，基于 52 个历史项目回测调优
- 🔔 **TG 推送** — 发现即推、倒计时提醒、上线瞬间、30min 周期追踪、异动告警
- 🔍 **智能过滤** — 自动排除期货合约、杠杆、维护公告等非 Alpha 首发

## 评级规则

| 级别 | 条件 | 图标 |
|------|------|------|
| **S 级** | 币安亲儿子 / 热叙事+Tier1 VC / 微盘+Tier1 | 🟢🟢🟢 |
| **A 级** | Tier1 VC + 小盘 | 🟡 |
| **B 级** | 中盘普通项目 | 🟠 |
| **C 级** | 大盘/弱信号 | ⚪ |

### S 级五条路径
1. 币安亲儿子（YZi Labs / Binance Labs / CZ 站台）
2. 热叙事（DeFi Perp / AI Agent / DeFAI / ZK）+ Tier1 VC
3. ≥2 家 Tier1 VC + 中盘（FDV < $300M）
4. Tier1 VC + 微盘（MCap < $10M）
5. 热叙事 + 微盘（FDV < $50M）

## 推送示例

```
🟢🟢🟢 Alpha 首发 · $CHIP 🟢🟢🟢
📋 S 级(必研究)

Chip
💡 基于Arbitrum/Base/Ethereum的DeFi协议，专注于稳定币发行与治理，获得YZi Labs投资
🏷 叙事: stablecoin

📊 FDV: $1.2B
📊 流通市值: $242.0M
💰 预估开盘价: $0.1198
📦 初始流通: 20.0%

🏛 机构
  ⭐ YZi Labs

🔥 币安亲儿子

🎯 币安亲儿子(YZi/Binance Labs/CZ)
```

## 推送节奏

1. **首次发现** → 立即推送完整分析卡片
2. **T-3h** → 倒计时提醒
3. **T-30min** → 准备下单提醒
4. **上线瞬间** → 实际开盘价 vs 预估
5. **上线后 30min × 4** → 价格跟踪 + 建议
6. **异动告警** → 翻倍/腰斩即时通知

## 快速开始

### 1. 安装依赖

```bash
pip install -r requirements.txt
```

### 2. 配置环境变量

```bash
cp .env.example .env
# 编辑 .env，填入你的 TG Bot Token 和 Chat ID
```

脚本启动时会自动读取仓库根目录下的 `.env` 和 `.env.systemd`。
如果两边都配置了同一个变量，优先级是：

1. 当前 shell / systemd 已注入的环境变量
2. `.env`
3. `.env.systemd`

如果你用 systemd，也可以继续保留 `.env.systemd`：

```bash
cp .env.example .env.systemd
```

**必填：**
- `TG_BOT_TOKEN` — Telegram Bot Token（找 @BotFather 创建）
- `TG_CHAT_ID` — 你的 Chat ID

**可选：**
- `ANTHROPIC_API_KEY` — 填了用 AI 分析叙事，不填降级为规则引擎（也能用，只是叙事分析没那么准）

### 3. 运行

```bash
# 直接运行
python3 alpha_monitor.py

# 或用 systemd 后台运行（推荐）
sudo cp alpha-monitor.service /etc/systemd/system/
# 编辑 service 文件中的路径
sudo systemctl daemon-reload
sudo systemctl enable --now alpha-monitor
```

### 4. 验证

启动后你的 Telegram 应该收到：
```
🎉 Alpha Monitor 启动成功
等待首个项目触发...
```

## 架构

```
alpha_monitor.py (单文件，~1000行)
├── 公告监听器 — 轮询币安 CMS API (30s)
├── 聚合工作者 — CoinGecko + LLM 分析 (15s)
├── 评级引擎 — S/A/B/C 规则 (52项目回测)
├── 推送模块 — Telegram Bot API
├── 上线监控 — 倒计时 + 周期追踪 + 异动检测
└── SQLite — 项目去重 + 推送记录
```

## 运行成本

| 项目 | 费用 |
|------|------|
| 币安公告 API | 免费 |
| CoinGecko API | 免费 |
| Telegram Bot | 免费 |
| Claude Sonnet（可选）| ~$0.01/项目，约 $0.5/月 |
| 链上 Gas | **零**（纯读取，无链上操作）|

**不配置 AI 则完全免费运行。**

## 注意事项

- 本项目仅做信息监控和推送，**不包含自动交易功能**
- 评级仅供参考，不构成投资建议
- CoinGecko 免费 API 有频率限制（约 10-30 次/分钟），系统已内置重试逻辑
- 币安公告 API 需要 User-Agent 头，系统已自动处理

## License

MIT
