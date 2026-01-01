# Xueqiu MCP

<div align="center">

**基于雪球 API 的 MCP 服务器**

通过 Claude 或其他 AI 助手轻松获取中国股票市场数据

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python Version](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/downloads/)
[![MCP](https://img.shields.io/badge/MCP-Protocol-green.svg)](https://modelcontextprotocol.io/)

</div>

## 📖 目录

- [简介](#-简介)
- [功能特性](#-功能特性)
- [快速开始](#-快速开始)
- [安装](#-安装)
- [配置](#-配置)
- [使用指南](#-使用指南)
- [可用工具](#-可用工具)
- [常见问题](#-常见问题)
- [贡献指南](#-贡献指南)
- [许可证](#-许可证)

## 💡 简介

Xueqiu MCP 是一个基于 [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) 的服务器，封装了 [pysnowball](https://github.com/uname-yang/pysnowball) 库，为 Claude 等 AI 助手提供访问中国股票市场数据的能力。

### 核心特性

- 🚀 **即插即用**：通过 MCP 协议快速集成到 Claude Desktop
- 📊 **全面数据**：支持股票行情、财务报表、资金流向、基金数据等 50+ 数据接口
- 🔄 **自动处理**：智能转换时间戳格式，提供可读性强的数据输出
- 🛡️ **类型安全**：完整的参数类型定义和默认值

## ✨ 功能特性

### 股票数据
- 实时行情和详细报价
- K 线数据获取
- 业绩预告和机构评级
- 资金流向分析（当日/历史）
- 大宗交易和融资融券数据

### 财务报表
- 利润表
- 资产负债表
- 现金流量表
- 主要财务指标
- 主营业务构成

### 股东数据
- 十大股东信息
- 股东人数统计
- 机构持仓变化

### 指数数据
- 指数基本信息和详情
- 指数权重股前十
- 指数收益表现（7天/30天/90天）

### 基金数据
- 基金详情和基本信息
- 基金增长和历史净值
- 基金业绩表现和资产配置
- 基金经理信息

### 其他功能
- 自选股列表管理
- 组合净值和交易历史
- 深港通/沪港通北向数据
- 可转债信息
- 股票关键词搜索

## 🚀 快速开始

### 前置要求

- Python 3.11 或更高版本
- [uv](https://github.com/astral-sh/uv) （推荐的包管理器）
- 雪球账号 Token（见[配置](#配置)部分）

### 三步启动

```bash
# 1. 克隆仓库
git clone https://github.com/bevisy/xueqiu_mcp.git
cd xueqiu_mcp

# 2. 安装依赖
uv venv && uv pip install -e .

# 3. 配置 Token 并启动服务
echo 'XUEQIU_TOKEN="your_token_here"' > .env
uv run main.py
```

## 📦 安装

### 使用 uv（推荐）

```bash
# 创建虚拟环境并安装
uv venv
uv pip install -e .
```

### 使用 pip

```bash
# 创建虚拟环境
python -m venv .venv
source .venv/bin/activate  # Linux/macOS
# 或 .venv\Scripts\activate  # Windows

# 安装依赖
pip install -e .
```

## ⚙️ 配置

### 获取雪球 Token

雪球 Token 是访问雪球 API 的必要凭证。获取步骤：

1. 登录 [雪球网](https://xueqiu.com/)
2. 打开浏览器开发者工具（F12）
3. 进入 Network 标签，找到任意请求
4. 复制请求头中的 `Cookie` 值
5. 提取 `xq_a_token` 和 `u` 参数

详细教程请参考 [pysnowball 文档](https://github.com/uname-yang/pysnowball/blob/master/how_to_get_token.md)。

### 配置 Token

在项目根目录创建 `.env` 文件：

```bash
# 方式一：直接创建
cat > .env << EOF
XUEQIU_TOKEN="xq_a_token=xxxxx;u=xxxx"
EOF

# 方式二：使用 echo
echo 'XUEQIU_TOKEN="xq_a_token=xxxxx;u=xxxx"' > .env
```

**安全提示**：请勿将 `.env` 文件提交到版本控制系统。

## 🎯 使用指南

### 集成到 Claude Desktop

#### 方式一：使用命令行工具

```bash
claude mcp add-json xueqiu-mcp '{
  "args": [
    "--directory",
    "/absolute/path/to/xueqiu_mcp",
    "run",
    "main.py"
  ],
  "command": "uv"
}'
```

#### 方式二：手动配置

编辑 Claude Desktop 配置文件（`~/.claude/desktop_config.json` 或 `~/Library/Application Support/Claude/claude_desktop_config.json`）：

```json
{
  "mcpServers": {
    "xueqiu-mcp": {
      "command": "uv",
      "args": [
        "--directory",
        "/absolute/path/to/xueqiu_mcp",
        "run",
        "main.py"
      ]
    }
  }
}
```

**注意**：请将路径替换为实际的项目绝对路径。

### 使用示例

配置完成后，重启 Claude Desktop，即可在对话中使用：

```
用户：帮我查询万科A的实时行情
Claude：[调用 quote_detail 工具获取数据]

用户：分析贵州茅台最近30天的K线数据
Claude：[调用 kline 工具并分析]
```

## 🛠️ 可用工具

### 股票行情

| 工具名称 | 功能描述 | 主要参数 |
|---------|---------|---------|
| `quotec` | 获取股票基本行情 | `stock_code` |
| `quote_detail` | 获取详细行情数据 | `stock_code` |
| `pankou` | 获取实时分笔数据 | `stock_code` |
| `kline` | 获取 K 线数据 | `stock_code`, `days` |

### 财务数据

| 工具名称 | 功能描述 | 主要参数 |
|---------|---------|---------|
| `indicator` | 业绩报表数据 | `stock_code`, `is_annals`, `count` |
| `income` | 利润表数据 | `stock_code`, `is_annals`, `count` |
| `balance` | 资产负债表数据 | `stock_code`, `is_annals`, `count` |
| `cash_flow` | 现金流量表数据 | `stock_code`, `is_annals`, `count` |

### 股东信息

| 工具名称 | 功能描述 | 主要参数 |
|---------|---------|---------|
| `top_holders` | 十大股东数据 | `stock_code`, `circula` |
| `holders` | 股东人数数据 | `stock_code` |
| `org_holding_change` | 机构持仓变化 | `stock_code` |

### 指数数据

| 工具名称 | 功能描述 | 主要参数 |
|---------|---------|---------|
| `index_basic_info` | 指数基本信息 | `index_code` |
| `index_details_data` | 指数详细信息 | `index_code` |
| `index_weight_top10` | 指数权重股前十 | `index_code` |
| `index_perf_7/30/90` | 指数收益表现 | `index_code` |

### 基金数据

| 工具名称 | 功能描述 | 主要参数 |
|---------|---------|---------|
| `fund_detail` | 基金详细信息 | `fund_code` |
| `fund_info` | 基金基本信息 | `fund_code` |
| `fund_nav_history` | 基金历史净值 | `fund_code` |
| `fund_manager` | 基金经理信息 | `fund_code` |

### 其他工具

- `suggest_stock`：关键词搜索股票
- `watch_list`：获取自选股列表
- `capital_flow`：资金流向数据
- `northbound_shareholding_sh/sz`：深港通/沪港通数据
- `convertible_bond`：可转债信息

完整工具列表请参考 `main.py` 源码或通过 Claude Desktop 查看工具面板。

## 📸 使用示例

### 在 Cursor 中使用

![Cursor MCP 示例](./images/cursor_mcp.png)

### 在 Claude Desktop 中使用

![Claude MCP 示例](./images/claude_mcp.png)

## ❓ 常见问题

### Q: 如何获取股票代码？

A: 可以使用 `suggest_stock` 工具搜索：
- 深圳股票：`SZ` 前缀（如 `SZ000002`）
- 上海股票：`SH` 前缀（如 `SH600000`）

### Q: Token 失效怎么办？

A: Token 有效期有限，失效后需重新获取。建议定期更新 Token。

### Q: 支持哪些数据频率？

A: 取决于雪球 API 的限制，实时数据基于雪球的更新频率。

### Q: 能否获取历史数据？

A: 支持 K 线、资金流向、基金净值等历史数据，具体可调用天数请参考各工具参数。

## 🤝 贡献指南

欢迎贡献代码、报告问题或提出建议！

1. Fork 本仓库
2. 创建特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 开启 Pull Request

## 📄 许可证

本项目采用 [MIT License](./LICENSE) 开源协议。

## 🙏 致谢

- [pysnowball](https://github.com/uname-yang/pysnowball) - 雪球股票数据接口的 Python 封装
- [fastmcp](https://github.com/jlowin/fastmcp) - MCP 服务框架
- [Model Context Protocol](https://modelcontextprotocol.io/) - AI 与工具交互的标准协议

## 📮 联系方式

- Issues: [GitHub Issues](https://github.com/bevisy/xueqiu_mcp/issues)
- Discussions: [GitHub Discussions](https://github.com/bevisy/xueqiu_mcp/discussions)

---

<div align="center">

**如果这个项目对您有帮助，请给一个 ⭐️ Star！**

Made with ❤️ by [bevisy](https://github.com/bevisy)

</div>