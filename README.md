<p align="center">
  <img src="assets/banner.webp" alt="Deep Review" width="100%">
</p>

# Deep Review

> **🐧 中文網頁版** — [penchan.co/ai/introducing-deep-review](https://penchan.co/ai/introducing-deep-review/)

**From articles to architecture decisions**

---

> **Mission:** Give AI agents a research methodology — a structured way to
> evaluate external resources before deciding what to adopt.
> Not copying, but learning with discipline.

You read a great article. New tips, better workflows, smarter prompts.
But should you actually change anything?

**deep-review** is a skill for Claude Code that answers this question. Instead
of going with your gut, it runs each recommendation through a structured
pipeline and gives you a clear verdict: **adopt**, **experiment**, **reject**,
or **needs discussion**.

## Quick start

### Claude Code

1. Copy `deep-review.md` into your project or `~/.claude/` skills directory
2. Say `deep-review` and paste the article
3. Get a structured analysis with clear, actionable decisions

### Other AI tools

The skill file is just a structured prompt. You can adapt it for Cursor,
Windsurf, or any AI assistant that reads markdown instructions.

## The problem

We all do this:

1. Read an exciting article
2. Think "this is brilliant, I should use this"
3. Either adopt everything (and bloat the system) or do nothing (and forget it)

The issue isn't the articles — it's that we skip the analysis. We get swayed by
who wrote it, how new it sounds, or the urge to "do something." deep-review
adds the thinking step you'd do if you had unlimited time and patience.

<img src="assets/sections/pipeline.webp" alt="Pipeline" width="100%">

## How it works

Six phases. Each one builds on the last.

```
article --> FILTER --> EXTRACT --> DIFF --> ARGUE --> DECIDE --..-> AUDIT --> result
              |                                                 ^
              +-- exit: not our problem                     subagent
```

| Phase | What happens |
|-------|-------------|
| **0. Filter** | "Do we even have this problem?" If not, stop here. |
| **1. Extract** | Break the article into individual claims. Tag each one: data, case study, logic, or opinion. |
| **2. Diff** | Compare each claim to what your system already does. Pull up the actual files. |
| **3. Argue** | For each claim: the case for, the case against. Cost, risk, missing info. |
| **4. Decide** | One decision card per claim. Adopt, experiment, reject, or flag for discussion. |
| **5. Audit** | Independent check for blind spots — runs as a **separate agent call** to avoid self-review bias. |

### Why is the audit separate?

When an AI evaluates its own output in the same breath, it almost always says
"looks good." Research shows this kind of self-review has
[near-zero discriminative power](https://arxiv.org/html/2412.05579).
Running the audit as a separate call fixes this.

## Design choices

**Why no role-play?**
Many prompts use personas like "Architect" and "Skeptic" debating each other.
This [doesn't actually work](https://arxiv.org/abs/2509.05396) in a single
generation — the AI can't reason independently for each role. We use structured
questions instead.

**Why no scores?**
Self-assigned scores (7/10, 85%) sound precise but are unreliable. The audit
checks for specific failure patterns instead — like "all claims adopted" or
"no counter-arguments given."

**Why Phase 0?**
Most articles solve problems you don't have. Catching this early saves tokens
and prevents unnecessary changes. "Do nothing" is a valid outcome.

## Research behind this

- [CheckEval](https://arxiv.org/abs/2403.18771) — Why checklists beat open-ended scoring
- [LLM-as-Judge research](https://arxiv.org/html/2406.12624) — Known biases and how to counter them
- [Multi-agent debate studies](https://arxiv.org/abs/2509.05396) — Why AI "debates" often make things worse
- [Heilmeier Catechism](https://www.darpa.mil/about/heilmeier-catechism) — DARPA's method for vetting proposals
- [Architecture Decision Records](https://adr.github.io/) — How engineering teams document decisions that stick

## Making it better over time

1. After each review, note what you actually adopted vs. skipped
2. Every 5-10 reviews, look for patterns in misjudged claims
3. Tweak the prompt — one change at a time, test it, keep or revert
4. Track versions in the file header

This follows the [autoresearch](https://github.com/karpathy/autoresearch)
philosophy: small, measured improvements — not wholesale rewrites.

## License

MIT

---

<h2 id="中文版">中文版</h2>

> **宗旨：** 讓 AI Agent 擁有一套研究方法論——快速且全面地評估外部資源，
> 再決定要不要採用。不是照抄，而是有紀律地學習。

你讀了一篇好文章。新技巧、更好的工作流程、更聰明的 prompt。
但你真的應該改什麼嗎？

**deep-review** 是一個 Claude Code 的 skill，幫你回答這個問題。不靠直覺，
而是把每個建議丟進結構化的 pipeline，給你明確的判定：**採用**、**實驗**、
**拒絕**、或**需要討論**。

## 快速開始

### Claude Code

1. 把 `deep-review.md` 複製到你的專案目錄或 `~/.claude/` skills 目錄
2. 輸入 `deep-review`，貼上文章
3. 得到結構化分析和明確的可執行決策

### 其他 AI 工具

skill 檔案就是一份結構化 prompt。你可以改寫後用於 Cursor、Windsurf，
或任何能讀 markdown 指令的 AI 助手。

## 問題

我們都做過這件事：

1. 讀到一篇讓人興奮的文章
2. 心想「太厲害了，我應該用這個」
3. 然後要嘛全盤照收（讓系統變臃腫），要嘛什麼都不做（然後忘了）

問題不在文章本身——而是我們跳過了分析。deep-review 補上你在時間精力無限時
會做的那一步思考。

## 運作方式

六個階段，環環相扣。

```
文章 --> 過濾 --> 提取 --> 比對 --> 論辯 --> 決策 --..-> 審計 --> 結果
          |                                            ^
          +-- 出口：跟我們無關                      subagent
```

| 階段 | 做什麼 |
|------|--------|
| **0. 過濾** | 「我們真的有這個問題嗎？」沒有就直接結束。 |
| **1. 提取** | 把文章拆成獨立的主張。標記證據類型：數據、案例、邏輯、或觀點。 |
| **2. 比對** | 把每條主張和系統現狀對照。打開實際檔案——不能含糊帶過。 |
| **3. 論辯** | 針對每條主張列出支持與反對。成本、風險、缺什麼資訊。 |
| **4. 決策** | 每條主張一張決策卡。採用、實驗、拒絕、或待討論。 |
| **5. 審計** | 獨立的盲點檢查——以**獨立的 agent 呼叫**執行，避免自我審查偏誤。 |

### 為什麼審計要獨立執行？

AI 在同一次生成中評估自己的輸出時，幾乎總是說「看起來不錯」。
研究顯示這種自我審查的[辨別力趨近於零](https://arxiv.org/html/2412.05579)。
將審計作為獨立呼叫執行，解決這個問題。

### 關於審計的定位

這裡的審計不是對來源文章做嚴格的真偽查核——deep-review 的核心態度是**學習**，
不是**複製**。Phase 5 審計的是分析過程本身的品質：有沒有盲點、偏見、或草率判斷。

## 設計選擇

**為什麼不用角色扮演？**
很多 prompt 讓「架構師」和「懷疑論者」辯論。但在單次生成中，
[這其實不管用](https://arxiv.org/abs/2509.05396)——AI 無法為每個角色獨立推理。
我們改用結構化提問。

**為什麼不打分？**
自評分數（7/10、85%）聽起來精確但不可靠。審計改為檢查特定的失敗模式——
比如「所有主張都被採用」或「沒有反對意見」。

**為什麼有 Phase 0？**
多數文章解決的問題你根本沒有。及早發現省下 token，避免不必要的改動。
「什麼都不做」是完全合理的結果。

## 背後的研究

- [CheckEval](https://arxiv.org/abs/2403.18771) — 為什麼 checklist 比開放式評分更好
- [LLM-as-Judge 研究](https://arxiv.org/html/2406.12624) — 已知偏見與應對方式
- [多 Agent 辯論研究](https://arxiv.org/abs/2509.05396) — 為什麼 AI「辯論」常常適得其反
- [Heilmeier Catechism](https://www.darpa.mil/about/heilmeier-catechism) — DARPA 的提案評估方法
- [Architecture Decision Records](https://adr.github.io/) — 工程團隊如何記錄可持續的決策

## 持續改善

1. 每次 review 後，記下實際採用了什麼、跳過了什麼
2. 每 5-10 次 review，找規律——哪些類型的主張容易判斷失誤？
3. 微調 prompt——一次改一個地方，測試，保留或回退
4. 在檔案開頭記錄版本

遵循 [autoresearch](https://github.com/karpathy/autoresearch) 的哲學：
小幅度、可衡量的改善——而非大規模重寫。

## 授權

MIT
