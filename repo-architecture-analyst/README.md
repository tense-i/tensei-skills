# Repo Architecture Analyst（仓库技术方案逆向分析器）

Auto-trigger keywords: 分析架构, 技术方案, 解决方案, 场景分析, 仓库分析, 架构解读, 方案拆解, 技术设计, 设计意图, repo architecture, solution architecture, scenario analysis, 技术挑战, 方案分析, 逆向分析, 架构师视角

## Purpose

以**解决方案工程师 + 架构师**视角分析本地代码仓库——从代码中提炼系统面临的核心场景与技术挑战，逆向推导每个场景的技术解决方案。

## Core Approach

**不是按模块罗列，而是按场景组织**：
1. 从代码中提炼 N 个核心场景/技术挑战
2. 针对每个场景，分析项目给出的技术解决方案
3. 解读方案的设计取舍、优劣评价与替代路径

## Usage

提供本地仓库路径：

```
分析这个仓库的技术方案：/path/to/repo
```

## Input

- **repo_path**（必填）：本地仓库绝对路径
- **focus_scenarios**：重点关注的场景（可选）
- **depth**：分析深度（standard/deep/exhaustive）

## Output

一份 > 1000 行的**场景驱动**技术方案分析文档（Markdown + Mermaid），包含：
0. 项目概览与技术栈
1. 场景全景图（提炼所有核心场景）
2. 逐场景方案深度分析（8 维度：问题定义→方案概述→方案架构→设计决策→异常处理→方案评价→替代对比→复用性）
3. 方案间关联与整体架构还原
4. 横切方案分析（错误处理/安全/可观测/测试/部署）
5. 方案质量总评与改进建议
附录：场景-方案映射表、设计决策清单、Mermaid 图索引

## See Also

- `SKILL.md` — 完整文档与执行流程
- `references/architecture-doc-template.md` — 输出模板
- `references/scenario-extraction-guide.md` — 场景提炼指南
- `references/pattern-catalog.md` — 模式识别目录
- `references/quality-assessment.md` — 质量评估框架
