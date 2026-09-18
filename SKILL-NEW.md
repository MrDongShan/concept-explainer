---
name: concept-explainer
description: "Generate explanatory Markdown articles for concept and terminology questions, and insert summaries with Obsidian backlinks at the question location. Maintains a YAML frontmatter tag system, handles disambiguation, and builds cross-article links within the learn/ workspace. Use when the user asks 'what is X', 'explain Y', 'X vs Y difference', 'why does X...', or any terminology/concept question while working with Markdown notes in /Users/mrdongshan/Documents/learn."
---

# Concept Explainer

Generate structured explanatory articles in the learn/ Obsidian vault for concept, comparison, and causal questions. Insert inline summaries at the point where the user asked, with bidirectional Obsidian links.

## Design Philosophy

Articles serve two layers:
- **信息层 (Information Layer)**: Quick lookup — what is X, why it matters, how it works. The "external hard drive" mode.
- **思维层 (Thinking Layer)**: Cognitive friction — contradictions with existing knowledge, missing bridge concepts, unexpected cross-domain connections. The "second brain" mode.

The collision section (⚡ 碰撞与张力) is the interface between Obsidian (knowledge storage) and Codex (dialogue). It plants questions in your articles that trigger real thinking when you re-read them — not soft "you might also like" suggestions, but sharp, uncomfortable questions that make you want to open Codex and chase the answer.

**Core principle**: Outward divergence over inward convergence. Every article should push the frontier outward, not just densify the existing network. The most valuable links are to what you DON'T yet know.

## Hard Gates

1. **Do NOT** write code, execute scripts, or perform destructive file operations (delete/merge/rename).
2. **Do NOT** auto-generate articles for sub-concepts mentioned within a generated article. Only generate what the user explicitly asked for. **However**: the collision section MUST explicitly list these missing concepts as frontier nodes in "待探索" and "缺失的桥梁".
3. **Do NOT** modify existing articles' cross-reference sections. Obsidian backlinks handle reverse links. **Exception**: collision scanning may reference existing articles BY NAME in the collision section of the new article only — do not edit old articles.
4. **Do NOT** create folders. Use existing directory structure only.

## Trigger Rules

| Question Type | Trigger? | Action |
|--------------|----------|--------|
| "What is X?" | ✅ | Generate `terms/X.md` + insert summary at question location |
| "A vs B difference?" | ✅ | Generate comparison article + individual definition articles per Four-Question Test |
| "Why does X...?" | ✅ | Run simplified Four-Question Test → pass: `深究/X.md`; fail: paragraph in parent concept article |
| "Generate article" (explicit) | ✅ | Unconditional generation + `scope/子概念` tag |
| "How to implement X?" / code questions | ❌ | Reject with `> ⚠️` block + suggest correct trigger |

## Four-Question Test (Independent Article Decision)

For each concept, ask in order:

| # | Question | Weight | Logic |
|---|---------|--------|-------|
| ① | Can it be explained in one sentence? | Veto | If NO → concept depends on prerequisites; check prerequisites, then return to ② |
| ② | Does it have independent knowledge structure? | Gate | **Most important.** Can expand at least 2 layers of sub-topics → pass |
| ③ | Will it be frequently cited by other articles? | Accelerator | YES → generate even if content is thin (useful as link target) |
| ④ | Does this concept point to knowledge territory the project doesn't yet cover? | Divergence | YES → elevate generation priority. Concepts that open new frontiers are MORE valuable than those that only densify existing territory. |

- ①② pass + no structure → write a paragraph in the parent concept article instead; do NOT generate standalone.
- ① fails + prerequisite missing → tell the user and suggest building the prerequisite first.
- User explicitly says "生成文章" → override all judgments, generate unconditionally + `scope/子概念`.
- When ④ is YES and ② is borderline → lean toward generating. Opening new territory is worth a thinner article.

## File Placement

```
Default:        learn/terms/
If current note is in a domain folder → sibling terms/ (e.g., learn/计算机/terms/)
No current note → learn/terms/
Causal (深究)    → learn/深究/
Marginalia (边注) → learn/边注/
Rejection record → learn/边注/拒绝-<concept>.md
```

## Article Format

### Standard Concept Article
```markdown
---
tags:
  - <domain>     # required, 1+ 
  - type/<type>  # required, 1+ (概念/机制/对比/事件/架构/方法/法则/深究/待验证)
  - scope/子概念  # optional, when user forced generation
  - importance/<level>  # optional (基石/转折点/前沿)
---

> ⚠️ **前置知识**：建议先阅读 [[prereq1]]、[[prereq2]]

> **一句话理解**：...

## 是什么？

（给出明确定义后，用直觉类比 + 具体例子让概念落地。不要停留在抽象定义。）

## 为什么需要知道？

（写真实的价值判断，不要泛泛而谈。能贴到读者的实际场景最好。）

## 核心机制/过程

（这是文章的主体。分层次展开，用对比表、流程图、时间线等结构化方式组织。）

## 直觉类比

（技术名词必填。找一个生活中或跨领域的类比——不是装饰性的，而是能真正帮助理解核心机制的。写清楚类比的对应关系：A 对应 B，不是"有点像"。）

## 反常识/常见误解

（所有文章必填，至少 1 条。写真正的认知冲突——读者（或写作者自己）最容易搞错的是什么？最常见的错误直觉是什么？这个概念的哪个结论最反直觉？不要写"你还可以了解 X"这类软性问题。）

---

## 延伸阅读

### 已有文档
- [[related-article]]
- 回到主线 → [[../main-note-path]]

### 待探索
- [[? 概念名]] — 值得深究的一句话理由。这个概念目前项目中没有，但和本文紧密相关。
```

