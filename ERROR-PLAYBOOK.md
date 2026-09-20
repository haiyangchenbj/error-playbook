# Agent Error Playbook — public sanitized mirror

> This file is the **public mirror** of an agent-execution error knowledge base.
> It is indexed by **operation type** (write JSON, write files, run shell, call API, git, publish, compute, layout, facts/images) — not by date — because the retrieval key for an error is the operation that failed, never the day it happened.

## Where the canonical copy lives

```
~/.workbuddy/ERROR-PLAYBOOK.md
```

The companion process-host skill (`workflow-guard-rails`) and unattended automation prompts
read the file from that exact path. To install this mirror:

```bash
mkdir -p ~/.workbuddy
cp ERROR-PLAYBOOK.md ~/.workbuddy/ERROR-PLAYBOOK.md
```

If you keep your own copy elsewhere, change the path in **one** place only: the resident pointer
in `~/.workbuddy/MEMORY.md`. Never fork this file into a second location — a second copy drifts
immediately, and a drifted error rule is worse than no rule.

## What was removed from this mirror

Personal absolute paths, host-specific usernames, a private domain, corporate-environment
references, and internal workspace layout were genericized. The operational content is unchanged.

---

# Agent 执行错误防御手册

**用途**：动手前查门禁，出错后查归因。按**操作类型**索引，不按时间、不按项目。
**维护**：每次"自己发现并修正了一个错"，必须当场追加到本文件对应章节，不能只写进当天日志。见 §0.3。
**来源**：2026-06 至 2026-09 的实际事故记录（24 个日志文件 + 30+ 个 skill 文档 + 两份长期记忆），去重后 100+ 条。

---

## §0 使用方式

### 0.1 三个触发点（强制）

| 时机 | 动作 |
| --- | --- |
| **动手前** | 按操作类型查 §2 对应门禁。写 JSON/YAML/frontmatter → §2.1；写文件 → §2.2；跑命令 → §2.3；调 API → §2.4；git → §2.5；发布 → §2.6；算数据 → §2.7；做物料 → §2.8 |
| **写完** | 过一遍 §3 写后验证。工具回执在**本环境不可信**（见 §3.0） |
| **报错时** | 先查 §1 复发榜，再查 §4 归因纪律。**禁止第一次报错就重试同一条命令** |

### 0.2 优先级

§1 复发榜（出现 ≥2 次的）> §2 门禁 > 其余。token 紧张时只读 §1 + 对应 §2 小节。

### 0.3 沉淀规则（这条决定本手册会不会失效）

一次会话里"我发现并修正了"的修正，**只活在当前 context 里**，下次会话是白纸。跨会话唯一存活物是磁盘文件。

- 修正若**没有追加进本文件**，等于没修正 —— 下次必犯，且犯得一模一样。
- 追加时必须写：现象 / 根因 / 正确做法（可执行的写法，不是"要注意"）/ 复发计数。
- 禁止只写进当天 `.workbuddy/memory/YYYY-MM-DD.md` 就当完事。日志是按**日期**组织的，下一次遇到同类错误没人会去翻 9 月的某篇日志。**本手册是按操作类型组织的，这才是能被调用的形态。**
- 连续两轮犯同一个错 → 该条加 `【复发】` 标记并上提进 §1。

### 0.4 无人值守场景：门禁必须内嵌进 automation prompt

automation 是错误发生与发现的主场景，但那里没有人类说一句话的机会 —— 任何需要触发的 skill 在那个场景都不会被加载。**沉淀闸门必须写在 prompt 里，不能指望 skill 被唤起。**

把下面这句直接复制到每个 automation 的 prompt 开头（也可压缩成最后两句）：

```
执行前按操作类型查 ~/.workbuddy/ERROR-PLAYBOOK.md §2 门禁；
写完过 §3 验证（工具回执不可信，见 §3.0 六类假成功）；报错先查 §1 复发榜，
禁止第一次报错就重试同一条命令。本轮若修正了任何错误，必须当场追加进该手册
对应章节并更新复发计数 —— 无人值守运行中没有人类会替你触发沉淀。
```

2026-09-18 登记的 automation（7 个 ACTIVE，均在 Claw / OpenClaw 环境）：日报生成、日报推送、盘前分析、盘后复盘、跑鞋市场价刷新、三 skill 扫描复扫终态确认、iddp clawhub slug rename。

**该清单与本机 WorkBuddy 环境不重合**（2026-09-20 核实）：WorkBuddy 侧另有 4 个 ACTIVE —— Data+AI 日报镜像同步（每日 18:00）、每日选题扫描（8:30）、美股开盘后持仓检查（工作日 22:30）、benjie-model 学习更新（23:30），这 4 个已内嵌门禁句。两个环境的 automation 各自独立，**每新增一条都要单独内嵌，不能指望另一环境做了就算做到**。

### 0.5 层级（谁管流程，谁管内容）

| 层 | 承担者 | 职责 |
| --- | --- | --- |
| 常驻 | `~/.workbuddy/MEMORY.md` 指针 + automation prompt 内嵌门禁句 | 每个会话、每次无人值守运行都覆盖，**不需要触发** |
| 流程宿主 | `workflow-guard-rails` skill | 决定何时检查、何时沉淀（guard #7 指向本手册） |
| 知识落点 | 本手册 | 具体错误条目，被 guard #1 / #5 / #7 查询 |

流程管时机，手册管内容。手册不知道"现在是写盘前还是写盘后"，流程不知道"这类操作有哪些坑"，两层分开才都能被复用。

**入口 skill 只有一个**：`workflow-guard-rails`。`error-playbook` 仓只放本手册，没有 SKILL.md，不承担流程（2026-09-20 更正：此前的 `workflow-guardian` 表述与现状不符）。
**本手册不在任何 skill 目录内**，路径独立于 skill —— MEMORY.md 与 automation prompt 会直接引用它，所以路径必须在 skill 被改名、删除或重新发布后依然成立。

---

## §1 高频复发榜（出现 ≥2 次，按危害排序）

