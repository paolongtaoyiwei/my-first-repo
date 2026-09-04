# 自动挖洞脚本 / 一站式漏洞挖掘工作流 · 可复刻仓库清单

> 本清单于 2026-08-29 通过 GitHub API 逐一核实（含 star 数、语言、描述），均为可 `git clone` 复刻的开源仓库。
> 面向 SRC / 众测 / 渗透测试的自动化资产发现 → 漏洞扫描 → 验证全流程。

---

## 一、一体化自动化框架（一站式工作流，优先复刻）

| 仓库 | Star | 语言 | 说明 |
|---|---|---|---|
| [blacklanternsecurity/bbot](https://github.com/blacklanternsecurity/bbot) | 10.5k | Python | 递归式互联网扫描器，子域名→端口→Web→漏洞全自动串联，一站式打点 |
| [six2dez/reconftw](https://github.com/six2dez/reconftw) | 8.1k | Shell | 经典 recon 框架，自动跑 subfinder/amass/httpx/nuclei 等一整套 |
| [j3ssie/osmedeus](https://github.com/j3ssie/osmedeus) | 6.5k | Go | 现代化安全编排引擎，一条命令完成全流程侦察+扫描 |
| [pry0cc/axiom](https://github.com/pry0cc/axiom) | 4.4k | Shell | 分布式扫描基础设施，把 nmap/ffuf/masscan/nuclei 等分发到云端 |
| [smicallef/spiderfoot](https://github.com/smicallef/spiderfoot) | 21.7k | Python | OSINT 自动化侦察，攻击面测绘 |
| [owasp-amass/amass](https://github.com/owasp-amass/amass) | 15.1k | Go | 深度攻击面测绘与资产发现 |
| [lanmaster53/recon-ng](https://github.com/lanmaster53/recon-ng) | 5.9k | Python | 模块化开源情报(OSINT)收集框架 |
| [codingo/Interlace](https://github.com/codingo/Interlace) | 1.3k | Python | 把单线程工具变成多线程批处理，支持 CIDR/glob |
| [binganao/Flow](https://github.com/binganao/Flow) | 41 | Shell | 基于 Docker 的自动化 SRC 打点工作流（subfinder→naabu→httpx→nuclei/xray） |
| [who0xac/Pinakastra](https://github.com/who0xac/Pinakastra) | 63 | Go | AI 驱动的侦察+漏洞利用框架（子域名/XSS/SQLi/SSRF/IDOR） |
| [donrich99/eclipse](https://github.com/donrich99/eclipse) | 2 | Python | AI 安全框架，30+ 模块（SQLi/XSS/WAF/子域名/端口/CMS/API） |

---

## 二、资产发现 / 子域名收集

| 仓库 | Star | 语言 | 说明 |
|---|---|---|---|
| [projectdiscovery/subfinder](https://github.com/projectdiscovery/subfinder) | 14.4k | Go | 快速被动子域名枚举 |
| [projectdiscovery/httpx](https://github.com/projectdiscovery/httpx) | 10.4k | Go | 高速 HTTP 探针工具包 |
| [projectdiscovery/naabu](https://github.com/projectdiscovery/naabu) | 6.2k | Go | 快速端口扫描 |
| [projectdiscovery/dnsx](https://github.com/projectdiscovery/dnsx) | 2.9k | Go | 多用途 DNS 查询工具 |
| [projectdiscovery/katana](https://github.com/projectdiscovery/katana) | 17.4k | Go | 新一代爬虫框架 |
| [tomnomnom/assetfinder](https://github.com/tomnomnom/assetfinder) | 3.7k | Go | 从公开源发现子域名 |
| [lc/gau](https://github.com/lc/gau) | 5.1k | Go | 从 OTX/Wayback/Common Crawl 拉取历史 URL |
| [blechschmidt/massdns](https://github.com/blechschmidt/massdns) | 3.6k | C | 高性能 DNS 批量解析（子域名爆破） |
| [infosec-au/altdns](https://github.com/infosec-au/altdns) | 2.5k | Python | 子域名排列/变异生成与解析 |

---

## 三、漏洞扫描 / 内容发现

| 仓库 | Star | 语言 | 说明 |
|---|---|---|---|
| [projectdiscovery/nuclei](https://github.com/projectdiscovery/nuclei) | 31.0k | Go | 基于 YAML 模板的快速漏洞扫描引擎（挖洞核心） |
| [projectdiscovery/nuclei-templates](https://github.com/projectdiscovery/nuclei-templates) | 12.9k | JS | 社区维护的 nuclei 漏洞模板库（海量 CVE/PoC） |
| [chaitin/xray](https://github.com/chaitin/xray) | 11.7k | Vue | 长亭安全评估工具，常见 Web 漏洞扫描 + 自定义 POC |
| [sqlmapproject/sqlmap](https://github.com/sqlmapproject/sqlmap) | 38.4k | Python | 自动化 SQL 注入检测与利用 |
| [ffuf/ffuf](https://github.com/ffuf/ffuf) | 16.6k | Go | 高速 Web 模糊测试（目录/参数发现） |
| [maurosoria/dirsearch](https://github.com/maurosoria/dirsearch) | 14.7k | Python | Web 路径扫描器 |
| [epi052/feroxbuster](https://github.com/epi052/feroxbuster) | 8.1k | Rust | 递归内容发现工具 |
| [zan8in/afrog](https://github.com/zan8in/afrog) | 4.4k | Go | 面向众测/红队的 PoC 扫描框架 |
| [knownsec/pocsuite3](https://github.com/knownsec/pocsuite3) | 3.9k | Python | 知道创宇 404 团队开源 PoC 框架 |

---

## 四、CVE/PoC 情报 与 漏洞环境

| 仓库 | Star | 语言 | 说明 |
|---|---|---|---|
| [trickest/cve](https://github.com/trickest/cve) | 8.0k | HTML | 汇集最新 CVE 及对应 PoC，持续更新 |
| [vulhub/vulhub](https://github.com/vulhub/vulhub) | 21.2k | Dockerfile | 基于 Docker 的一键漏洞复现环境 |

---

## 五、工具合集 / 进一步检索

| 仓库 | Star | 说明 |
|---|---|---|
| [vavkamil/awesome-bugbounty-tools](https://github.com/vavkamil/awesome-bugbounty-tools) | 6.2k | 众测工具精选列表（持续更新，可继续挖） |

---

## 推荐「一站式」最小工作流

```bash
# 1) 子域名枚举
subfinder -d target.com -all -o subs.txt
# 2) 存活探测 + 端口
cat subs.txt | httpx -silent -o alive.txt
naabu -l alive.txt -top-ports 1000 -o ports.txt
# 3) 历史 URL + 爬虫
cat alive.txt | gau --threads 20 > urls.txt
katana -list alive.txt -d 5 -jc -o crawl.txt
# 4) 漏洞扫描（核弹级模板库）
nuclei -l alive.txt -severity low,medium,high,critical -o vulns.txt
# 5) SQL 注入专项
sqlmap -m urls.txt --batch
```

> 说明：`chaitin/xray` 官方仓库已归档/闭源化，社区版仍可复刻使用；`binganao/Flow` 已把以上工具封装成 Docker 一键工作流，适合快速上手。

---

*最后更新：2026-08-29 · 数据源：GitHub REST API · 由 DSH agent 生成*
