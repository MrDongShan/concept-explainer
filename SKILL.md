---
name: concept-explainer
description: "Generate explanatory Markdown articles for concept and terminology questions, and insert summaries with Obsidian backlinks at the question location. Maintains a YAML frontmatter tag system, handles disambiguation, and builds cross-article links within the learn/ workspace. Use when the user asks 'what is X', 'explain Y', 'X vs Y difference', 'why does X...', or any terminology/concept question while working with Markdown notes in ~/Documents/learn."
---

# Concept Explainer

Generate structured explanatory articles in the learn/ Obsidian vault for concept, comparison, and causal questions. Insert inline summaries at the point where the user asked, with bidirectional Obsidian links.

## Hard Gates

1. **Do NOT** write code, execute scripts, or perform destructive file operations (delete/merge/rename).
2. **Do NOT** recursively generate articles for sub-concepts mentioned within a generated article. Only generate what the user explicitly asked for.
3. **Do NOT** modify existing articles' cross-reference sections. Obsidian backlinks handle reverse links.
4. **Do NOT** create folders. Use existing directory structure only.

## Trigger Rules

| Question Type | Trigger? | Action |
|--------------|----------|--------|
| "What is X?" | ✅ | Generate `terms/X.md` + insert summary at question location |
| "A vs B difference?" | ✅ | Generate comparison article + individual definition articles per Three-Question Test |
| "Why does X...?" | ✅ | Run simplified Three-Question Test → pass: `深究/X.md`; fail: paragraph in parent concept article |
| "Generate article" (explicit) | ✅ | Unconditional generation + `scope/子概念` tag |
| "How to implement X?" / code questions | ❌ | Reject with `> ⚠️` block + suggest correct trigger |

## Three-Question Test (Independent Article Decision)

For each concept, ask in order:

| # | Question | Weight | Logic |
|---|---------|--------|-------|
| ① | Can it be explained in one sentence? | Veto | If NO → concept depends on prerequisites; check prerequisites, then return to ② |
| ② | Does it have independent knowledge structure? | Gate | **Most important.** Can expand at least 2 layers of sub-topics → pass |
| ③ | Will it be frequently cited by other articles? | Accelerator | YES → generate even if content is thin (useful as link target) |

- ①② pass + no structure → write a paragraph in the parent concept article instead; do NOT generate standalone.
- ① fails + prerequisite missing → tell the user and suggest building the prerequisite first.
- User explicitly says "生成文章" → override all judgments, generate unconditionally + `scope/子概念`.

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

（灵活补充：技术名词→直觉类比+例子；对比→对比表；事件→为什么重要）

---

## 延伸阅读
- [[related-article]]
- 回到主线 → [[../main-note-path]]
```

- Prequisites: existing articles first, placeholders last, max 3. Omit the line if none.
- Sub-concept articles: add `> 📎 本文是 [[parent]] 的子概念。` at top.
- Dubious concepts: add `> ⚠️ **注意**：这是一个较新的/不常见的术语，建议通过其他来源交叉验证。` and tag `type/待验证` (use ONLY this type, no regular type alongside).
- Comparison articles (special): do NOT use the standard template. Each concept gets a paragraph overview (3-5 sentences), then core differences, then selection guide. Mechanism details stay in individual articles. Both link to each other.

### Causal (深究/) Article
Filenames prioritize recognizability over format uniformity. Use natural topic phrasing (e.g., `AI为什么现在才火.md`). Same frontmatter rules.

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

**Priority**: User explicit command > Three-Question Test > Rejection record.

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

- When generating a new article, the agent already has context of the existing file system. Write "延伸阅读" naturally citing known related articles.
- For comprehensive coverage, the agent may do a lightweight title-level check (scan filenames only, not article bodies) after writing.
- Do NOT auto-update older articles. Obsidian's backlinks panel handles reverse links.
- Shallow article convergence: when ≥3 `scope/子概念` articles accumulate in the same domain, suggest a consolidation/comparison article. Non-mandatory.

## Tags Reference

**type**: `概念` `机制` `对比` `事件` `架构` `方法` `法则` `深究` `待验证`
**scope**: `子概念` (only when user forced generation)
**importance**: `基石` `转折点` `前沿` (optional; omit if uncertain)

## YAML Validation

Before writing, verify: `---` wrapper, tags are a list, colon-space after keys, no illegal characters (`[ ] { } : # ,`). Auto-fix silently on failure.

## Full Design Document

See `~/Documents/learn/AI-for-Everyone-terms/概念解释Skill设计结论.md` for the complete design rationale, edge case discussions, and pending verification items.
