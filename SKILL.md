---
name: concept-explainer
description: "Generate explanatory Markdown articles for concept and terminology questions, and insert summaries with Obsidian backlinks at the question location. Maintains a YAML frontmatter tag system, handles disambiguation, and builds cross-article links within the learn/ workspace. Use when the user asks 'what is X', 'explain Y', 'X vs Y difference', 'why does X...', or any terminology/concept question while working with Markdown notes in /Users/mrdongshan/Documents/learn."
---

# Concept Explainer

Generate structured explanatory articles in the learn/ Obsidian vault for concept, comparison, and causal questions. Insert inline summaries at the point where the user asked, with bidirectional Obsidian links.

**Core philosophy**: Balance internal coherence (linking existing articles) with external openness (marking knowledge frontiers). Every article should both consolidate what's known AND point to what's next.

## Hard Gates

1. **Do NOT** write code, execute scripts, or perform destructive file operations (delete/merge/rename).
2. **Do NOT** recursively generate articles for sub-concepts mentioned within a generated article. Only generate what the user explicitly asked for. (Marking them as "待探索" external nodes is allowed — see Article Format.)
3. **Do NOT** modify existing articles' cross-reference sections. Obsidian backlinks handle reverse links.
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
| ④ | Does this concept point to knowledge areas the project doesn't yet cover? | **Expansion** | YES → elevate generation priority. Concepts that open new territory are more valuable than those that only reinforce existing clusters. Even if content is thin, generating this article marks a knowledge frontier and invites further exploration. |

- ①② pass + no structure → write a paragraph in the parent concept article instead; do NOT generate standalone.
- ① fails + prerequisite missing → tell the user and suggest building the prerequisite first.
- User explicitly says "生成文章" → override all judgments, generate unconditionally + `scope/子概念`.
- **④ takes precedence over ③ in priority conflicts**: a frontier-opening concept beats a frequently-cited-but-inward-looking one.

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
## 为什么需要知道？
## 核心机制/过程
## 直觉类比
## 反常识/常见误解
## 待探索问题

（灵活补充：技术名词→直觉类比+例子；对比→对比表；事件→为什么重要）

---

## 延伸阅读
### 已有文档
- [[related-article]]
- 回到主线 → [[../main-note-path]]

### 待探索
> 以下概念目前项目尚无文档，但与本主题深度相关，值得进一步探究：
- **概念A** — 为什么值得探索（一句话理由）
- **概念B** — 为什么值得探索（一句话理由）
```

#### Section Guidelines

**直觉类比** (required for all technical concepts):
- Must include at least ONE concrete, everyday-life analogy. Not optional.
- Analogy should map key mechanism to something the reader has definitely experienced.
- Bad: "神经网络像人脑" (vague, hand-wavy). Good: "反向传播像打台球——你击球后发现偏了，于是调整下一次的力度和角度，逐渐逼近目标。"

**反常识/常见误解** (required for all articles):
- List 1-3 things that people commonly get wrong about this concept.
- Frame as "你以为 X，其实是 Y" to create tension and memorability.
- If there's truly no common misconception, state "目前没有广泛传播的误解" — but only after genuinely trying to find one.

**历史脉络** (use when the concept has an evolution story — predecessor → breakthrough → successor):
- Not a dry timeline. Answer: "为什么在那个时间点出现？之前的技术卡在哪里？它的出现改变了什么？"
- Example: not "1998 年 LeNet 出现，2012 年 AlexNet 出现" but "在 GPU 足够快之前，CNN 只是理论玩具，AlexNet 证明了大算力+大数据的组合能解锁 CNN 的真正威力。"

**待探索问题** (required for all articles):
- List 2-3 open questions or adjacent concepts that this article naturally raises.
- These are NOT links to existing articles. They are genuine knowledge frontiers.
- Format each as a question: "XX 和 YY 之间是什么关系？" / "如果 AA 条件不成立会怎样？"
- This section is the primary mechanism for breaking the information cocoon — it should always point outward.

#### Other Rules

- Prequisites: existing articles first, placeholders last, max 3. Omit the line if none.
- Sub-concept articles: add `> 📎 本文是 [[parent]] 的子概念。` at top.
- Dubious concepts: add `> ⚠️ **注意**：这是一个较新的/不常见的术语，建议通过其他来源交叉验证。` and tag `type/待验证` (use ONLY this type, no regular type alongside).
- Comparison articles (special): do NOT use the standard template. Each concept gets a paragraph overview (3-5 sentences), then core differences, then selection guide. Mechanism details stay in individual articles. Both link to each other.

### Causal (深究/) Article
Filenames prioritize recognizability over format uniformity. Use natural topic phrasing (e.g., `AI为什么现在才火.md`). Same frontmatter rules. Same section requirements as standard articles.

### Marginalia (边注/) File
Pure Markdown, no frontmatter. One file per question, named by question core (e.g., `边注/什么是机会成本.md`).

## Summary Format (Inserted at Question Location)

All use `> 🤔` blockquote. Concept/causal/comparison queries use `⚡` for the core answer. Extended-thinking questions use `—` for sub-questions. Multi-concept queries use bullet lists without `⚡`. Place at section end (before next `###`).

