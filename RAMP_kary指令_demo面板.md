# RAMP · kary(Codex)指令 — Demo Dashboard 三个面板
目标:把已有的好数据做成 demo 屏幕上**一眼击中评委**的视觉。这是 demo 第 2-3 幕的视觉支撑。
你只动 `apps/dashboard`。**别碰 harness / control-plane / shared。**

> 数据现成:`packages/harness/fixtures/vs-axe-report.json`(已在 main)= "axe: 0 / Ramp: 12" 对照数据。
> ⚠️ 每个面板做完停下截图给我核对,再做下一个。

---

## 🔴 面板 1(最重要):"axe vs Ramp" 左右对照 —— demo 王牌

这是 demo 最炸的一屏,做得**对比强烈、大字、一眼看懂**。

```
在 apps/dashboard 新增一个 "axe vs Ramp" 对照页/区块(只动 dashboard)。
数据读 packages/harness/fixtures/vs-axe-report.json。

布局:左右两大栏,中间一条分割线。
- 顶部居中一行超大字:
  "Same pages. axe: 0 issues found.  ·  Ramp: 12 issues axe can't see."
  (axe 那个 0 用灰/绿,Ramp 那个 12 用醒目高亮色,形成强对比)

- 左栏(axe):标题 "axe-core (industry standard)",下面一个大大的 "0 issues",
  配一个"全部通过"的绿勾视觉。显得"它觉得这页很完美"。

- 右栏(Ramp):标题 "Ramp",下面 "12 semantic issues",列出每条(从 JSON 取),
  每条一张小卡片:
    · 元素(如 product image / search button / pricing link)
    · axe 判定:PASS(灰色小标)
    · Ramp 判定:NOT MEANINGFUL(红色小标)+ 原因(如 "filename, means nothing to screen readers")
    · Ramp 的修复建议(绿色):DSC_1042.JPG → "Aero Runner shoe"
  按 3 个页面(Acme dashboard / Northwind SaaS / Pace Athletics)分组显示。

视觉:深色背景,左栏冷淡(显得 axe 漏了),右栏高亮(显得 Ramp 抓到了)。
字要大(评委坐得远)。做完 pnpm dev 起来截图给我。
```
✅ 验收 🔴:屏幕上一眼看到"axe 说 0,Ramp 抓出 12 + 具体修复"。这是 demo 第 2 幕。

---

## 🔴 面板 2:修复前后分数跳变 + 真实 PR

展示"我们不报告,我们修复"的成果。

```
在 dashboard 加一个 "Auto-fix results" 面板,展示真实修复 PR 的成果(数据我会给你,
或读 scores 表)。每个 PR 一张卡片:
- repo 名 + 修复类别(如 "aigov-ops · landmarks")
- 大号分数跳变:92 → 100(用动画/箭头强调)
- axe 违规:5 → 0
- 一个 "View PR" 链接(指向真实的 GitHub PR)
横排展示 3-4 个真实 PR 卡片,体现"覆盖多个真实项目、多种修复类别"。
顶部一句:"Ramp doesn't just report — it fixes and opens a real PR."
只动 dashboard。做完截图给我。
```
✅ 验收:一排真实 PR 卡片 + 分数跳变,点 "View PR" 能到真实 GitHub PR。这是 demo 第 3 幕。

---

## 🟡 面板 3:三支柱概览(技术深度,次要)

```
加一个简洁的 "How it works" 概览:三支柱
① A11y-Bench:51 道真实 benchmark
② Harness:读屏模拟 + 对比度实测 + a11y 树(precision 84%)
③ Auto-fix loop:Claude Code 驱动,审计→修复→PR
做成三个图标卡片,简洁即可。只动 dashboard。
```

---

## 视觉与技术要求
- **深色主题**(你之前的风格),配色干净,**字号大**(demo 投屏,评委坐远)。
- 面板 1 的左右对比是重点 —— 左"全通过"、右"一堆被抓出",视觉反差越强越好。
- 用 React + Tailwind + shadcn(你已有的栈)。**不引入新复杂依赖。**
- 数据优先读现成的 vs-axe-report.json;PR 卡片的数据我会单独给你(repo/分数/PR链接)。

## git / 安全
- 只动 `apps/dashboard`,push 前 pull --rebase,前缀 `feat(dash):`。
- 别碰 harness/control-plane/shared;别明文贴 key;别用付费 API(dashboard 是纯前端,不调模型)。

## 优先级
面板 1(axe vs Ramp)🔴 最优先,这是 demo 王牌 → 面板 2(PR 卡片)🔴 → 面板 3 🟡 有时间再做。
**先把面板 1 做炸,其余其次。**

## 一句话
你在把"axe: 0 / Ramp: 12"这个杀手锏,从一行 JSON 变成评委一眼震撼的画面。面板 1 是 demo 第 2 幕的全部视觉,务必做到对比强烈、大字、一眼看懂。