| # | 错误 | 复发 | 一句话防御 | 详 |
| --- | --- | --- | --- | --- |
| 1 | 同一文件并行 Edit，全部回执 success，实际只留最后一个 | 4+ | 同文件多处改动**一律串行**，或单次 Write 全量写回 | W1 |
| 2 | 改完不复验，以为落地了其实没落地 | 4+ | 每次编辑后 grep / Read 回验关键标记串 | W2 |
| 3 | bash shim 缺 coreutils（`head`/`sed`/`dirname`/`rm` exit 127）且**管道整体 exit 0 静默失败** | 4+ | 复合命令/管道一律写成 .py 脚本文件执行；禁 `\| head`/`\| tail` | C1 |
| 4 | Windows GBK 破坏中文（print emoji 崩 / 内联中文变 `?` / Node 输出乱码） | 4+ | 中文与 emoji 只进 UTF-8 文件；`python -X utf8`；`[Console]::OutputEncoding` | C4 |
| 5 | Grep 搜 `.workbuddy/` 静默返回 No matches | 3+ | ripgrep 不进隐藏目录 → `path` 直接指向该隐藏目录 | C7 |
| 6 | 一次网络失败就下"被拦截/被墙/权限不足"的重结论 | 3+ | 先重试 + 换栈交叉验证，两个独立客户端都失败才谈环境 | N1 |
| 7 | `git push` 静默挂起（GCM 凭据窗），被误读为网络/token 问题 | 3+ | `GIT_CONFIG_GLOBAL=/dev/null GIT_CONFIG_SYSTEM=/dev/null GIT_TERMINAL_PROMPT=0 git push https://$TOKEN@github.com/...` | G1 |
| 8 | 路径形态错（`/tmp` `/c/...` vs `C:\...`）导致读不到或 CLI 报错 | 4+ | 临时文件放工作区 `.workbuddy/`；给 CLI 传 Windows 原生路径 | C2 |
| 9 | 自写校验脚本有 bug，对正确文件假报 FAIL | 3+ | 校验脚本先在 known-good 样本上跑通；FAIL 先按脚本 bug 查 | V1 |
| 10 | 断言写在写盘之后 / 断言判据与写入文本不一致 → 静默跳过 | 3+ | 断言在写盘**之前**；幂等标记必须写进插入块文本本身 | V2 |
| 11 | 结构化文本里未转义的字面量破坏语法（JSON/YAML/JS/正则） | 3+ | 见 §2.1 全套 | J1 |
| 12 | 引用价格/数据时点靠推断，标注错一天 | 2+ | 必须读 `price_as_of` 字段，不靠标题或撰写日期推 | D1 |
| 13 | 全量刷新漏行不报错；陈旧字段长期无刷新路径 | 2+ | 全量刷新逐行比对；余额类字段强制带 `as_of` | D3 |
| 14 | 字段语义变更后，引用它的断言/散文未同批更新 | 2+ | 改值即改断言，下游文本同批更新并记录旧值·新值·日期 | D4 |
| 15 | 提交后仍有进程续写文件，改动丢失 | 2+ | commit 后 `git status` 兜底，push 前再 `git status` 一次 | G3 |
| 16 | 幂等标记与实际写入文本措辞不一致 → 分支静默跳过 | 2 | 用该块独有的字符串判断，且写进插入文本 | V2 |
| 17 | `curl -o file \|\| echo 000` 被 exit 23 假象触发，拼出 `200000` | 2 | http_code 写变量后单独判空，不用 `\|\|` 追加兜底 | C6 |
| 18 | 越权改动（只让改 A，顺手改了 B） | 2 | 动手前声明触碰文件清单，收口校验实际修改 ⊆ 声明 | W5 |
| 19 | 单位/口径换算错（亿↔B 差 10 倍、字符数当字节数） | 2+ | 换算后做量级 sanity check；字节用 `len(s.encode('utf-8'))` | D2 |
| 20 | 把本地脚本行号/二手镜像当成线上真源 | 2 | 凡标"冲突/待修"必须先读回线上确认文本确实存在 | F3 |
| 21 | 写了**不可能失败**的断言/门禁（判据对任何输入都成立，或分支压根不可达） | 2 | 每条断言/门禁必须能举出一个反例用例，并**实测该反例真的 FAIL** | V3 |
| 22 | 产物的生成器**不可重跑 / 不在仓库里**（一次性脚本、日戳化、放 gitignored 目录） | 2 | 生成器放受版本控制目录、唯一写盘目标＝产物本身、自带 known-FAIL 自检 | W8 |
| 23 | **声明了某效果，但执行层不读它** —— 台账/文档写「封锁生效」，而机器判据把它跳过 | 2 | 任何「X 生效/会执行」的声明，必须**找到执行它的那行代码并实测触发**；找不到＝没生效 | V4 |
| 24 | **运行值无来源地写进脚本**（凭记忆手填价格/汇率/档位），且用 `change`/`prev_close` 一算就矛盾 | 2+ | 运行值必须指回**当次调用**输出；写前用 `price == prev_close + change` 反算校验 | D1/D9 |

---

## §2 写前门禁（按操作类型）

### 2.1 写 JSON / YAML / frontmatter / 结构化文本

**J1 未转义的字面量破坏语法**（本类错误反复换宿主复发：JS 引号 → YAML 冒号 → JSON 尾逗号 → 正则转义，根因是同一条）

| 宿主 | 具体踩法 | 正确写法 |
| --- | --- | --- |
| JS 字符串 | 中文双引号 `“ ”` 提前闭合字符串，整段 `<script>` 解析失败 | 内层用「」《》；交付 HTML 前用 node 跑 `new Function(scriptText)` 语法校验 |
| YAML frontmatter | `description: >-` 块标量被朴素解析器读成字面量 `>-` | 单行 + 双引号包裹：`description: "..."` |
| YAML | `description` 含 `": "` 未加引号 → 非法 | 整体双引号包裹 |
| YAML | 平铺 `tags:` 写在嵌套 `metadata.openclaw.tags:` **之前**，被后者覆盖成空 | 平铺 `tags` 放嵌套块**之后** |
| 通用 | 自己写"等价解析器"替代真函数 → 漏掉规范化步骤（剥引号、字段展开）产生假警报 | 能用真函数就用真函数 |

**J2 frontmatter 发布前双解析器校验**（两个平台解析器不一致）
```
python -c "import yaml,sys;yaml.safe_load(open(sys.argv[1],encoding='utf-8').read().split('---')[1])" SKILL.md
python "~/.skillhub/skills_store_cli.py" publish <dir> --dry-run --json
```
必须两项都过。SkillHub 的 `parse_skill_md_frontmatter()` 只做逐行 `key: value` 切分，**不是 YAML**。

**J3 用正则改写结构化数据**必须覆盖**所有字段排布形态**。实证：正则没兼容 `main: '...', gallery: []` 与单行 brand 块两种形态，产生 154+125+2+22 处多余引号与对象截断，文件直接不可解析。改完必须 `require()` / `json.load()` 解析验证。

**J4 多行内容禁止走 shell 参数**（heredoc / `python -c` 字符串）。换行会变成字面量 `\n`。一律 Write 成脚本文件再执行。

---

### 2.2 写文件（Edit / Write / 脚本）

**W1 同文件多处改动必须串行**。
实证：海外版 8 个 Edit 并行、国内版 14 个分批，每个都回执 `Successfully edited`，实际只剩最后一个存活；另一次同文件并行 2 个 Edit，插入块未落地只有追加行生效。
- 正解：串行分批，或 Read 全文 + 单次 Write 全量写回。

**W2 Edit 回执不可信**。批量 `replace_all` 报成功但 grep 发现没变 —— 先怀疑 `old_string` 失配，不要怀疑环境。

**W3 定点插入的缩进必须从文件实际 `repr` 取值**，不能凭记忆写。实证：写成 7 空格，实际 6 空格，`count==1` 变 0，静默失败无报错。

**W4 禁止用 `cat` 拼接 + 重定向改长文档** —— 会把母稿截断。改长文档一律 Read 全文 + Write 整文件。

**W5 动手前声明触碰文件清单，越权改动先确认**。实证：指令只让改国内版，顺手把海外版做了四节重构（5069→3688 字），删掉关键节，只能从语义名交付稿全文回滚。收口时校验**实际修改 ⊆ 声明清单**。

**W6 交付稿要有语义名**。实证：`07-final.md` 全局搜不出内容，回滚时找不到可靠副本。case 内固定命名文件旁**必须出一份语义名交付稿**作为唯一可靠回滚源，用 md5 双向核对。

**W7 删除文件**：`[System.IO.File]::Delete()` / `os.remove()`。本环境 `Remove-Item` 被 `[safe-delete][SAFE_DELETE_FAIL_CLOSED]` 拦（报 `trash-failed` 但 `detail` 写 `OK`，**具误导性**），bash `rm` 因缺 `dirname` exit 127。大目录（数千文件）走 .NET 一次递归 + `run_in_background`，Node 逐层 `fs.rmSync` 实测慢 20 倍以上。

**W8 生成器必须「纯」且进版本控制。** 实证两例（同一项目，同日取证）：
(a) `reports/INDEX.md` 头部自述「生成器：`tmp/batch_a2_20260917.py`（重跑即刷新）」，但 `tmp/` 被 `.gitignore` 排除 → 该承诺在**任何一次 fresh clone 后即失效**；
(b) 同一脚本**不是纯生成器**：它先 `shutil.copy2` 台账再做文本替换（把 OI-004 的 `review_at` 从 9/24 挪到 9/17，自述动机是「与检查器一致」），然后才生成索引 —— **对着一个坏掉的闸门改数据，而不是修闸门**，而这个 9/17 恰好等于当时检查器里硬编码的 `REF`，正是让那条闸门隐形的原因之一。
- 三条门禁：① **唯一写盘目标 == 产物本身**（用正则扫自身源码里的写盘调用做机械断言，禁夹带改写治理数据的负载）；② 生成器放**受版本控制**的目录（`scripts/`），禁放 `tmp/`；③ 自带 **known-FAIL 自检**（漏登记 / 重复登记 / 幽灵文件 / 全空），并**实测失败分支可达**。
- 判据：**「重跑即刷新」这句话必须能被别人兑现** —— 换台机器 clone 下来跑不动，它就不是生成器，是一次性脚本，而产物头部的自述在说谎。
- 附带教训：**改数据去贴合闸门，永远是错的**。闸门报 FAIL 时，先问「闸门对，还是数据对」，再动任何一边；把声明值改成闸门喜欢的样子，等于把缺陷焊死在数据里。

