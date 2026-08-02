# comfy-anima-skill-share
This is an anima skill share
# Anima Prompt Skills

Systematic prompt-engineering skills for the **Anima** anime image model (circlestone-labs/Anima) — a family of 4 skills covering single-image prompting, NSFW deconstruction, long-form doujin storyboarding, and scene/background art.

为 **Anima** 动漫图像模型（circlestone-labs/Anima）编写的系统化提示词方法论 skill 集，共 4 个 skill，覆盖单图提示、NSFW 色气解构、长篇黄漫分镜、场景/背景绘制。

> **⚠️ 18+ 成人内容警告 / Adult Content Warning**
> 本仓库含 **NSFW / 成人 / 露骨性内容**的提示词方法论（`anima-nsfw-prompt`、`anima-doujin-plan`）。仅限成年人使用。使用者在下载、使用、转发本内容时须自行承担全部责任并遵守所在地法律。
> This repository contains **NSFW / adult / explicit sexual content** prompt methodology. For adults only. Users are solely responsible for compliance with local laws.

---

## What this is / 这是什么

Anima (circlestone-labs/Anima) is an open anime-focused text-to-image DiT model. Writing good Anima prompts well requires model-specific knowledge: tag syntax, prompt structure, negative-prompt strategy, character consistency, artist strategy. This repo packages that knowledge as **methodology + reusable rules**, not as a copy-paste example gallery.

Anima（circlestone-labs/Anima）是开源的动漫向文生图模型。写出好的 Anima 提示词需要模型特化知识：tag 语法、提示词结构、负面策略、人物一致性、画师策略。本仓库把这类知识整理为**方法论 + 可复用规则**，而不是堆砌可照抄的例句。

- **Methodology over examples / 方法论优先于例句**：每个 skill 给出"如何思考/如何拆解"，可泛化到任意新案例，而非要求照抄某个案例。
- **Intent-driven / 意图驱动**：先解构"为什么色/要表达什么"，再决定视觉、模板、负面。

## Skills / 四个 skill

| Skill | Purpose / 用途 |
|---|---|
| `anima-prompt` | Core single-image prompting: intent analysis, templates A/B/C, intent-based negative system, character consistency, artist strategy, ComfyUI params. 核心单图提示词：需求拆解、模板 A/B/C、意图负面、人物一致性、画师策略、参数。 |
| `anima-nsfw-prompt` | NSFW deconstruction supplement: erotic-core analysis (find-core → innovate), event-to-visual-anchor derivation, atmosphere tips, common pitfalls. NSFW 特化补充：色气核心分析法、事件→视觉锚点推导、氛围技巧、常见坑。 |
| `anima-doujin-plan` | Long-form doujinshi (>10p) script & storyboard design: story arcs, event chains, beats, camera language, template allocation, storyboard-table output. 长篇黄漫（>10p）剧本与分镜设计：分幕、事件链、节奏、镜头语言、模板分配、分镜表产出。 |
| `anima-scene-prompt` | Pure scene / background / environment art. 纯场景/背景/环境绘制。 |

**Skill hierarchy / 职责边界**：
- `anima-prompt` is the core — all templates/negatives/tags live there.
- `anima-nsfw-prompt` supplements the NSFW fragment inside `anima-prompt` (not a separate router).
- `anima-doujin-plan` plans *what to draw* for long-form; `anima-prompt` writes *how to express* each page.
- `anima-scene-prompt` routes pure scenes (no main character).

## Requirements / 依赖

- **Anima** model (open-source, see [circlestone-labs/Anima](https://huggingface.co/circlestone-labs/Anima)); any of Aesthetic / Turbo / Base variants.
- A local **ComfyUI** installation (Anima has native support in recent ComfyUI).
- These are skill files (markdown instruction packs) for an AI agent (e.g. Claude Code `~/.claude/skills/`); place each under your agent's skills directory and invoke by name.

这些是给 AI 智能体（如 Claude Code）的 skill 文件（Markdown 指令包），放入 `~/.claude/skills/<skill-name>/SKILL.md` 后按名调用。

## Usage / 使用

1. Single image: invoke `anima-prompt` (add `anima-nsfw-prompt` for NSFW, `anima-scene-prompt` for pure scenes).
2. Long-form doujinshi (>10p): invoke `anima-doujin-plan` first for script/storyboard, then `anima-prompt` to write per-page prompts.
3. Each skill is self-contained — read the skill, follow its methodology top to bottom.

## Notes / 说明

- Prompt weighting, artist tags (`@name`), and negative strategy are Anima-specific — verified through iteration, not guessed.
- Negative templates are provided in full (CORE/SAFE/NSFW/EXPL) — intent-based additions are described in each skill.

## License / 许可证

MIT — see [LICENSE](LICENSE). The methodology and text are free to use, modify, and redistribute with attribution-free MIT terms.
