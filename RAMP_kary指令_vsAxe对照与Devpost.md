# RAMP · kary(Codex)指令 — Ramp vs axe 对照展示 + Devpost
新卖点:**axe 只能查"有没有"+ 只会报告;Ramp 能判"写得好不好"(语义)+ 自动修复。**
你负责把这个对照**展示出来 + 写进 Devpost**。
你的目录:`apps/dashboard`、`packages/bench`(写 Devpost 素材)

> ⚠️ 用 Codex 时每个任务做完停下贴结果给你核对,别连续自动跑。

---

## 🚫 绝对别碰
1. **别碰 harness / control-plane / shared** —— 队友在那加语义审查能力,你只用他给的输出数据,不改他的代码。
2. **别明文贴 API key**(inline,跑完删 history)。**别用 Anthropic API**(要跑模型用 OpenAI mini)。
3. **别删/merge 那些 demo PR;别覆盖 leaderboard.json;别改 shared 的类型/枚举。**
4. dashboard 要数据 → 用队友给的 vs-axe 输出 JSON,不自己跑 harness。

---

## 🔴 任务 1:dashboard 做 "Ramp vs axe" 对照展示(核心)

队友会给你一份对照数据(axe 查到的 vs Ramp 查到的,含 axe 漏掉、Ramp 抓到的语义问题 + 修复建议)。

```
在 apps/dashboard 新增一个 "Ramp vs axe" 对照页/区块(只动 apps/dashboard):
左右两栏对比同一个页面:
  左:axe-core —— 列它查到的,并显示它"放行"的那些(如 alt="image" axe 说通过)
  右:Ramp —— 显示 Ramp 额外抓到的语义问题(alt="image" → 判定"无意义,对盲人没用")
       + 给出的修复建议(→ alt="Acme company logo")+ 是否已开修复 PR
顶部一句大字:"axe: 0 issues found  ·  Ramp: 3 semantic issues + fixes"
数据来源:队友给的 vs-axe 输出 JSON(我会拿到后给你)。深色、干净、对比强烈。
做完 pnpm dev 起来截图给我。
```
✅ 验收:dashboard 上能直观看到"axe 放行,Ramp 抓出并修复"的左右对比。**这是 demo 最有力的一屏。**

---

## 🔴 任务 2:dashboard 接 before/after(之前那个)
```
ScoresPage 显示修复分数跳变(60→96 / 92→100)+ axe 违规 before/after(5→0)
+ 读屏 before/after 文本对比。数据读 control-plane 接口/DB 的 scores 表(before+after phase)。
只动 apps/dashboard。after-score 若 DB 里没有,告诉我,我让队友补,你别碰他代码。
做完截图给我。
```

---

## 🔴 任务 3:Devpost(套新卖点 + Codebreaker 结构)
重点:**别再主打"检出率提升"**(那个 harness 没赢)。主打**"axe 做不到的语义判断 + 修复闭环"**。

```
写 Devpost 项目描述(英文),结构如下,每节 2-4 句塞数字:

Inspiration: 13亿残障用户被代码挡门外;现有标准工具 axe/Lighthouse 只能查"有没有"
  (机械规则)、且只报告不修复;约70%的无障碍问题需"理解语义"才能判断。

What it does: Ramp 做 axe 做不到的两件事 ——
  ① 语义质量审查:axe 说"有 alt 就通过",Ramp 判断 alt 写得"有没有意义"
     (alt="image" 对盲人无用 → 标记 + 给出有意义的替代);
  ② 自动修复闭环:审计→Claude Code 改代码→axe 验证→开 merge-ready PR。
  外加 A11y-Bench(51 道真实任务)做评测基座。
  一句话:axe 检测,我们理解并修复。

How Claude Code powered Ramp(重点,赞助商):Claude Code 是修复闭环的核心 agent ——
  读问题、做最小代码修改、自验证;也帮我们并行搭了整个系统。

How we built it: Playwright + axe-core(基础检测)+ 语义审查层(模型判断可访问名质量)
  + a11y树/读屏/对比度实测 + Vercel AI SDK + Drizzle/SQLite + React dashboard
  + Claude Code headless 修复 + Octokit 开 PR。

Challenges: 把检测/语义判断/沙箱/Claude Code/GitHub 缝成可复现闭环;
  认识到价值不在"检出率"(机械规则 axe 已够),而在"语义判断 + 修复"。

Accomplishments & What we learned: "axe 这类工具查不了语义,只有理解语言的模型能做";
  "PR 是无障碍工作的正确接口";"我们不报告,我们修复"。

What's next: 接进 CI,真实项目新问题自动修复 PR 回上游;源码层审计。

Built With: playwright, axe-core, claude-code, anthropic, openai, vercel-ai-sdk,
  react, vite, tailwind, drizzle, sqlite, typescript, octokit, github

素材(确定的,直接用):
- axe vs Ramp 对照:axe 放行的垃圾 alt/按钮名,Ramp 抓出并给修复
- 两个真实修复 PR:bad.html 60→96、真实开源项目 92→100
- axe 违规 5→0;读屏 "image,button,button" → 有意义的名字
- 51 道真实 benchmark
先出完整草稿给我看。
```
✅ 验收:Devpost 完整草稿,主打"语义判断 + 修复",不主打检出率。

---

## 🟢 任务 4:有时间才做
- 把"axe 放行 vs Ramp 抓出"的例子做成一张醒目的对比图(Devpost 配图)。
- dashboard 整体美化。

## 提交前(和队友一起逐条过)
- [ ] Devpost 完整、数字填真值、**两人都加进 team**
- [ ] dashboard 有 Ramp vs axe 对照 + before/after
- [ ] 两个 demo PR 链接有效(没删没 merge)
- [ ] 选赛道(TOOLBOX / WORLD)
- [ ] 留 30 分钟 buffer

## 一句话(给 kary)
新卖点是"axe 检测、我们理解并修复"。你的活:把这个对照**直观展示在 dashboard** + **写进 Devpost**。守住三条:别碰队友目录、别明文贴 key、数字别调。