---

**F4 翻转一条规则 = 全文搜同义表述，不是改一处就完**。改掉一条硬规则（如"新规则需人类批准"→"agent 当场写入"）时，同一主张会以不同措辞散落在多个位置：操作步骤、Human-in-the-loop 清单、Failure Handling 表行、输出模板的一行、`references/*.md`、README / README_zh。**只改主条目会留下 5-6 处与新规则直接矛盾的文字**，而自相矛盾比规则本身更容易被扫描器判为问题（会反向升级）。→ 改完主条目后，用规则里的**关键词**（approval / propose / draft / 人工确认 / 提议）全仓 grep 一遍再收工。实证：2026-09-20 workflow-guard-rails 1.0.4 只翻了 Hard Rule #7，遗留 6 处矛盾表述，1.0.5 才补齐。

### 2.3 跑命令 / shell

**C1 shim 缺 coreutils 且静默失败**（最高频）。
`dirname` / `head` / `sed` / `tail` / `tr` / `ls` / `rm` 全部 `command not found`（exit 127），**但管道整体仍返回 exit 0** —— stdout 被整体丢弃。
- 最危险组合：`git status --short | head -20` → 输出为空 → 误判"工作区干净"（实际积压 8 modified + 3 untracked）。
- 规则：**一切复合命令/管道/正则/中文 emoji 全写成 `.py` 脚本文件执行**。禁 `| head` / `| tail`。要限输出就在产生端限（python 切片）。判空必须看 exit code + 显式回显哨兵字符串。

**C2 路径形态**。Node 把 `/tmp` 解析成 `c:/tmp`（两个文件系统视图）；`clawhub publish /c/Users/...` 报 `Path must be a folder`；`--workdir /c/foo` 解析成 `c:\c\foo`；Windows Python 不认 `/c/...`。
- 临时文件一律放工作区 `.workbuddy/`；给 CLI 传 `C:\Users\...`；给 Windows Python 传 Windows 风格路径。

**C3 环境变量**：`VAR=x cmd ... $VAR` 不生效 —— 同一条命令里展开早于赋值。必须单独一行 `export VAR=...`。`TOKEN=$(cat f) && node -e` 不 export 则子进程读到空，不能据此说 PAT 失效。

**C4 编码**（Windows GBK 三连坑）：
- `print("✅")` → `UnicodeEncodeError` 崩掉整个通道（GitHub Pages 已推成功，企微通道被打死在半途）→ `PYTHONIOENCODING=utf-8 python -X utf8` 或 `sys.stdout.reconfigure(encoding='utf-8')`
- bash 内联中文喂 Python → 中文变 `?`（静默）→ 中文常量写进 `# -*- coding: utf-8 -*-` 的 .py 文件
- Node/子进程中文 stdout 被按 GBK 解码乱码 → `[Console]::OutputEncoding=[System.Text.Encoding]::UTF8`，或走桥接层：Write JSON 文件 → `spawnSync` 传 argv → Node 自己按 utf8 解码，全程不过 shell 管道

**C5 PowerShell 输出不回传**：`$o = & cmd 2>&1; $o -join "`n" | Out-File <file> -Encoding utf8`，再 Read。
**C5b** PS 5.1 `Invoke-RestMethod` 静默把 body 转 GBK，设 `Content-Type: charset=utf-8` 无效（已知设计缺陷）→ 所有 API 调用把 body 显式转成 UTF-8 字节数组。

**C6 curl 的 exit code 不可信**：Git Bash 下 `curl -o file -w "%{http_code}"` 返回 exit 23（`CURLE_WRITE_ERROR`）被 `||` 当成失败 → 拼出 `200000`。`curl -o /dev/null` 报 exit 23、`size_download` 恒 0 —— **http_code 可信、exit code 不可信**。响应体大小用 `curl -s <url> | wc -c`。
**C6b** curl 抓证书：`curl -v 2>&1 | grep subject:` 抓不到（openssl 输出不进 stdout）→ 用 `echo | openssl s_client -connect host:443 -servername host | openssl x509 -noout -subject -issuer -dates -ext subjectAltName`。

**C7 Grep 搜隐藏目录**：ripgrep 默认不进隐藏目录 + 尊重 `.gitignore` → 搜 `.workbuddy/` 静默返回 No matches。把 `path` **直接指向具体隐藏目录**；Glob 用 `**/.workbuddy/memory/**/*.md` 可正常列出。

**C8 可执行文件版本号/路径写死** → 环境升级后 exit 127。用前先 Glob 验证实际路径。Windows venv 是 `envs/default/Scripts/python.exe`（**Scripts 不是 bin**）。

**C9 内联 JS 在 Git Bash 易碎**：多语句箭头函数回调必须写 `{}`，引号分号转义错误率高 → 一律写成 .js 文件再跑。`node /tmp/x.js` 报 MODULE_NOT_FOUND → 脚本放 workspace 绝对路径。

**C10 删目录 / 建 git repo 都在独立副本里做**，`.git` 目录不能进发布包。

---

### 2.4 调 API / 网络

**N1 归因纪律**：一次失败禁止下"网络层被拦截""TLS 被墙""权限不足"的重结论。流程：① 同一命令重试 1-2 次；② 换栈交叉验证（curl / Node https.request / Python urllib 三者轮换）；③ 仍失败才上报，且必须附重试次数 + 交叉验证结果。禁止一次失败就给用户甩 A/B/C 三条路。
- 实证：curl 测 4 个域名，Notion 两个返回 000 + schannel 握手失败，立刻判定"受管终端针对性拦截"——10:17 被用户截图打脸，重试一次立刻 200。

**N2 企业代理误报 5xx**：urllib/WebFetch 对「301 → DNS 未配置域名」返回 **502**（真实是 301）；`dns.google` DoH 也 502。→ urllib/WebFetch 报 502/503 **先用 curl 交叉验证**，再 `curl --noproxy '*'` 三重确认。

**N3 子代理 429**（使用量超频率限制）不重试子代理，直接降级为自己 WebSearch（独立配额）。

**N4 外部平台状态用一次权威查询确认，不靠 sleep 轮询**。实证：用 sleep 轮询 ClawHub 发布状态纯等 5 分钟；表格视图有缓存，轮询看不到最新 → 用 `inspect --json` 的 `latestVersion.version`。

**N5 Notion API 六个已命中坑**：
1. `database query` 必须 POST，**`page_size` 放 body**；block children 取列表时 `page_size` 放 **query string**（正好相反，放 body 报 `validation_error`）
2. `PATCH /blocks/{parent}/children` **静默丢弃块内联 `children`**（不报错）→ 先建干净块拿 id 再递归 append。**唯一例外：table 必须保留内联 `table.children`**
3. `PATCH /v1/pages/{id}` 带 `parent` 返回 **HTTP 200 但 parent 不变** → 归档改走顶层 `[已归档]` 前缀约定
4. `PATCH /blocks/{id}/children` 的 `results` 长度**不可信**（实际插 5 条返回 `inserted 25`）→ 必须读回父块 children 计数
5. `GET /blocks/{table_id}/children` 的 `table_row.cells` 元素是**裸 rich_text dict 而非 list** → 解析函数必须兼容 `str / dict / list` 三种形态
6. `PATCH callout` 必须连 `icon` 和 `color` 一起发否则被清空 → 先 GET 原块合并
7. `table_row` **禁用 DELETE+INSERT**（DELETE 返回 400），用 `PATCH /v1/blocks/{row_id}` 改 `table_row.cells`
8. `PATCH /v1/blocks/{id}/children` **没有 prepend**，只有 `after`
9. 改 Notion 前**先拿真实 block id**，绝不能凭序号猜（实证把"资金比例"的校准写进了"中美对冲口"条）