| Type | Lines | Structure |
|------|-------|-----------|
| Concept query | 2-3 | `> 🤔` + `⚡ one-sentence answer` + `→ [[path|detail]]` |
| Causal inquiry | 3-5 | `> 🤔` + causal chain summary + `→ [[深究/...|detail]]` |
| Comparison | 2-3 | `> 🤔` + one-sentence core difference + analogy + links |
| Extended thinking | 3-5 | `> 🤔` + restate confusion + sub-questions + links |
| Multi-concept | 1/line | `> 🤔` + `— [[A]]: one-sentence` per concept |

- Max 5 summaries per section. On overflow → downgrade to `边注/` + insert `> 📋 更多讨论：— [[边注/file1]] — [[边注/file2]]` listing each file.
- Same concept in same section → update existing summary, never duplicate.
- Nested follow-ups → inline at same level (do NOT nest blockquotes).

## Deduplication & Conflict

| Scenario | Action |
|----------|--------|
| Exact same concept (file exists) | Skip generation, insert summary + link only |
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

### Internal Links (已有文档)
- When generating a new article, the agent already has context of the existing file system. Write "延伸阅读 → 已有文档" naturally citing known related articles.
- For comprehensive coverage, the agent may do a lightweight title-level check (scan filenames only, not article bodies) after writing.
- Do NOT auto-update older articles. Obsidian's backlinks panel handles reverse links.

### External Nodes (待探索)
- **MUST** include 2-3 external nodes in every article's "待探索" section. These are concepts the project doesn't yet cover but that are naturally connected to the current topic.
- External nodes are NOT links — they are plain text markers. Format: `- **概念名** — 一句话说明为什么值得探索`
- Selection criteria for external nodes: (a) directly related to the current concept's mechanism or implications, (b) would deepen or broaden understanding if explored, (c) not already covered by any existing article.
- This is the primary anti-cocoon mechanism. Every article must open windows, not just close doors.

### Convergence
- Shallow article convergence: when ≥3 `scope/子概念` articles accumulate in the same domain, suggest a consolidation/comparison article. Non-mandatory.

## Exploration Guide Generation Strategy

When generating or updating exploration guides (探索指南), event timelines (事件线), or learning roadmaps (实战路线):

1. **List milestones first, then check coverage.** Enumerate important milestones / topics in the domain based on the field's actual history and knowledge structure — independent of whether the project has corresponding articles. This prevents the guide from becoming just a rearrangement of existing documents.

2. **Mark coverage status.** After listing all milestones, annotate each:
    - ✅ 已有 — project has a corresponding article
    - 📝 待写 — important but no article yet

3. **Treat gaps as next steps, not flaws.** Missing articles are the user's natural exploration path. Frame them as "接下来你可以探索的方向" rather than deficiencies.

4. **Cross-reference generously but don't force.** Guide-to-article links should be natural. Don't fabricate links to non-existent articles.

5. **Divergence before convergence.** When structuring the guide, start with broad questions ("what's out there?") before narrowing. The guide should suggest more paths than it resolves.

## Tags Reference

**type**: `概念` `机制` `对比` `事件` `架构` `方法` `法则` `深究` `待验证`
**scope**: `子概念` (only when user forced generation)
**importance**: `基石` `转折点` `前沿` (optional; omit if uncertain)

## YAML Validation

Before writing, verify: `---` wrapper, tags are a list, colon-space after keys, no illegal characters (`[ ] { } : # ,`). Auto-fix silently on failure.

## Full Design Document

See `/Users/mrdongshan/Documents/learn/AI-for-Everyone-terms/概念解释Skill设计结论.md` for the complete design rationale, edge case discussions, and pending verification items.
