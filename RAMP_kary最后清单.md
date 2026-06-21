# RAMP · kary 最后阶段完整清单(Codex 执行)
现状:主菜已赢 —— 完整流水线在 main,两个真实修复 PR(bad.html 60→96 · 真实开源项目 92→100)。
你负责证明路径的收尾:**Devpost + dashboard + precision 数字定稿**。
你的目录:`packages/bench`、`packages/scoring`、`apps/dashboard`

> ⚠️ 这份清单要严格照做。用 Codex 时,**每个任务结束让它停下、把结果贴出来给你核对,再做下一个**,不要让它连续自动跑多个任务。

---

# 🚫 绝对别碰(碰了会破坏队友的活 / 烧钱 / 翻车)

1. **别碰 `packages/harness`、`packages/control-plane`、`packages/shared`** —— 这是队友的目录,你只读不改。dashboard 要数据就读它们的接口/DB,不改它们的代码。
2. **别改 `shared` 里的类型 / ViolationType 枚举** —— 改了两边对不上,全盘乱。只 import,不动。
3. **别 merge / 别删那两个 demo PR**(Ramp 仓库 #7、那个 fork PR)和它们的分支 —— 它们是 demo 素材,删了就没了。
4. **别覆盖 `leaderboard.json`** —— 除非明确要重跑,否则别动它。
5. **别明文贴 API key 进任何对话 / 文件 / commit** —— key 只用 inline(`OPENAI_API_KEY='...' 命令`),跑完删 history。
6. **别用 Anthropic API**(队友那个余额封存),你要跑模型一律用 OpenAI gpt-4o-mini。
7. **别擅自跑大批量评测** —— 跑任何调 OpenAI 的命令前先确认范围,小批量,设了 $5 cap。

---

# 🔴 任务 1:起草 Devpost(最高优先,现在就做)

这是**没有就前功尽弃**的事。用下面的结构和素材写。**先写草稿,数字先留占位,等任务 3 定稿后填真数字。**

给 Codex 的指令:
```
按以下结构写一份 Devpost 项目描述(英文),套这个大纲,每节 2-4 句,塞进具体数字:

Inspiration: 13亿残障用户被代码挡门外;现有工具(axe/Lighthouse)只报告不修复;
  约70%的WCAG问题需人类级判断,自动工具查不出。

What it does: Ramp 三支柱 ——
  ① A11y-Bench:从真实 GitHub 无障碍修复 PR 构建的 51 道 benchmark,覆盖多类 WCAG 违规;
  ② Harness:给模型装 a11y 树/读屏模拟/对比度实测/WCAG 规则,把通用模型变成无障碍审计专家;
  ③ 自动修复闭环:审计→Claude Code 改代码→axe 验证→开 merge-ready PR。
  关键差异:别人停在报告,我们交付可合并的修复 PR。

How Claude Code powered Ramp(重点写,对标赞助商):Claude Code 是修复闭环的核心 agent ——
  读 finding、做最小代码修改、自验证;也帮我们并行搭建了整个系统。

How we built it: TypeScript monorepo;Playwright + axe-core 审计;a11y 树 + 读屏序列化 + 
  对比度实测;Vercel AI SDK 多 provider;Drizzle/SQLite;React/Vite/Tailwind dashboard;
  Claude Code headless 做修复;Octokit 开 PR。

Challenges: 把审计/沙箱/Claude Code/GitHub 缝成一个可复现闭环;
  发现"检出率(recall)"奖励乱报、不是正确指标,转向 precision + 修复闭环。

Accomplishments & What we learned: "工具与 prompt 是模型能力的真实杠杆";
  "PR 是无障碍工作的正确接口";"价值不在报得多,在精准 + 能修复"。

What's next: 接进 CI,真实项目新披露的无障碍问题自动复现、修复、提 PR 回上游;
  源码层审计(benchmark 里的源码任务已铺好路)。

Built With: playwright, axe-core, claude-code, anthropic, openai, vercel-ai-sdk,
  react, vite, tailwind, drizzle, sqlite, typescript, octokit, github

数字占位先留 [X],等我给最终数字再填。先出完整草稿给我看。
```

**要填进去的真实素材**(确定的,可直接写):
- 两个真实 PR:bad.html **60→96**、真实开源项目(aigov-ops)**92→100**
- axe 违规 **5→0**(bad.html)
- 读屏 before/after:`"image, button, button"` → `"image: Acme logo, button: Search, button: Add"`
- benchmark **51 道任务**(其中 15 道 html-live)
- precision 数字 → 等任务 3 定稿后填

✅ 验收:Devpost 完整草稿,结构齐、素材齐,数字处留 [X] 待填。

---

# 🔴 任务 2:dashboard 接 before/after(跟队友协调接口)

让分数跳变(60→96 / 92→100)和读屏 before/after 显示在 dashboard 上,不只在 PR 里。

**先确认数据从哪来**(别瞎改):
```
先只读不改:看 control-plane 的 fix loop 把修复后的 score(after)写没写进 scores 表
(phase="after")。如果写了,我 dashboard 直接读 before+after 两个 phase。
如果没写,告诉我差什么,我跟队友说一声让他补一个 after-score 写入 ——
我不碰他的 control-plane 代码。把你查到的情况贴给我。
```

**确认后再改 dashboard(只动 apps/dashboard)**:
```
在 ScoresPage(或新组件)显示:
- 合规分 before → after 的大数字跳变(60→96 / 92→100),做成醒目对比
- axe 违规 before → after(5 → 0)
- 读屏 before/after 文本对比
只动 apps/dashboard,不碰 control-plane/harness/shared。深色干净。
做完 pnpm dev 起来截图给我。
```

✅ 验收:dashboard 上能看到分数跳变 + 读屏对比。**这是 demo 第 2-3 步的视觉支撑。**
⚠️ 坑:如果 after-score 没写进 DB,**别自己跑去改 control-plane** —— 报给队友补,你只管 dashboard 读和显示。

---

# 🟡 任务 3:precision 数字定稿(复杂页面对比)

你之前造了 10 个完整页面(含诱饵)。在它们上跑 naked vs harness 取平均,拿到稳定的 precision 领先数字。

```
用新 OpenAI key(inline,跑完删 history)在那 10 个 complex fixture 页面上跑
naked vs harness 3 次取平均(gpt-4o-mini)。
报每次 + 平均的 recall + precision。不覆盖 leaderboard.json。
跑完把平均数字贴给我。这是唯一花钱步骤,几分钱,已设 $5 cap。
```

✅ 验收:一组稳定的 naked vs harness precision 数字(预期 harness precision 明显领先)。
把这个数字填进任务 1 的 Devpost [X] 占位,也放进 dashboard 的 Scores 页。
⚠️ 坑:**数字本身绝不许调**(改匹配规则/评分让数字好看 = 作弊,会被查穿)。只如实报跑出来的。

---

# 🟢 任务 4:有时间才做(锦上添花)

- 把 51 道 benchmark 整理成一张"我们构建的真实 a11y benchmark"展示图(放 Devpost / dashboard)。
- dashboard 整体视觉再美化(配色/字体/动效)。
- leaderboard 那版混合数字别放主位(它对 harness 不利),用任务 3 的 precision 数字当主数字。

---

# 你的执行顺序(这一天)

```
上半天:任务1 Devpost 草稿(最优先) → 任务3 precision 跑平均(几分钱)
下半天:任务2 dashboard 接 before/after → 任务1 填真数字定稿 + 配图
临提交:和队友一起端到端验收 + 提交
```

---

# ⏰ 提交前你要确认的(和队友一起逐条过)
- [ ] Devpost 正文完整、数字都填了真值(没有 [X] 残留)
- [ ] **两人都加进 Devpost team**(你和队友都要在,没加 = 没正式组队)
- [ ] dashboard 能展示分数跳变 + precision 数字
- [ ] 两个 demo PR 链接有效(没被删 / 没被 merge)
- [ ] 选对赛道(和队友商量:TOOLBOX 开发者工具 / WORLD 社会影响)
- [ ] 留 ≥30 分钟 buffer,别卡点提交

---

# 给你(kary)的一句话
你的 51 道 benchmark + dashboard + precision 数字,是项目"技术深度"的证据支柱。这一天你最重要的事是 **Devpost 写扎实**(把所有数字和两个真实 PR 讲清楚)。**别碰队友的 harness/control-plane/shared,别明文贴 key,数字别调** —— 守住这三条,剩下照清单做就稳。
