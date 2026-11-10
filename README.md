# Surge 加密货币交易所分流规则

一份开箱即用的 Surge 分流规则（RULE-SET 远程规则集格式），覆盖主流加密货币交易所的**网页端**与**手机 App**（API / WebSocket / CDN 静态资源域名）。

Quantumult X 版见姊妹仓库：[quantumultx-crypto-rules](https://github.com/carlleilzj/quantumultx-crypto-rules)

## 文件说明

| 文件 | 用途 |
| --- | --- |
| `rules/CryptoExchanges.list` | **全量合并版（推荐）**：全部主流交易所 + 其他常用交易所 + DEX，一条 RULE-SET 全部搞定 |
| `rules/Binance.list` / `OKX.list` / `Bybit.list` / … | 分交易所版：每个大所一个文件，可给每个交易所指定不同策略组 |
| `rules/OtherExchanges.list` | 其他常用交易所（Upbit / BingX / CoinEx / BloFin / Deepcoin 等）+ DEX / 预测市场 |
| `rules/CryptoTools.list` | 行情（CoinMarketCap / CoinGecko / TradingView）、钱包、区块浏览器 / RPC |
| `Surge-RuleSet.conf` | 可直接粘贴到 Surge 配置 `[Rule]` 区的引用片段（含两种用法） |

## 用法一：全量订阅（推荐，最简单）

在 Surge 配置的 `[Rule]` 区加入一行（放在你的 `FINAL` 规则之前）：

```
RULE-SET,https://raw.githubusercontent.com/carlleilzj/surge-crypto-rules/main/rules/CryptoExchanges.list,Snell-v6
```

- 行尾的 `Snell-v6` 是**策略组名**，改成你自己配置里已有的策略组 / 节点名即可（例如 `Proxy`、`🚀 节点选择`）。
- Surge 会按远程规则集的更新机制自动拉取新版；之后改规则只需推送本仓库，客户端自动同步。
- 工具类（行情 / 钱包 / 浏览器）如需一起分流，再加一行：

```
RULE-SET,https://raw.githubusercontent.com/carlleilzj/surge-crypto-rules/main/rules/CryptoTools.list,Snell-v6
```

## 用法二：按交易所分别指定节点（进阶）

先在 `[Proxy Group]` 里为每个交易所建一个同名策略组，再在 `[Rule]` 区逐条引用：

```
[Proxy Group]
BINANCE = select, JP-Snell-v6, US-Snell-v6
OKX = select, JP-Snell-v6, US-Snell-v6
BYBIT = select, JP-Snell-v6, US-Snell-v6

[Rule]
RULE-SET,https://raw.githubusercontent.com/carlleilzj/surge-crypto-rules/main/rules/Binance.list,BINANCE
RULE-SET,https://raw.githubusercontent.com/carlleilzj/surge-crypto-rules/main/rules/OKX.list,OKX
RULE-SET,https://raw.githubusercontent.com/carlleilzj/surge-crypto-rules/main/rules/Bybit.list,BYBIT
```

分交易所文件清单：Binance、OKX、Bybit、Coinbase、Kraken、KuCoin、Bitget、Gate、HTX、MEXC、Bitfinex、CryptoCom、Gemini、BitMEX、Deribit、Bitstamp、Robinhood，路径均为 `rules/<名称>.list`。

## 覆盖清单（摘要）

- **主流交易所**：币安、欧易 OKX、Bybit、Coinbase、Kraken、KuCoin、Bitget、Gate、HTX（原火币）、MEXC、Bitfinex、Crypto.com、Gemini、BitMEX、Deribit、Bitstamp、Robinhood。
- **其他常用**：Upbit、Bithumb、CoinEx、BingX、XT、LBank、Phemex、WhiteBIT、BitMart、Poloniex、BloFin、Deepcoin、Toobit、bitFlyer、Coincheck。
- **DEX / 预测市场**：Hyperliquid、dYdX、GMX、Aster、Polymarket。
- **工具类**（CryptoTools）：CoinMarketCap、CoinGecko、TradingView；MetaMask、Trust Wallet、Phantom、imToken 等钱包；Etherscan、BscScan、Solscan、Infura、Alchemy 等浏览器 / RPC。

每个交易所同时列出主站、备用站与 CDN 静态域；`DOMAIN-SUFFIX` 天然匹配 `www.*` / `api.*` / `ws.*` 等全部子域名。Bybit 另有 `DOMAIN-KEYWORD,bybit` 兜底，覆盖其随机备用域名。

## 常见问题

**Q：部分 App 流量没有命中规则？**
个别交易所 App 会绕过域名直连 IP，域名规则无法命中。可在 Surge 的「最近请求」里找到该 App 实际请求的域名 / IP，把域名发给我补进规则；IP 直连则需另加 `IP-CIDR` 规则。

**Q：提示策略组不存在 / 规则报错？**
RULE-SET 行尾的策略组名必须是你配置里**已存在**的策略组或节点名，拼写（含 emoji）须完全一致。

**Q：想让某个交易所换节点？**
用法二下直接改对应策略组的成员即可，无需改动规则文件。

## 注意事项

- 部分交易平台在部分司法辖区受到限制，请遵守当地法律法规。本规则仅作网络分流的技术配置，不构成任何投资建议。
- 本规则只新增交易所相关匹配，不影响你原有配置中「国内直连、境外代理」的兜底逻辑；请确保 RULE-SET 行位于 `FINAL` 规则之前。
