# tensei-skills

一组面向中文信息分析、研究扩写与仓库解读场景的 Codex Skills。

这个仓库主要收录可直接复用的 `SKILL.md` 能力包。大多数 skill 都围绕“把原始输入扩展成结构化、高信息密度、可执行的中文输出”来设计，适合用在内容分析、技术调研、仓库解读、产品判断和热点追踪等任务中。

## 仓库包含什么

当前仓库包含这些 skill：

| Skill | 用途 |
| --- | --- |
| `code-archaeology` | 基于 Git 历史做代码考古，分析架构演进、关键决策与技术迁移轨迹。 |
| `daily-hotspot-info-gap-analyzer` | 将每日热点口播或碎片文本整理成结构化中文分析，重点识别信息差与误读点。 |
| `github-trending-quick-deep-analyzer` | 对 GitHub Trending 或单个仓库做中文深度解读，覆盖价值、特性、证据和试用路径。 |
| `hackernews-topn-explainer` | 抓取 Hacker News 榜单并输出结构化中文深读。 |
| `interview-question-bank` | 基于 JD、岗位方向或技术主题，结合公开招聘信息生成高频面试题库。 |
| `post-deep-dive-analyst` | 把社交媒体帖子及评论区扩展成高信息密度的长文分析。 |
| `product-launch-analysis` | 对产品做立项前分析，覆盖需求、竞品、商业价值、风险与验证计划。 |
| `repo-architecture-analyst` | 从本地代码仓库中提炼系统场景、技术挑战与对应解决方案。 |
| `report-to-lecture` | 将文章、论文、研报或白皮书重写成可教学、可转发的中文讲义。 |
| `sdd-plane-bridge` | 将 superpowers 的计划与执行结果同步到 Plane，形成闭环状态流转。 |
| `short-insight-deep-dive` | 将短洞察、短帖或一句话经验扩展成高密度中文深度文档。 |
| `track-awesome-yesterday-analyzer` | 分析 Track Awesome List 昨日更新，并输出结构化中文报告。 |

## 目录约定

每个 skill 通常使用独立目录组织，常见结构如下：

```text
<skill-name>/
  SKILL.md
  references/
  agents/
  README.md
```

说明：

- `SKILL.md`：skill 的主入口，包含触发条件、工作流、输入输出约束与执行规则。
- `references/`：模板、报告骨架、补充说明或示例资料。
- `agents/`：需要分工执行时使用的子代理说明。
- `README.md`：单个 skill 的补充介绍；不是每个目录都会有。

仓库根目录中的 `skills.md` 是聚合后的技能文档，适合集中浏览或分发。

## 如何使用

如果你在 Codex / oh-my-codex 环境中使用这些 skills，通常有两种方式：

1. 将仓库中的 skill 目录接入你的 skills 搜索路径。
2. 直接阅读对应目录下的 `SKILL.md`，按其中定义的触发词、输入格式和工作流执行。

选择 skill 时，优先看两件事：

- 你的输入对象是什么：仓库、文章、帖子、热点、岗位 JD，还是产品。
- 你要的输出是什么：讲义、长文分析、题库、架构解读，还是追踪报告。

## 适合谁

这个仓库更适合以下场景：

- 需要把短内容扩写成高价值中文分析文档的人
- 需要快速读懂代码仓库、产品或技术趋势的人
- 需要把公开信息转成研究材料、培训材料或决策材料的人
- 需要一套可复用的中文研究型 skill 模板的人

## 维护说明

- 优先保持 skill 的输入输出边界清晰。
- 能复用已有模板时，不额外新增抽象层。
- 如果新增 skill，建议至少补齐 `SKILL.md`，并在需要时提供 `references/` 模板。
- 更新仓库级索引时，同步检查根目录 `skills.md` 是否仍然准确。