- Prerequisites: existing articles first, placeholders last, max 3. Omit the line if none.
- Sub-concept articles: add `> 📎 本文是 [[parent]] 的子概念。` at top.
- Dubious concepts: add `> ⚠️ **注意**：这是一个较新的/不常见的术语，建议通过其他来源交叉验证。` and tag `type/待验证` (use ONLY this type, no regular type alongside).
- **待探索**: Use `[[? 概念名]]` notation for concepts that are worth exploring but don't yet exist in the project. The `?` prefix visually distinguishes frontier nodes from existing links. Write a concrete, specific reason — not "interesting topic" but "without understanding this, your grasp of X will break at step Y".
- Comparison articles (special): do NOT use the standard template. Each concept gets a paragraph overview (3-5 sentences), then core differences, then selection guide. Mechanism details stay in individual articles. Both link to each other.

### ⚡ 碰撞与张力 (Collision & Tension)

**All articles MUST include this section.** It creates the cognitive friction that turns a knowledge base into a thinking tool. Write real, uncomfortable questions — not soft suggestions.

```markdown
## ⚡ 碰撞与张力

### 与已有知识的矛盾
> 扫描项目中已有文档，找出与新文章核心主张矛盾或形成张力的判断。
> 写成让你停下来想的问题。如果扫描没发现矛盾，写"暂未发现直接矛盾"——不要强行编造。

### 缺失的桥梁
> 扫描新文章中提到但项目里没有的概念/工具/方法。
> 不只是列出名字——解释"没有这个节点，你对 X 的理解在哪一步会断掉"。
> 如果没有缺失，写"相关概念已基本覆盖"——不要强行编造。

### 意外的连接
> 扫描跨领域的意外关联——这篇文章的概念和方法论，和你项目中完全不同的领域有没有奇妙的共振？
> 如果没有发现，写"暂未发现意外跨领域连接"——不要强行编造。
```

**Quality requirements for collision questions**:

| ❌ Soft question (no friction) | ✅ Sharp question (real friction) |
|-------------------------------|----------------------------------|
| "你还可以了解 bitsandbytes" | "QLoRA 说压缩几乎无损——如果你相信这个结论，那你之前写的 Scaling Law 笔记是不是需要修正？" |
| "建议阅读 NF4 相关文献" | "NF4 是 4-bit，为什么不是 3-bit 或 2-bit？降到多少比特信息损失会突然崩掉？为什么恰好是 4？" |
| "HuggingFace 有相关教程" | "你下一个动手实验用 unsloth 跑 QLoRA——如果 loss 降不下去，怎么判断是量化导致的精度损失还是数据问题？" |
| "这个概念和 XX 有点像" | "Dropout 和早停都解决过拟合，但一个让训练更难、一个让训练更短——为什么相反策略达成同一目的？你的直觉告诉你哪个更根本？" |

- Prefer false positives over false negatives — an imperfect but provocative question is more valuable than a missed real tension.
- If genuinely nothing to report in a sub-section, state it plainly. Do not fabricate.
- The collision section should NOT auto-generate follow-up articles. It's bait for the reader's curiosity — they'll open Codex when a question hooks them.

### Causal (深究/) Article
Filenames prioritize recognizability over format uniformity. Use natural topic phrasing (e.g., `AI为什么现在才火.md`). Same frontmatter rules. All standard sections apply, including collision section.

### Marginalia (边注/) File
Pure Markdown, no frontmatter. One file per question, named by question core (e.g., `边注/什么是机会成本.md`). No collision section needed.

## Summary Format (Inserted at Question Location)

All use `> 🤔` blockquote. Concept/causal/comparison queries use `⚡` for the core answer. Extended-thinking questions use `—` for sub-questions. Multi-concept queries use bullet lists without `⚡`. Place at section end (before next `###`).

| Type | Lines | Structure |
|------|-------|-----------|
| Concept query | 2-3 | `> 🤔` + `⚡ one-sentence answer` + `→ [[path\|detail]]` |
| Causal inquiry | 3-5 | `> 🤔` + causal chain summary + `→ [[深究/...\|detail]]` |
| Comparison | 2-3 | `> 🤔` + one-sentence core difference + analogy + links |
| Extended thinking | 3-5 | `> 🤔` + restate confusion + sub-questions + links |
| Multi-concept | 1/line | `> 🤔` + `— [[A]]: one-sentence` per concept |

- Max 5 summaries per section. On overflow → downgrade to `边注/` + insert `> 📋 更多讨论：— [[边注/file1]] — [[边注/file2]]` listing each file.
- Same concept in same section → update existing summary, never duplicate.
- Nested follow-ups → inline at same level (do NOT nest blockquotes).

