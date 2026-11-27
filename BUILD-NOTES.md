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
