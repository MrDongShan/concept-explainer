# Concept Explainer

> 为 Obsidian 笔记场景设计的 Codex Skill——遇到概念直接问，自动生成结构化 Markdown 文章并建立双向链接。

## 安装

在 Codex 中说：

```
帮我安装 skill：https://github.com/<你的用户名>/concept-explainer
```

或者手动将仓库克隆到 `~/.codex/skills/concept-explainer/`：

```bash
git clone https://github.com/<你的用户名>/concept-explainer.git ~/.codex/skills/concept-explainer
```

安装后下一轮对话即可生效。

## 使用方式

在 Obsidian 笔记中打开一个 Markdown 文件，直接向 Codex 提问：

| 提问类型 | 示例 | 效果 |
|---------|------|------|
| 概念解释 | "X 是什么？" | 生成 `terms/X.md` + 提问处插入摘要和 `[[双向链接]]` |
| 对比 | "A 和 B 的区别？" | 生成对比文章 + 各自定义文章 |
| 深究 | "为什么会 X？" | 通过三问测试判断是否独立成篇 |
| 强制生成 | "生成 X 的文章" | 无条件生成，打上 `scope/子概念` 标签 |

## 触发条件

仅在 Markdown 笔记场景生效。代码问题、实现问题不会触发此 Skill。

## 文件结构

```
learn/
├── terms/          # 概念文章
├── 深究/           # 因果深究文章
└── 边注/           # 简短注释 & 拒绝记录
```

## 更多

完整的 Skill 设计文档见 [SKILL.md](SKILL.md)。
