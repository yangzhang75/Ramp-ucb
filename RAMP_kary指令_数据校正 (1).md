# RAMP · kary(Codex)指令 — Dashboard 数据校正(demo 前必做)
你的 dashboard demo 面板做得很好(6 个 tab、axe vs Ramp、auto-fix、precision),界面保留。
现在只校正**数据**,让它经得起评委核实。你只动 `apps/dashboard` 和 `packages/bench` 的数据文件。

> ⚠️ 这些是"demo 前会被评委核实"的东西,数据必须真实、链接必须对。每改完一项停下贴给我核对。

---

## 🔴 第 1 项(最重要,诚信硬伤):修 aigov-ops 的 PR 错链接

现在 `auto-fix-results.json` 和 `fix-outcomes.json` 里,aigov-ops 那张卡的 PR 链接指向:
```
https://github.com/bobrapp/aigov-ops-open-source-vendor-rfi-rapp-johnston-june-2026/pull/12
```
**这是错的** —— 那是当初挖 benchmark 用的"人类原始 a11y PR"(标准答案来源),**不是 Ramp 自己开的**。评委点开会以为我们拿别人的 PR 冒充自己的。

**换成我们 fork 上真实开的 PR:**
```
https://github.com/yangzhang75/aigov-ops-open-source-vendor-rfi-rapp-johnston-june-2026/pull/1
```

✅ 同时核对**所有** auto-fix 卡片的 PR 链接,确保每个都指向 yangzhang75 fork 上的真实 Ramp PR(下面有完整清单)。

---

## 🔴 第 2 项:precision 数字 —— 先确认,再统一

**先回答我一个问题**:dashboard 里那份 precision 三次平均(naked 84.4/82.7、harness 77.1/83.1),
**是你真的跑 `score:fixtures` 跑出来的,还是估/手填的?**

- **如果是真跑的**(有日志/输出)→ 告诉我,我们对齐一下,用一套统一的真实数字。
- **如果是估的**(没真跑)→ 换成下面员工真实跑的 3 次数据:

```
真实 3 次跑分(score:fixtures,gpt-4o-mini,10 页 fixtures):
Run 1: naked 87.5%/84.8% (28/33)  | harness 78.1%/80.6% (25/31)
Run 2: naked 81.3%/78.8% (26/33)  | harness 78.1%/89.3% (25/28)
Run 3: naked 87.5%/84.8% (28/33)  | harness 81.3%/83.9% (26/31)

平均(micro-average,TP/detected 在 3 次上汇总,expected=32×3=96):
naked:   recall 85.4% / precision 82.8%  (82/99)
harness: recall 79.2% / precision 84.4%  (76/90)
结论:recall 基本打平(naked +6pt),harness precision 略高(+1.6pt)。
```

要改的文件(两个,互为镜像,都要改成上面的真实数字):
- `packages/bench/data/fixture-precision-runs.json`
- `apps/dashboard/src/data/complex-precision.json`

> 关键:不管用你的还是员工的,**最终 dashboard 显示的、Devpost 写的、demo 说的必须是同一套**,别两套并存。

---

## 🟡 第 3 项:补两张真实 PR 卡片(让"真实修复"有量)

现在 auto-fix 只有 bad-html / aigov-ops / garbage-names 三张,缺这两个真实 PR,补进 `auto-fix-results.json`:

```json
{
  "id": "whatifarcade",
  "repo": "flyingtigerstrat-coder/Whatifarcade",
  "category": "Form labels · landmarks",
  "title": "Real arcade site — label + main landmark",
  "prUrl": "https://github.com/yangzhang75/Whatifarcade/pull/1",
  "beforeScore": 96, "afterScore": 100,
  "beforeAxeViolations": 2, "afterAxeViolations": 0
},
{
  "id": "caelaria",
  "repo": "Hush1e/caelaria",
  "category": "Form labels (select)",
  "title": "Escape-room module — unlabeled selects",
  "prUrl": "https://github.com/yangzhang75/caelaria/pull/1",
  "beforeScore": 84, "afterScore": 92,
  "beforeAxeViolations": null, "afterAxeViolations": null
}
```
(若你的卡片字段带 screenReaderBefore/After,按你现有格式补全。)

---

## 📋 所有真实 Ramp PR 清单(全开在 yangzhang75 fork 上,用于核对每张卡)

| demo | fork 仓库 | PR | 分数 | axe |
|---|---|---|---|---|
| bad.html | yangzhang75/Ramp | #7 | 60→96 | 5→0 |
| aigov-ops | yangzhang75/aigov-ops-…june-2026 | #1 | 92→100 | 2→0 |
| caelaria | yangzhang75/caelaria | #1 | 84→92 | — |
| Whatifarcade | yangzhang75/Whatifarcade | #1 | 96→100 | region→0 |
| HDcredit(可选) | yangzhang75/HDcredit1.2 | #1 | 68→68(真实修复,分数平) | — |

---

## ✅ 改完的验收标准
- [ ] aigov-ops 卡片链接 = yangzhang75 fork 的 PR(不再是 bobrapp 上游)
- [ ] 所有卡片 PR 链接点开都是 Ramp 真实开的 PR
- [ ] precision 数字统一成一套真实数据(dashboard = Devpost = demo 说的)
- [ ] 补了 caelaria + Whatifarcade 两张卡
- [ ] pnpm dev 起来,截图给我看

## 安全 / 边界
- 只动 apps/dashboard 和 bench 的数据文件,别碰 harness/control-plane/shared。
- push 前 pull --rebase。别明文贴 key。

## 一句话
你的界面做得好,这步只校准数据,让它经得起评委核实——尤其那个 aigov-ops 错链接(会穿帮)必须改。先回我 precision 是不是真跑的,再统一。