## Collision Scanning

After writing the article body, 延伸阅读, and 待探索 sections, run a collision scan before finalizing:

### Step 1: Extract Core Claims
From the new article, identify 2-3 core claims (statements the article asserts as true). These are the hooks for contradiction detection.

### Step 2: Scan for Contradictions
Scan the project (title-level + tag-level is sufficient — scan filenames and YAML frontmatter tags of existing articles, NOT full article bodies):
- Find articles whose core claims might contradict or create tension with the new article's claims
- Example: new article says "compression is nearly lossless" → search for articles about "scaling", "parameters", "model size" → Scaling Law article says "more parameters = better"
- Write the contradiction as a question that makes the reader think, not a statement of resolution

### Step 3: Scan for Missing Bridges
Identify concepts, tools, methods, or people mentioned in the new article that:
- Are critical to understanding the new concept, AND
- Don't have a corresponding article in the project
- List them with: name + "without this, your understanding breaks at step [具体哪一步]"

### Step 4: Scan for Unexpected Connections
Look for surprising cross-domain links:
- The new article's method/insight → applied in a completely different project domain
- A pattern in the new article that echoes a pattern in an unrelated existing article
- Even loose associations are valuable — they spark lateral thinking

### Step 5: Write the Collision Section
Compose the ⚡ 碰撞与张力 section following the template above. Quality checklist before finalizing:
- [ ] Are the contradiction questions genuinely uncomfortable? (Would the reader pause and think?)
- [ ] Do the missing bridge descriptions explain exactly WHERE understanding breaks without this node?
- [ ] Are the unexpected connections actually surprising? (Not just "X and Y are both about AI")
- [ ] Could a reader finish this section and NOT feel curious about at least one thing? If so, revise.

**DO NOT auto-generate articles for the missing bridges or frontier nodes.** The collision section's job is to surface what's missing, not fill it. The reader decides what to chase next.

## Deduplication & Conflict

| Scenario | Action |
|----------|--------|
| Exact same concept (file exists) | Skip generation, insert summary + link only. However, if the existing article lacks a collision section, note this to the user. |
| Same name, different domain (context clear) | Auto-generate with domain suffix (e.g., `过拟合（计量经济学）.md`), add mutual disambiguation links |
| Same name, ambiguous domain | Ask: point to existing article, offer two options. If user says "生成" without specifying → default to disambiguated version |
| Rejection record exists, user says "生成文章" | **Unconditional generation** (user intent is highest priority). Generate + tell why previously rejected |
| Rejection record exists, citation needs changed | Regenerate |
| Rejection record exists, conditions unchanged | Skip, tell user previous rejection reason |

**Priority**: User explicit command > Four-Question Test > Rejection record.

Rejection records: `边注/拒绝-<concept>.md` with date, reason, parent article, re-trigger conditions.

## Out-of-Bounds Behavior

| Scenario | Behavior | Output |
|----------|----------|--------|
| Non-concept question | Reject + guide to correct trigger | `> ⚠️` block |
| Concept too small for standalone | Write to parent article, inform user | `> 💡` block: one-line answer + location. e.g., `> 💡 epoch = 模型看完整个训练集的一轮。已补充到 [[梯度下降]] 中。` |
| Concept existence uncertain | Generate + tag `type/待验证` + top warning | Normal article + warning |
| Knowledge cutoff (needs latest info) | Do NOT fabricate. Invite user to provide materials. | `> ⚠️` block |
| Destructive operation | Refuse | `> 🚫` block |
| Ambiguous input | List existing + ask for context | `> ⚠️` block |

## Cross-Article Links

- **内部链接 (Internal Links)**: When generating a new article, cite known related articles in the "已有文档" subsection. The agent may do a lightweight title-level check (scan filenames only, not article bodies) after writing.
- **外部节点 (External Frontier Nodes)**: Concepts mentioned in the article that don't exist in the project go in the "待探索" subsection with `[[? concept-name]]` notation. Each must include a concrete, specific reason for exploration — not generic "interesting topic" language.
- Do NOT auto-update older articles. Obsidian's backlinks panel handles reverse links.
- Shallow article convergence: when ≥3 `scope/子概念` articles accumulate in the same domain, suggest a consolidation/comparison article. Non-mandatory.
- **The collision section may reference existing articles by name as part of contradiction/connection analysis. This is the only exception to the "do not modify old articles" rule — and even then, only reference, never edit.**

## Tags Reference

**type**: `概念` `机制` `对比` `事件` `架构` `方法` `法则` `深究` `待验证`
**scope**: `子概念` (only when user forced generation)
**importance**: `基石` `转折点` `前沿` (optional; omit if uncertain)

## YAML Validation

Before writing, verify: `---` wrapper, tags are a list, colon-space after keys, no illegal characters (`[ ] { } : # ,`). Auto-fix silently on failure.

## Full Design Document

See `/Users/mrdongshan/Documents/learn/AI-for-Everyone-terms/概念解释Skill设计结论.md` for the complete design rationale, edge case discussions, and pending verification items.