**N6 抓取反爬**：`vldb.org` 直连 403 → `curl -A "Mozilla/5.0 ..."`；WebFetch 对长页面截断、对 PDF 丢字节 → PDF 改 curl 下载 + pypdf + Grep；Agent 返回被摘要化 → 用 `resume` 要回原文。

**N7 通道可用性（本环境实测，别再当兜底）**：
- **Bing 是负资产**：出口 IP 被定位到越南/日韩，返回越南日历站、韩国旅游博客、博彩站；中文查询退化为实体兜底、忽略 `site:`；`cn.bing.com` 返回 14KB 空壳 → 中文任务不要用
- **百度已 IP 级持久封禁**，限速无效 → 不要用它判断脚本是否正常
- **DuckDuckGo 返回 202 验证码页** → 停止批量查询，复用磁盘缓存或换通道
- `service.tcloudbase.com` 整体被网络层拦截，`tcloudbaseapp.com` 可达

**N8 命令行长度**：图片 base64 经 CLI 传参触发 WinError 206（命令行过长）→ 改 in-process `import tencentdocs; tencentdocs.main([...])`。

---

### 2.5 git

**G1 禁裸 `git push` / `git ls-remote` / `git fetch`**。
根因：`system: credential.helper = helper-selector` 在 helper 链首位 → `git-credential-manager.exe` 在无头环境等 OAuth/系统凭据窗 → 静默挂起 8–10 分钟并在桌面反复弹 GUI 对话框。**与网络、与 token 无关**（挂起期间 `curl api.github.com/user` 立刻 200）。
正解（实测 ~3s）：
```
export TOKEN=$(cat ~/.workbuddy/connectors/default/tokens/github.txt)
GIT_CONFIG_GLOBAL=/dev/null GIT_CONFIG_SYSTEM=/dev/null GIT_TERMINAL_PROMPT=0 git push https://$TOKEN@github.com/owner/repo.git main
```
- **禁令一**：任何作用域的 `credential.helper` 都不得配成**空字符串** —— 空值本身就是弹框触发条件。绕过 GCM 只能靠命令行标志，不能改 config。
- **禁令二**：不得把静默挂起读成"网络/token 问题"，**禁止重试同一条挂起命令**（每次重试再弹一次框）。排查先分层：`curl --max-time` 验可达 → 认证 API 验凭据 → 最后才碰传输层。
- 无效写法：`git -c http.extraheader="AUTHORIZATION: bearer $TOKEN" push` —— GCM 仍介入，先挂 8 分钟再报 invalid credentials。
- token 只在当次命令行，**不写入 `.git/config`**，事后 grep 验证零 `ghp_` 泄漏。
- 另：PAT 位置 `~/.workbuddy/connectors/default/tokens/github.txt`。GitHub connector 是 App integration token，**硬限制不能在个人 namespace 建 repo**（403）→ 建/写用 PAT，读可以用 connector。

**G2 对象库不一致时禁带 `-u`/`--hard` 的命令**。实证：一次 `git read-tree --reset -u` 在对象缺失状态下清空整个工作树，20 个未推送工件永久丢失。
- 先 `git fsck` 确认对象完整再动工作树。带 `-u` 的命令在对象缺失时"只删不写"。
- 恢复顺序恒为三步：① 抢救未提交文件到仓库外 → ② 全量 fetch 补对象 → ③ 重建 index 与工作树。
- 判断"抢救成功"不能只看 diff 一致（可能两者都是旧版）→ 另外核对内容特征。
- `git` 的删除不走回收站，`$Recycle.Bin` 里找不到。
- 并发会话是风险源：rebase/gc 被 SIGTERM 中断会留下损坏状态。

**G3 提交后仍有进程续写**。实证：16:28 提交后，`a-share-capital-plan.yaml` 在 16:36 被盘后 automation 改写，差点丢失。
- **每次 commit 后 `git status` 兜底，push 前再 `git status` 一次。**

**G4 CJK 路径被静默转义**：`git diff-tree --name-only` 对非 ASCII 路径应用 `core.quotePath` → `fs.existsSync` 全失败 → **文件被静默跳过，产出缺文件的 commit 且无报错**。用 `git -c core.quotePath=false diff-tree ...`。
**G4b** 提取 commit message 用 `git cat-file commit HEAD` 取第一个空行之后的内容（`git log -1 --format=%B` + `.trim()` 会丢尾换行从而改变 SHA）。

**G5 备份禁用 `tar -cf - . | tar -xf -` 管道**（会超时被杀）。用 robocopy 并校验子目录真的复制完成（曾只复制根层文件、子目录全空却报成功）。

**G6 别把 G1 的 env 覆盖套到 `add` / `commit` / `status` 上**（2026-09-18 实证，复发 1）。
- 现象：`GIT_CONFIG_GLOBAL=/dev/null GIT_CONFIG_SYSTEM=/dev/null` 是为躲 GCM 挂起（G1）而设的，只为 `push`/`pull`/`fetch` 服务。把它套用到 `git add -A` 后，system 级 `core.autocrlf=true`（Git for Windows 装在 system config，不在 `~/.gitconfig`）一并被丢掉 → 工作树 CRLF 文件**原样入 index**，而仓库历史是 LF → 一次 `git status` 冒出 **109 个 M、38,868 行**的假变更，全站 HTML 换行符被整体翻转。
- 正解（两者分开）：
  - `add` / `commit` / `status`：显式 `git -c core.autocrlf=true <cmd>`，**不依赖** system/global config，也不清空它。
  - `push` / `pull` / `fetch`：才用 G1 的 env 覆盖。
- 配套坑（同一轮踩到）：**`git status` 的 stat cache 会撒谎**。`add` 之后 index 记的是工作树的 mtime/size，后续 `status` 命中缓存直接判 clean —— 即使 index 里存的是错的换行符。修正完 config 后重跑 `status` 仍报 ALREADY-IN-SYNC。强制重新哈希要用 **`git add --renormalize .`**（我靠它才把 109 个文件纠正回 LF）。**"status 干净"不能证明内容对，只能证明 stat 没变。**
- 判据：同步类脚本的 `git status` 输出若不是预期的小 diff（`M data/site.json` + `?? daily/<date>.html`），先查换行符/autocrlf，再查内容。

---

### 2.6 发布 / 同步（skill 三平台）

**P1 版本号取平台 Latest + 0.0.1，绝不信任文件里的 `version:` 字段**。平台存在"只补文件不改 SKILL.md"的**空占版本**。三平台统一号 = `max(各平台 latest) + 0.0.1`。发布前 `clawhub inspect <slug> --versions`。

**P2 `clawhub publish` 的 slug 与 name 从发布目录名派生，不读 frontmatter** → **永远显式传 `--slug` 和 `--name`**。
- 不传 `--slug`：发布静默落到错误 slug；若该 slug 已属他人返回 `AMBIGUOUS_SKILL_SLUG`，所有人的 install 全崩。
- 不传 `--name`：`+` 号丢失，displayName 被 title-case 污染成 `Pub Chinese Fortune Telling`（frontmatter 里的 `displayName` **被忽略**）。

**P3 发布目录必须只含目标文件**。混入 `skill-card.md`（clawhub 拒收）、`LICENSE`（skillhub 拒收）、`FILES.txt`（fileCount 7 变 8）。`--dry-run` **只校验元数据+本地打包，抓不到文件类型问题**（含 LICENSE 仍 exit 0）。→ 建暂存目录；SkillHub 单独 staging 一份去掉 LICENSE 的副本。

**P4 发布是异步的，别把中间态当失败**。返回 `pending-publication`、`inspect` 仍 `skill: null`、`legacyReason=pending.publication` → **每 15s 轮询 `inspect` 到 `skill` 非 null 且 `latestVersion.version` 正确**。不要据此重发版本。以 `--json` 的 `latestVersion.version` 为准（表格视图有缓存）。实证滞后量：1.5.8 提交后 `--json` 仍返回 1.5.7 达 **140 秒**，第 8 次轮询（20s 间隔）才变 1.5.8；这段窗口内 `clawhub install` 装到的也是旧版 —— **不是发布失败，不要重发**。

