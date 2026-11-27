# Token Radar — 构建记录（2026-11-27）

## 做了什么

- 参赛作品：make.fun `token-radar-01`（奖池 $1,325，截止 2026-12-14 10:00 UTC）。
- 位置：`~/workspace/makefun/token-radar/`，本地 git 已提交，未推送、未建仓、未提交参赛。
- 在 starter 基础上实质重写：模型 `radar-2`（`lib/model.ts`）、仪表盘（`dashboard/app.js`、`app.css` 全新深海军蓝 + 琥珀配色）、`main.ts` 适配。

## 排名模型（radar-2）

- 不再用 starter 的 TVL × 0.25 扁平倍数。类别对标倍数在运行时从快照现算：同类别已发币协议的中位 mcap/TVL，向全局中位（0.214）收缩（k=4），钳制在 0.05–0.60；类别内少于 2 个已发币对标时直接用全局中位（避免 Risk Curators 唯一对标 2.98x 这种野值定价整个类别）。
- 增长：change_7d 钳制 [-40, +60]，权重 0.6；成熟度：年龄（listed_at 起）0.75–1.00，未知年龄按 365 天；多链广度：每多一条链 +1.5%，上限 6 条。
- 榜单 198 行：40 个 watchlist 全覆盖 + 158 个 watchlist 外的 pre-token 协议（按隐含价值取，不只看 TVL——例如 $2.76B 的 Sentora Curator 不在 watchlist 里，但排第 2）。CEX/桥/质押衍生/版本池/交易所自有产品一律排除。
- 头名：Steakhouse Financial，隐含价值 $554.0M；榜单总隐含价值 $4.04B。
- 详情页含「Why this rank」解释句、同类别已发币对标（Priced against）、7 步 farming 计划（主操作 + 第二个不同操作 + 周常 + 持有期 + 领奖核查 + 风险提示，每步含成本与耗时）。

## 自测结果

- 本地复刻 gate 七项全部通过（脚本 `/tmp/gate-check.mjs`，非仓库文件）：board 198 行、rank 1–198 单调、watchlist 40/40、farm 步骤 7 条且 118–190 字符、截图 3 张 1280×800 PNG（各 136–200KB）、dossier 684,206 字节且与 `buildDossier` 逐字节一致、仪表盘源码 39,437 字节（< 65,536 上限）。
- 两次运行 `main.ts` 输出逐字节相同（确定性通过）。
- 仪表盘页面重算与 board.json 逐行一致（0/198 不符；曾因乘数四舍五入导致 18 行差几美元，已改为全精度存储、显示时保留 4 位小数）。
- 三张截图是真实浏览器渲染（本地 Chromium + CDP 实拍，已目视核对）：`screenshots/1-board.png`（排行榜）、`2-detail.png`（Steakhouse Financial 详情）、`3-filtered.png`（Risk Curators 筛选视图）。
- 注意：本机 Chromium 需 `--hatch-webgl-allowed-domains-file` + sha256 开关才能启动，且 localhost 会被 Local Network Access 检查拦截，截图实际走 `file://` 地址拍的；`hatch-chromium-sandbox` 在此环境不可用。

## 提交前待办（用户或主会话处理，本次未做）

1. 在 GitHub 新建公开空仓库（用户在网页端建，Muse 的 GitHub 应用建不了仓），把本目录推送上去。
2. 把 `README.md` 里的 `{owner}`、`{repo}`、`{referral_code}` 占位符换成真实值（提交检查要求徽章 URL 无占位符）；按 `req-token-radar-01.json` 里的 `how_to_submit.repo_about` 设置仓库 About（描述、主页 `https://make.fun/bounty/token-radar-01`、topics `makefun,ai-agents,bounties,trading`）。
3. 如用 GitHub 仓库提交：加 `.makefun/owner.json`（`{"wallet": "<用户钱包地址>"}`），除非仓库 owner 就是其 GitHub 登录账号。
4. 打包 transcripts（`node cli/makefun.mjs bundle ...`，需附 Muse usage），再跑 `check`，最后 `submit --competition token-radar-01 --repo-url ... --commit <sha> --title "Token Radar" --bundle transcripts.tar.gz --keypair wallet.json`（签名与钱包步骤由用户本人完成）。
5. 注意：kit 的 `init`（source bundle 路线）本次没跑，因为它需要钱包 keypair；走 GitHub 路线则不需要 init。

## 迭代记录（2026-11-27，radar-2.1）

针对 radar-2 复核出的问题做的实质修正（数据仍只用项目内快照，未编造任何协议数字）：

- **倍数全精度**：radar-2 把类别倍数存成 3 位小数——按全局中位定价的行因此有两种「全局中位」（n=1 类别用 0.214、无对标类别用 0.21380…），头名 Steakhouse 用的就是前者。2.1 起倍数与中位数全精度存储，所有「全局中位」行共用同一个数（0.213803…）；166 行隐含价值小幅变动，15 对近乎并列的行名次互换，前三名不变（Steakhouse $554.0M → $553.5M）。
- **详情页真的给出对标**：radar-2 的「Priced against」从市值前 32 的已发币列表里按类别筛，66 行筛不到任何对标（其中 41 行明明有 ≥2 个对标）。2.1 起对标集把每类最大的 3 个真实对标按名带进 data.js，180/198 行能列出真实对标；其余 18 行明说该类别在快照里没有任何已发币对标、按全局中位定价。
- **解释按模型实际口径**：「Why this rank」改用模型实际计入的涨幅（钳制后），不再出现「down -72.71%」这类双重否定，也不再拿 +4448% 的原始涨幅冒充模型计入值（注明按 +60% 上限计）；缺 7 日数据（2 行）与缺上市日期（7 行）的行明说缺失与所用假设（增长中性、年龄按 365 天）；链数超过广度上限的行注明只计 7 条；每行补与上、下名的差距。
- **农场计划修错**：17 个单链 curator/allocator 协议的第 3 步原写「在另一条链存入第二个金库」——协议只在一条链上，该步不可能执行；2.1 起单链协议改为在本链第二个金库（model.ts 与仪表盘内复制的同一逻辑同步改，198 行两边逐步一致已验）。
- **仪表盘诚实性小修**：头部「Watchlist covered」改为按榜单实际计数（原为拿 watchlist 总数自填自答）；头部 7 日增长中位数改用真中位数（原偶数个样本取上中位）；模型公式补上广度上限 min(chains − 1, 6) 且常数全部取自模型参数（原写死 0.015 且漏上限）；对标表题注点名 13 个无对标的榜上类别；已发币表里 7 日涨幅缺失的行不再误标红色。
- **复验**：两次运行逐字节一致；board 198 行、名次 1–198 单调、watchlist 40/40、农场计划每行 7 步且 20–600 字符；dossier 与 buildDossier 逐字节一致（689,242 字节，文本 44,359 ≤ 65,536）；仪表盘源码 44,095 字节 < 65,536；页面按 data.js 重算 198/198 行与 board.json 一致。
- **截图需重拍**：榜单数值与详情文案已变，现有 3 张截图（按 radar-2 渲染）与新榜单不完全一致，提交前需按新仪表盘重新实拍，本轮未动截图文件。