**P5 大包用 `run_in_background` + timeout ≥300s**。实证：356KB/41 文件耗时 3m46s，前台撞 120s 超时表现为**零输出 + 非零退出，与硬失败无法区分**；158KB/42 文件前台 180s SIGTERM、后台 4m48s 成功。clawhub v0.23.3 **没有 `--wait`** 参数。用 `try/finally` 把 exit code 与输出落盘。

**P6 shim 下 npm 全局 CLI 包装脚本坏**（依赖 sed/dirname → MODULE_NOT_FOUND）→ node 直调 `$(npm root -g)/clawhub/dist/cli.js`。skillhub 同理绕过 wrapper 直调 `skills_store_cli.py`。

**P7 SkillHub 删除 = slug 墓碑，不可逆**。删掉后重新 publish 报 slug already exists 但 resolve 404，无 undelete API。→ **绝不为了强制重发删 source=clawhub 的 skill**；`description`/`description_zh` 在**首次发布时**就写对（两者首版锁定，后续发布不更新）。墓碑态用「公开详情 404 + publish 409」判定，不要拿 DELETE 探活。

**P8 闭环 = 三处落地 + 干净目录回装**。用 `clawhub install <slug>` 到干净目录，**逐文件 sha256 与发布包比对**。GitHub 走 contents API 是 upsert-only 从不删除，大重构后必须审计仓库文件清单；只从**最终发布源目录**推送，不从 install 目录推（可能带旧 `version:`）。

**P9 维护型更新基线 = 线上最新包**，本地母本常落后。母本与包漂移时先 diff。含真实生辰/持仓/私有路径/真实域名的 skill，发布包必须是**脱敏底稿 + 修复叠加**，禁止本地母本直接打包。

**P10 脱敏**：`example.com` 系真实域名、真实 GitHub 用户名、"企业代理环境"表述 —— 私有库可留，**公开发布前必须替换成 example.com 占位符并去除公司环境表述**。发布前三件套：claim-to-source → cross-material-consistency → localization；P0 未清零不发布。

**P11 clawhub 存量会被新引擎重扫**（推翻"旧版本不重扫"认知）：平台对存量 latest 版本重扫并拉进 `review_llm_review` 队列 —— Dashboard「Needs review」= 队列状态**不是新指控**。跨版本对比必须核对 scannerVersion。UI 右侧 Vulnerability Patterns ⚠️ 是 pattern 类别目录标记，非指控条目。引擎 issueCount 展示口径有矛盾（44 vs 明细 25、score=100 与 CRITICAL 并存）→ **以 issues 明细为准**。

**P12 SkillSpector 修复机理**：查 frontmatter `metadata.openclaw.permissions` **和 allowed-tools capability token 两处** —— 只补 permissions 不够（sap 1.5.7 补了仍报 LP1×2，因 allowed-tools 无 `env`/`network` token；双补后复扫 LP1→0 已验证）。AE1 触发 = SKILL.md 引用「引擎未能完全检查」的大/复杂产物（17KB+ 级）**字面量**，小脚本引用零触发，跨文件引用（references/*.md）零触发 → 修法：字面量改描述性提法 + 命令示例整块移 references（含文件名映射表保可执行性）。PE3 静态模式按注释字面量匹配（"access token" 连写会中）。

**P13 改了已发布 skill 的内容 = 欠一次重发，不是改完就结束**。本地 `SKILL.md` 与线上包各自演进：改 frontmatter / 正文 / 触发词后只留在本地，线上仍是旧内容，而 `version:` 与平台 latest **相同**会让"已经同步"看起来成立。→ 每次改动已发布 skill 后当场核对，判据是**文件内容 diff，不是版本号相等**；触及 frontmatter 或正文即 bump `max(各平台 latest)+0.0.1` 并重发三平台（只改 references/脚本同样要发，包内容变了）。正文里的 `C:\Users\<用户名>\...` 属私有路径（P10），公开发布前换 `~/.workbuddy/...`，用**脱敏 staging 副本**发，不动本地母本（P9）。首次实证：2026-09-17 workflow-guard-rails 改了 Hard Rule #7 + 触发词 + 新增两节，本地仍 1.0.3、clawhub latest 也 1.0.3，靠 `inspect --versions` + GitHub repo 存在性才查出线上已落后。

**P14 skillhub 没有可读接口，发布状态无法远程核实**。`GET /api/v1/community/skills/{slug}`、`.../mine`、`.../list`、`.../rankings` **全部 405**（带 Bearer 也一样），只有 POST publish 通。→ 判断"某 skill 是否上过 skillhub"改用旁证：clawhub `inspect <slug> --versions` 有 @<your-handle> 记录 + GitHub `<your-handle>/<slug>` 存在（三平台同步惯例）。**不要用 DELETE 探活（P7），也不要为了确认就先发一版（发布是对外动作，需确认门禁）。**

**P15 skillhub 发布走自建 multipart 脚本，CLI 在本机已不存在**。旧 CLI `skills_store_cli.py` 全盘搜索不到（AppData / .workbuddy / workspace 均无），别再花时间找。直接 POST `https://api.skillhub.cn/api/v1/community/skills/publish`：Bearer 取 `~/.skillhub/credentials.json` 的 **`user.token`**（顶层没有 token 字段）；multipart 两部分 —— ① `payload`（`application/json`）：`slug / displayName / version / description / changelog / category / subCategories / source:"community" / tags`；② 每个文件一个 part，**field name 必须是 `files`**（`file`、`files[]` 未验证），`filename`=相对路径（如 `references/x.md`），`Content-Type: text/markdown`。**HTTP 201 = 成功**（返回 `ok:true` + `version` + `fileCount` + `skillId`）。已验证：2026-09-18 workflow-guard-rails 1.0.4，4 文件，一次 201。**P15a 连续发布多个 skill 会撞 429 RATE_LIMITED（20s 重试不够）→ 90s 退避一次即过（2026-09-20 tcms 五连发实证：4 发 201 后第 5 个 429×3（20s 间隔全撞），90s 间隔一次 201）。批量发布直接用 90s 间隔，别用 20s 探底。**

---

### 2.7 数据处理 / 计算

**D1 引用价格/数据时点必须读 `price_as_of` 字段**，不靠报告标题或撰写日期推断。
- 实证：COST 902.60 / AXP 321.80 标为"9/10 收盘"，实为 9/9 收盘（9/10 官方收盘是 902.38 / 320.71）；另一次把 467.16 写成 9/16 收盘，实为 9/15。
- 日志时间戳必须在动作完成时实取，**禁止事后估写**（曾偏差 1.5–2 小时）。

**D2 单位与口径**：
- 1 亿 = 100 million，1B = 10 亿 → $240B 曾被写成 $24B（差 10 倍）。换算后做**量级 sanity check**
- `len(json.dumps(...))` 是字符数不是字节数（中文占比高时字节 ≈ 字符 ×1.40）→ 算字节用 `len(s.encode('utf-8'))`
- 跨来源数字并列必须显式写"口径不同、不可比"（Snowflake 86.3% / Databricks 84.5% / Anthropic 95% 三组自测口径完全不同）

**D3 全量刷新漏行不报错** → 全量刷新必须**逐行比对**。余额类字段（如 `accounts.US.cash`）必须有刷新路径 + `cash_as_of` 标记，否则 6 周后产生伪"缺口"被误判为资金划出。账户级更正必须附 cash rollforward，残差非零必须显式归因。

**D4 字段语义变更必须与下游文本同批更新**。实证：`A_share.value_usd` 语义从"仅证券"变成"含现金"（31,580.62 → 76,298.71），三处引用它的断言没同批更新 → 留下三条自信的错误陈述。记录旧值·新值·日期。

**D5 行情接口顺序**：`westock kline` 返回**按日期降序**，`rows[0]` 才是最新 —— 取 `rows[-1]` 拿到一个月前数据。显式断言日期；港股盘中占位行（open=0）过滤；raw 文件 US 代码需大写（`usGOOGL`）。

**D6 ETF 穿透**：必须乘 `equity_ratio`（159919 有 4.46% 是银行存款，忘乘会系统性高估）。中证一级与申万一级**两套分类严格不可相加**。**投影情景的每项修正必须按各情景自身基数重算**，不能把基准情景的差额当常数外推。

**D7 聚合脚本的源列表逐项断言**。实证：`gen_board.py` 漏读 `_wechat_2024_data.json`，2024 年 12 篇直接丢失。

**D8 增量/幂等写入的判据**：幂等标记必须与实际写入文本**完全一致**。实证：`MARK="2026-09-04校准"` 而实际写的是"已于 2026-09-04 **标注废止**" → `has_mark()` 返 False → 插入分支**静默跳过**（既不 OK 也不 SKIP）。整页级 `MARK in 任意子块文本` 会被前一步刚改过的块污染 → 用**该块独有字符串**判断，且该串必须写进插入块文本本身。表格追加前先 `row_has(tid, key_text)` 查重。

**D9 港股收盘价必须多次取样取稳定值，单次读数可能是瞬时档位**（2026-09-18 建）。
- 实证：9/18 写 state 时把 0700.HK 收盘记为 **421.40**，实际官方收盘是 **421.20**（`westock quote hk00700 --date 2026-09-18` 连续三次返回 `421` / `421` / `421.2`，`westock quote hk00700` 返 `421`，`change=-5` 与 `prev_close=426` 自洽）。421.40 **无任何来源支撑**，是凭空写入运行值的典型。
- **防御**：① 关键价位**连续取样 ≥3 次**，取多次一致或出现的最高精度值；② 用 `change`/`prev_close` 反算校验 —— `price == prev_close + change` 必须成立（421.20 = 426 + (−4.8) ✓；421.40 代入则不成立）；③ 与 `high`/`low` 边界校验（low=421，收盘 421.20 合理，421.40 亦可但无出处）。
- **根因不是"取错一个数"，而是"运行值可以无来源地写进脚本文本"** —— 与 D1（时点靠推断）同源：凡写进 PRICES 这类运行值字典的数，必须能指回一次**当次调用**的输出，禁止凭记忆/手填。

---

### 2.8 内容与排版

**M1 微信 HTML 是硬限制不是建议**：不支持 CSS class、不支持 `position:fixed/absolute`、不支持 `<script>`、只支持系统默认字体；`border-left` 被过滤（改用独立 `section` 色条 `width:4px;height:18px;background`）；误加 `<style>`/外层 `<div>` 粘贴后样式被剥离；普通正文**不写 `text-align`**（会被判"文字对齐异常"，仅 `<h1>` 保留居中）。
- **绝不能把浏览器预览当作微信兼容的证明**，微信编辑器才是最终渲染器。
- **排版 HTML 必须以 `wechat_layout_baseline.json` 为唯一基线，不允许凭印象生成**；每个标签的 style 必须恰好等于契约 key 集合；每次输出必须跑校验器。校验器对未授权样式**硬报错**（`extras = actual - allowed`）。
- 基线集中到单一 JSON，不在 SKILL.md / 参考文档 / 脚本里分别维护同一套（会漂移出 23/18/16 与 24/20/16 两套）。

**M2 前端原型三连击**（实证白屏）：① `fetch('./data/site.json')` 在 `file://` 下被 CORS 拦截 → 数据内联；② `boot()` 注入到 `const` 声明之前触发 **TDZ** → `boot()` 移到 `</script>` 之前；③ JS 模板字段名与数据不一致（用 `judge/kw/link` 数据实际是 `judgment/keywords`）→ 字段名以数据为准。
- CloudBase Web SDK 全局名是 `window.cloudbase` 不是 `window.cloud`。

**M3 matplotlib / 中英混排**：英文文本长度是中文的 1.5–2 倍，列宽要按语言重配。`LINE_H` 取 **1.35**（内容字号 9.5pt ≈ 0.93 单位，留 45% 余量；设 0.82 会导致行距 < 字高）。长描述**预埋 `\n` 断行点**。雅黑粗体需 `font_manager.fontManager.addfont("C:/Windows/Fonts/msyhbd.ttc")` 注册否则 bold 被静默降级。

**M4 图片**：Pillow 画中文必须用 `C:\Windows\Fonts\msyh.ttc` / `msyhbd.ttc`。**ImageGen 多版本调用必须串行**（并行会因时间戳碰撞合并成同一文件）。headless Chrome 在 Windows 上 `--window-size` 有约 **552 CSS px 下限**，要 393 会静默给 ~526px 布局视口而截图仍 393px 宽 → **两侧裁掉内容且无报错**；可靠修法：注入 `body{width:393px !important}` 用宽窗口渲染后裁切。

**M5 校验器自身会假报错**。实证：PNG 尺寸校验器报"无法读取尺寸"，实际文件是好的 —— 源码里 PNG 签名字节误写成**双反斜杠字面量**。正确签名 `b"\x89PNG\r\n\x1a\n"`。遇到该错误**先检查文件签名与解析器**。
- 另：**日报的 `validate_html.py` 依赖日报结构，不可用来判定月报失败**（不同体裁用不同校验脚本）。

**M6 小程序**：`<block wx:if>` 内嵌套 `wx:if/elif/else` + 绑定内算术表达式 `{{index*0.15}}` → 400 invalid parameter value。预计算 `imageMode`/`animationDelay` 字符串 + 独立 `wx:if`。`image` 组件**不支持 SVG**（只用 PNG 端点）。云开发：客户端 `.limit()` 上限 20（全量排序走云函数）、**云函数 `update` 不能含 `_id`**、"上传版本"只传代码包不更新云数据库。

---

### 2.9 事实 / 图像 / 越权

**F1 图像识别编造**：模型不支持图像时**绝对禁止**凭印象/推断编造内容。实证：对一张海报"识别"出"碎片为局""逛宇宙""宇宙探索风"——全是幻觉；OCR 核实真实内容是"特别支持：新智锦绣"，且还把"鼎新棱镜"误读。读不了就明说"当前无法识别这张图"，不可识别部分标"未做识别补全，待文字版确认"。

**F2 未核实就下结论**：
- 引语：引号内的英文原话必须核 transcript 原文，宁可改中文转述（实证写出语法不通的疑似中文回译句）
- 人物贡献：实证写"姚顺雨是 CoT 提出者"—— 错，CoT 是 Jason Wei 等 2022 年在 Google Brain 提出，姚的真实贡献是 ReAct / Tree of Thoughts / SWE-bench
- 厂商口径：区分官方公告 vs 开发者社区/自媒体（实证"推荐 AnalyticDB 承接"出自自媒体非官方公告）
- 公司身份：识别匿名公司**先搜 JD 里的产品英文名本身**，不能套中文语境特征匹配（实证把 PaleBlueDot AI 误判成硅基流动，一通财务分析全废）
- 价格/方向：比较方向写反（Hy3 ¥1/¥4 实际比 V4-Pro ¥3/¥6 便宜却写成"略贵"）
- 同音近形：微信聊天截图口语误读（"差点打车回家了"误读成"差点被车撞到"）

**F3 把本地/二手当真源**：
- 实证一：审计报告写"美股策略页残留「单只不超30%」（line ~2054）"，逐块读回 53 块核实**线上无此块** —— 该文本仅存在于本地惰性归档脚本。**凡标"冲突/待修"，必须先读回线上确认文本确实存在**，并区分"误报/展示滞后/真冲突"三类。
- 实证二：读 `risk-state.json`（二手镜像）没读 `open_items.yaml`（真源），把"数值比较"当"规则判定"、把"镜像一致"当"正确性"、把"待裁定开放问题"升格为可执行信号。**镜像必须带 `adjudication` 标记**；校验 PASS ≠ 规则可信。
- Notion 页面级 `last_edited_time` 滞后于块级编辑 → 判断陈旧度必须**读正文比对**，不能只看时间戳。

**F4 判断先行的过早概括**：证据补全前不下"不在 X 在 Y"的对举判断。实证：中国图景补完前就下"入口之争不在浏览器，在超级 App"，后续补研究后修正为"三层各自 AI 化的并行竞赛"。

**F5 过度保守 = 把判定成本转嫁给用户（2026-09-18 建，复发 2）**
本可当场判定「不适用 / 不需要」的条目，因为怕判错而一律标「待确认」并塞进正文，结果文档臃肿、用户困惑（实证：德国结婚清单里 Kammergericht 程序 —— § 1309 BGB 明确只管「在**德国**结婚」，而当事人是在北京结婚，且清单写的是「免除**提交** EFZ」与他们正在**申请** EFZ 逻辑互斥，属于模板残留；我却把它当"未决项"列在正文里，用户一眼看出并质疑）。
- **判定顺序**（动手写之前先过一遍，不是写完再补）：
  1. **能否从法条/原文自证不适用**？能 → 直接判"不适用"并**移出正文**，进「已排除项」表并写明排除理由
  2. 不能自证，但**后果可控** → 给**一条推荐路径**，把备选压成一行注
  3. 只有**影响费用/周期/法律效力**的才列为真待确认
- **"已排除项"表是必需件**：让用户看得出"审过"而不是"漏了"。每行列：排除项 + 原文 + 排除原因。
- **反向校验**：写完问自己"这条凭什么还在正文里" —— 答不出就删。
- **F5 变体：同域近义词误并（复发 2，2026-09-18 同日第二次）**。外文/法律材料里成对的近义术语被当成一个处理，导致把不适用的那一条也写进正文。实证：`Dolmetscher`（**口译**，§ 2 Abs. 2 PStV，只管当事人**在德国户籍官面前**的口头程序）与 `Übersetzer`（**笔译**，§ 2 Abs. 1 PStV，外文证件附德文译文）—— 当事人在北京结婚、材料交 Pförtner，前者不适用、后者必需，我却照抄表格勾选写成"柏林面洽需自带德语口译"。
  - **门禁**：遇到外文清单里出现的每个专业角色/文书名，**先查它对应的法条适用条件（对谁、在哪个环节、口头还是书面）**，再决定要不要进正文；不要因为"表格里勾了"就照抄。
  - **自检句**：这条到底约束**谁的什么行为**？当事人会不会真的经历那个行为？

---

## §3 写后验证（工具回执在本环境不可信）

### 3.0 六类"假成功"

| 回执 | 真相 |
| --- | --- |
| `Successfully edited` | 并发写互相覆盖，只剩最后一个 |
| `exit 0` | 管道里 `head`/`sed` 不存在，stdout 被整体丢弃 |
| `HTTP 200` | Notion `PATCH /pages/{id}` 带 `parent` 返回 200 但 parent 不变 |
| `results` 长度 | `PATCH /blocks/{id}/children` 实际插 5 条返回 `inserted 25` |
| curl exit 23 | 写输出失败，但 http_code 是好的 |
| `[safe-delete] detail: OK` | 实际是被回收站钩子 fail-closed 拦了 |

### 3.1 验证清单

1. **写文件后**：grep / Read 回验关键标记串（不查"我以为改了的地方"，查**唯一标记**）
2. **批量替换后**：grep 计数核对预期次数
3. **结构化文件后**：`json.load()` / `yaml.safe_load()` / `require()` 解析一遍
4. **HTML 交付前**：node `new Function(scriptText)` 语法校验
5. **API 写入后**：读回原块/原记录核对字段值
6. **git commit 后**：`git status`；push 前再 `git status`
7. **断言位置**：必须在写盘**之前**；写后校验只能是独立的、可重跑的第二个脚本
8. **控制字符扫描**：写盘后扫 `[c for c in s if ord(c) < 32 and c not in '\n\t']` 要求为空。判据不是"断言过了"，是"无控制字符 + 关键记号都在"

### 3.2 机器门禁 ≠ 合格

实证：`scan_draft_gates.py` 全部 P0/P1 为 0 的初稿，人工通读仍抓出国内版 7 处、海外版 13 处元语言与预告式句式，机器正则一条没抓到。
- 正则只能抓字面模式，抓不到语义层废话。**门禁后必须人工逐条清**，表达精度评审独立成步骤。

### 3.2.1 报 PASS 的检查，先确认它"有没有能力 FAIL"（V3 · 2026-09-18 建）

**不能失败的检查不是检查，是装饰。**

- **实证一（B-29，项目内）**：`scripts/check_open_items.py` 把基准日硬编码为 `REF = date(2026, 9, 17)`，而唯一到期项的 `review_at` 恰好等于该常量 → 判定式 `REF > review_at` **永不成立**，上线首日即失效，却一直报 `OK` / `逾期=0`。它不是潜在风险，而是**自出生起就不存在**的检查。
- **实证二（同日本轮）**：校准脚本里写了 `assert out.count('=====') == raw.count('=====')` —— 文件里根本没有 `=====`，两边都是 0，这条断言**对任何输入都成立**。同一天内同一缺陷类第 2 次。
- **排查动作（三步）**：
  1. 对**任何**长期只报 PASS 的检查，**grep 它有没有硬编码的日期 / 阈值 / 白名单**；
  2. **看失败分支可不可达** —— 用一条已知应当 FAIL 的样本喂它，看它是否**真的** FAIL，而不是看代码"像不像"会 FAIL；
  3. 断言里的**期望值必须是量出来的、不是估的**（本轮 `assert out.count('verdict_of') == 3` 就是估的，实测为 1；好在它在**写盘之前**触发，文件未被改动 —— 这是 §3.1 第 7 条在起作用）。
- **沉淀方向**：**新增检查必须自带 known-FAIL 样本的自检，且自检随每次运行执行**（不是 `--selftest` 才跑），判定逻辑**只准存在一份**。已落为项目私有 skill **HR 47**，并写入 `~/.workbuddy/MEMORY.md`。
- **正面样板**：项目里的 `scripts/sync_skill_to_repo.py --check` 是一条**能 FAIL 的好闸门** —— 本次它如实报 `VERIFY MISMATCH` / rc=1。复验时**主动确认它有能力失败**，而不是见到 rc=0 就放心。

### 3.2.2 声称「某效果生效」时，必须找到执行它的那行代码（V4 · 2026-09-18 建）

**声明 ≠ 执行。写进台账/文档的「生效」是散文，不是机器行为。**

- **实证（B-31，项目内）**：OI-004 裁定转 `deferred` 时，我在台账里写「`blocking_scope` 维持生效，减仓信号照旧不得发出」。**这句话是错的** —— `scripts/validate_levels.py` 的 C9 判据是 `if status != 'open': continue`，转 deferred 的那一刻，NVDA 的减仓封锁**已经彻底失效**。实测：deferred 时 C9 命中数 2 → 1；把 status 改回 open 才命中 OI-004。
- **为什么没被任何闸门拦住**：C9 本来就不会报「我漏了一个」—— 它只是**不报**。**沉默既可以是「无风险」，也可以是「检查没跑」，而输出长得一模一样。**
- **与 V3 的关系**：V3 是「检查永不 FAIL」（B-29）；V4 是「封锁永不执行」（B-31）。同属**声明与执行不一致**这一族：V3 是**假绿灯**，V4 是**假护栏**。护栏比绿灯更危险 —— 绿灯会让你去查，护栏会让你以为不用查。
- **排查动作（三步）**：
  1. 每当要写/信「X 生效」「封锁继续」「闸门会拦」这类**效果性声明**，**先去 `grep` 出执行它的那行代码**；找不到那行代码，声明就是假的。
  2. 找到后**实测触发一次**（本轮做法：把 status 在临时副本里改成三种取值，观察 C9 是否按预期变化）。
  3. 机器判据的**状态集合/白名单**必须是**模块层常量**，以便回归测试直接导入断言 —— 藏在函数体里就没人能守它。
- **沉淀方向**：为「可执行状态集合」这类判据建立**端到端回归**（正样本 + 已知应当 FAIL 的样本都必须有），并**实测注入缺陷后测试真的会 FAIL**。已写入项目私有 skill **HR 47** 的推论。

### 3.2.3 验证脚本自己的路径处理会制造假差异（V5 · 2026-09-20 建）

**DIFF 先查归一化签名，再信真丢失。**

- **实证（tcms 补发轮回装验证）**：验证脚本用 `os.path.basename(n)` 取 zip 内条目名 —— Python 的 ntpath 对 `references/brand-rules.md` 返回 `brand-rules.md`（basename 同时识别 `/` 和 `\`），子目录被脚本自己拍平 → 与本地相对路径对比报出 5 条 MISSING + 5 条假 EXTRA（`MISSING references/brand-rules.md` vs `EXTRA brand-rules.md`，**尺寸完全一致**），差点误判「平台把子目录拍平了」。用 zip 原始完整路径重新对照后 6/6 全 PASS —— 丢文件的是验证脚本，不是发布包。
- **归一化 bug 的签名**：每条 MISSING 都有一条同名、同尺寸的 EXTRA 与之对应（只是路径前缀不同）。看到这个形态先修验证脚本，别急着下「平台/管线丢数据」的结论。
- **排查动作**：① 验证 zip / 文件树结构用**原始相对路径**，禁 `basename` 拍平；② 平台包可能有 `slug/` 前缀或平台自生成文件（`skill-card.md`、`.clawhub/`），对比前做白名单归一化；③ 结论「结构被拍平/丢目录」必须先用**已知正常的对照包**（本轮用 sap@1.5.8）交叉验证原始路径后再下。

---

## §4 失败归因纪律

1. **先查自己**：命令是否真的把凭据/参数传给子进程；路径、分支、SHA、HTTP 方法和请求体是否正确。
2. **新写的校验脚本也是"工具"**：它报 FAIL 时第一嫌疑是脚本，不是被检文件。三日三次假报错**全部落在正确文件上**，而我每次都先怀疑文件：
   - 编号标题后的 `\b` 匹配到子标题（`### 7.7` 命中 `### 7.7.1`）→ 标题锚点必须带尾随空格或行尾
   - 用"子串计数"判断"是否只出现一处"（`|------` 在单行 `|---------|------|-------------|` 里出现两次）→ 必须按**整行**比较
   - 按 `##` 切段时最后一段吞掉尾部分隔符与下章导语（3,181 B 报成 3,513 B 且已对外引用两次）→ 必须按显式首末边界切片
   - 校验函数返回形状不一致（`[[x],[y]]` vs `[x,y]`）→ 内容相同仍判 MISMATCH → **必须先归一化形状再比对**
3. **禁止把"一次测量就能解决的分歧"留成推断收尾**。实证：`_meta.json` 案例以"形似平台元数据"结案，实际只需比对同目录 12 个同类文件即可定论；往返一轮后回到原结论，净产出为零。**能测量的不许推断。**
4. **同一个数字两次交付不得不同**。对外引用的体量/占比必须同时标明**量的是哪一段、哪种单位**；改口径必须明说，不许静默换数。
5. **交付前过一遍"我这轮打算下几个结论"**：哪些是测过的，哪些只是推断。只交付测过的；推断要么当场测掉，要么标成待测，不混进结论。（实证：当日"更正"一词在日志里出现 12 次，绝大多数属"本可当场测掉"。）
6. **能测量的不许推断**：大量"疑似"结论其实一次 API 调用/一次 grep 就能定论。

---

## §5 为什么会"自己发现、说自己修正了、然后照样犯"

七个成因，全部落在机制层面：

**1. 修正的落点是 context，不是系统。**
"我发现并修正了"只活在当前会话的上下文里。下次会话是白纸，**唯一能跨会话存活的是写进磁盘、且被执行前读取路径覆盖到的规则**。历史上大量修正只写进了对话和当天日志 —— 等于没修正。

**2. 记忆按时间组织，检索键却是操作类型。**
日志是 `2026-09-11.md` 这种按日期命名的叙述体。下一次遇到 JSON 语法错误，没有任何路径会引导我去翻 9 月 11 日那篇日志。**按日期组织的经验等于不可检索的经验。** 本手册按操作类型（§2.1 写 JSON / §2.5 git / §2.6 发布）组织，动手前才查得到。

**3. 有"写后修"，没有"写前门禁"。**
默认流程是：生成 → 落盘 → 报错 → 改 → 再错 → 再改。每一轮都在付 token。正确流程是：查门禁 → 生成 → 落盘 → 验证。§2 就是门禁，§3 是验证。

**4. 信任工具回执。**
`Successfully edited`、`exit 0`、`HTTP 200`、curl exit 23、`detail: OK` —— 本环境至少有六类"看起来成功"（§3.0）。信回执就等于跳过验证，跳过验证就等于错误只会在更下游、更贵的地方才暴露。

**5. 同一条错误换宿主复发，被当成不同错误。**
JS 里中文引号提前闭合字符串、YAML 里 `description` 含 `": "`、JSON 尾逗号、正则漏转义 —— 表层是四种语言，根因是同一条：**字面量进结构化文本未做转义/形态覆盖**。按表层语言记录就看不出是同一个，按根因归组（§2.1 J1）才防得住。

**6. 重试优先于查规则。**
第一次报错后，agent 的自然反应是"改一改再试"，不是"这条操作有没有已知坑"。§0.1 第三条把这一点写成硬动作：报错先查 §1 复发榜。

**7. 已有的护栏 skill 是空壳方法论，且沉淀闸门设在人类一侧。**
`~/.workbuddy/skills/workflow-guardian/`（2026-07-29 建立；9/18 更名为 `workflow-guard-rails`，运行时目录为 `Claw/skills/workflow-guard-rails`）声称七项守卫、含「规则沉淀 guard #7」，正是为解决本问题而建的。但至今规则库为空，三个原因：① 它只给方法论框架，不含本机任何一条具体错误；② 触发词是英文与正式中文（"工作流守护""生产护栏""漂移检测"），日常不会这么说，因此几乎从不加载；③ Hard Rule #7 规定"新规则需人类批准才能激活" —— 闸门在人类一侧，实际从未被激活过。
**教训：护栏的防护力取决于已沉淀的规则条目数量，方法论框架本身不拦任何错误。** 本手册把闸门放在 agent 一侧（§0.3：当场追加，不等批准）—— 漏掉一条已知错误的代价，远大于多加一条可能冗余的规则。

### 落地约束（对抗以上七条）

- **单一权威文件** = 本手册。散在几十个日志里的教训不算数。
- **按操作类型索引**，不按项目、不按时间。
- **§1 复发榜**放在最前，token 紧张时只读它 —— 高频错误必须是最便宜能拿到的。
- **§0.3 沉淀规则**：每次"自己发现并修正"，当场追加进本文件对应章节 + 复发计数。这是让手册不失效的唯一机制。
- **~/.workbuddy/MEMORY.md 顶部硬指针**：每次会话开始就能看到指向本手册的指针。

---

## §6 出处索引

- 长期记忆：`~/.workbuddy/MEMORY.md`（工具调用失败处置、失败归因前自复查、git 推送硬规则、git 恢复硬规则、Skill 三平台版本号等）
- 工作区记忆：`<workspace>/.workbuddy/memory/MEMORY.md`（Skill 发布与治理、环境坑、PR 监测 engine）
- 日志：`<workspace>/<project>/.workbuddy/memory/YYYY-MM-DD.md`
- Skill 文档：`~/.workbuddy/skills/{skillhub-clawhub-sync,skill-audit-publish,skill-batch-ops,skill-design-guide,skill-local-overlay,skills-security-check,deep-search-engine,data-ai-daily-brief-skill,chinese-fortune-telling,notion-trade-log-mirror,content-publishing-suite,wechat-native-html-layout,private-github-repo-upload,local-skill-to-expert-package,github-pages-deploy,wecom-docx-roundtrip,deterministic-html-poster-export,mobile-reading-export,bazi-chart-dossier-pipeline,ima-skills,westock-data,windows-html-to-docx-recovery,pptx-*}/SKILL.md`

---

_建立于 2026-09-17。每次修正必须回写本文件，否则下次必犯。_
