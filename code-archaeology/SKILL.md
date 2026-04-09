```skill
---
name: code-archaeology
description: |
  以解决方案架构师视角，基于 Git 历史（log/diff/blame/PR）对代码仓库进行"考古式"演进分析。
  从提交记录、PR 历程、文件变更中反推系统的演进阶段、架构变迁、关键决策、技术债务清偿与方案迭代轨迹。
  输出一份超长演进分析文档，配合 Mermaid 时间线与架构快照。不需要 checkout 旧版本。

  Use when: 用户要求分析仓库的 git 历史、演进过程、重构历程、PR 记录，
  关键词包含"代码考古""演进分析""git 历史分析""重构历程""技术迁移""PR 分析""架构演变""历史回溯"。
user-invocable: true
metadata:
  author: tense-i
  version: "1.0"
---

# Code Archaeology（代码考古学｜Git 历史 → 系统演进方案解读）

**Status**: Production Ready
**Last Updated**: 2026-02-16
**Dependencies**: 需要访问本地 Git 仓库；可选 GitHub Token 用于 PR 导出

---

## Purpose

以**解决方案工程师 + 架构师**的复合视角，基于 Git 历史（不 checkout 旧版本）对仓库进行"考古式"分析。核心任务：

1. **演进分期**：从 git log 中划分系统的 N 个演进阶段（Epoch），每个阶段有清晰的主题
2. **方案迭代追踪**：追溯核心技术方案从"初始形态→重构→稳定态"的演化路径
3. **决策考古**：从 commit/PR/diff 中挖掘关键设计决策的时机、动机与取舍
4. **演进叙事**：将零散的 commit 串联成一条有因果逻辑的演进故事线

> 核心问题：**这个系统是怎么一步步长成现在这个样子的？每一步做了什么决策？为什么？**

### 视角定位

| 维度 | 说明 |
|------|------|
| **解决方案工程师** | 关注每个阶段引入/演化了什么方案、方案迭代的驱动力 |
| **架构师** | 关注架构演化轨迹、分层/拆分时机、技术债务与清偿 |
| **不是 Git 考古爱好者** | 不追求完整的 commit 清单，聚焦有"方案意义"的变更 |

### 与 repo-architecture-analyst 的区别

| 维度 | repo-architecture-analyst | code-archaeology |
|------|--------------------------|------------------|
| **时间轴** | 分析**当前态** | 分析**演进过程**（从 v0 到现在） |
| **数据源** | 代码本身 | Git 历史 + PR + Diff + Blame |
| **主轴** | 场景→方案 | 时间→阶段→方案演化 |
| **关注点** | 方案"是什么" | 方案"怎么来的、为什么变" |
| **输出** | 静态方案分析文档 | 动态演进叙事文档 |

---

## When to Trigger

当用户出现以下意图/关键词时触发：
- "分析这个仓库的 git 历史 / 演进历程"
- "这个项目是怎么一步步发展的"
- "分析 PR 记录 / commit 历史"
- "代码考古 / 架构演变分析 / 重构历程"
- "这个系统经历了哪些技术迁移"
- "帮我做仓库的演进分析"
- 用户提供仓库路径/URL 并要求分析历史

---

## Inputs（输入规范）

### 必填

| 参数 | 说明 |
|------|------|
| **repo_path** | Git 仓库本地路径（或先 clone 的 URL） |

### 可选（强烈建议）

| 参数 | 说明 | 默认值 |
|------|------|--------|
| **focus** | 关注方向（如：认证演进/数据层重构/前端架构/部署流水线） | 全面分析 |
| **time_window** | 时间范围（如：近6个月 / v1.0~v2.0 / 全部） | 全部 |
| **depth** | 深度（`standard` / `deep` / `exhaustive`） | `deep` |
| **pr_export_md** | 已导出的 PR markdown 文件路径（由辅助脚本生成） | 无（从 git log 分析） |
| **github_repo** | GitHub 仓库全名 owner/repo，用于在线获取 PR 信息 | 从 remote 自动获取 |

### 缺失信息处理规则
- 不反复追问；缺失信息从 git log / README 中主动获取
- focus 未指定 → 全面扫描所有维度
- PR 数据缺失 → 退回纯 git log 分析模式（仍可产出高质量文档）
- 可推断的标注 `[推断]`，不确定的标注 `[待确认]`

---

## Output（输出规范）

### 核心要求

1. **演进叙事驱动**：文档以"时间线→阶段→方案演化"为主轴，不是按 commit 罗列
2. **必须是长文档**：内容翔实完整，覆盖所有演进阶段
3. **证据标注**：所有关键判断必须标注 `[证据]` `[推断]` `[假设]`
4. **Mermaid 可视化**：时间线、架构快照对比、方案演化图
5. **方案视角**：不是"改了哪些文件"，而是"方案从 A 演进到 B，为什么"

### 证据链优先级

| 级别 | 来源 | 标注 |
|------|------|------|
| **一级证据** | commit message、diff 内容、tag、文件变更 | `[证据]` |
| **二级证据** | PR 标题/描述、Issue 关联、CI 配置变更 | `[证据]` |
| **三级** | 从变更模式/命名/重构类型推测原因 | `[推断]`（附依据） |
| **四级** | 无法确认的动机/外部约束 | `[假设]`（附验证建议） |

### 输出格式

按 `references/evolution-doc-template.md` 的章节结构输出（Markdown）。

### 多片追加协议

如果一次回复无法承载全部内容，按多片连续输出：

- 每片末尾注明下一片将覆盖哪些章节/阶段
- 用户回复"继续"或"继续 + 阶段/主题"即可获取下一片
- 跨片内容衔接自然，不遗漏章节

---

## Constraints（约束）

### 视角约束（核心）

**是解决方案架构师在做演进复盘，不是在做 git log 翻译**：

- ✅ "Epoch 3（2024 Q2）：团队引入 CQRS 模式将读写路径分离。从 PR #87 的 diff 看，原先的 OrderService 被拆分为 OrderCommandHandler + OrderQueryService，读侧增加了 Redis 缓存层。这标志着系统从单体读写混合架构向 CQRS 演进"
- ❌ "commit abc1234 修改了 order.ts 文件，增加了 50 行代码"
- ✅ "这次认证方案迁移（Session → JWT）分 3 个 PR 渐进完成：先引入 JWT 签发（PR #12），再迁移验证中间件（PR #15），最后删除 Session 依赖（PR #18）。渐进式迁移降低了风险"
- ❌ "PR #12 改了 auth.ts，PR #15 改了 middleware.ts"

**关注层次**：
- **阶段主题**：这个阶段的核心矛盾/驱动力是什么？
- **方案演化**：方案从什么形态变到什么形态？为什么变？
- **决策解读**：关键技术选择的时机、权衡与后果
- **架构轨迹**：架构边界何时出现/移动/消失？分层何时确立/打破？
- **技术债务**：何时积累？何时清偿？清偿成本多大？

### 不 checkout 约束

**禁止使用 `git checkout`、`git switch` 切换到旧版本**。

替代方案：
- `git show <commit>:<file>` → 查看某次提交时的文件内容
- `git diff <commit1>..<commit2>` → 查看两次提交之间的差异
- `git log --stat` → 查看每次提交的文件变更统计
- `git log -- <path>` → 查看某个文件/目录的变更历史
- `git blame <file>` → 查看文件每行的最后修改者
- `git log -p -- <file>` → 查看某文件的完整补丁历史
- `git diff <commit> -- <path>` → 查看某次提交对某文件的改动

### Mermaid 约束
- 必须包含至少 1 张演进时间线图（timeline / gantt）
- 必须包含至少 3 张架构快照对比（不同阶段的架构状态）
- 每张图必须配合文字解读
- Mermaid 代码块语法必须正确

### 诚信约束
- commit message / diff / tag / PR 信息 = 一级证据 → `[证据]`
- 从变更模式推断的动机和原因 → `[推断]`（附依据说明）
- 无法从代码历史确认的背景/约束 → `[假设]`（附验证建议）
- 严禁把推断当事实、把假设当推断

---

## Workflow（执行流程）

### Step 0 — 仓库画像（Repo Profiling）

**目标**：建立仓库的**当前态认知** + 统计信息概览。

1. **基础统计**：
   ```bash
   # 项目基本信息
   git rev-parse --show-toplevel
   git log --oneline | wc -l                    # 总 commit 数
   git log --reverse --oneline | head -1         # 首次提交
   git log --oneline -1                          # 最近提交
   git shortlog -sn --no-merges | head -20       # 贡献者排名
   git tag --sort=creatordate                    # 版本标签
   ```

2. **当前态快照**：
   - `tree` / `ls` 获取当前目录结构
   - 读取 README、CHANGELOG、package.json/pyproject.toml 等
   - 识别技术栈、构建系统、部署方式

3. **历史概览**：
   ```bash
   # 时间跨度与节奏
   git log --format="%ai" | head -1 && git log --reverse --format="%ai" | head -1
   # 月度提交分布
   git log --format="%Y-%m" | sort | uniq -c
   # 高频变更文件（热点文件）
   git log --name-only --pretty=format: | sort | uniq -c | sort -rn | head -20
   ```

4. **输出**：
   - 仓库体检表（当前态）
   - 开发节奏概览（活跃度/贡献者/里程碑）
   - 初步阶段划分假设

### Step 1 — 演进分期（Epoch Segmentation）

**目标**：把完整的 git 历史切分为 **5~12 个阶段（Epoch）**，每个阶段有明确的主题。

> Epoch = 项目生命中的一个"方案主题期"。不是按月切，而是按**方案变更的自然断层**切。

1. **分期信号识别**：

   | 信号类型 | 发现命令 | 解读 |
   |---------|---------|------|
   | tag/release | `git tag --sort=creatordate` | 版本里程碑 |
   | 目录大改 | `git log --diff-filter=R --summary` | 重命名/重组 → 架构重构 |
   | 关键词提交 | `git log --oneline --grep="refactor\|migrate\|breaking\|perf\|ci\|test\|docker\|k8s"` | 主题性变更 |
   | 依赖变更 | `git log -- "**/package.json" "**/go.mod" "**/Cargo.toml" "**/pyproject.toml"` | 技术栈演进 |
   | 月度提交量突变 | `git log --format="%Y-%m" \| sort \| uniq -c` | 活跃度断层 |
   | 新目录首次出现 | `git log --diff-filter=A --name-only` | 新模块诞生 |
   | 大体量 commit | `git log --shortstat` | 单次大改 → 里程碑 |

2. **分期方法**：
   - 先用 tag/release 做粗切（版本边界）
   - 再用关键词 commit + 目录变更做细分
   - 最后用语义合并（相邻的同主题阶段合并）
   - 每个 Epoch 取一个名字 + 一句话主题

3. **输出**：

   | Epoch | 名称 | 时间 | 主题 | 关键信号 |
   |-------|------|------|------|---------|
   | E1 | 最小可行版本 | 2023-01 ~ 2023-03 | 核心功能闭环 | first commit → v0.1 |
   | E2 | ... | ... | ... | ... |

### Step 2 — PR / MR 分析（如可用）

**目标**：从 PR 记录中提炼**方案级变更**与**决策讨论**。

> PR 是比 commit 更高层级的"方案变更单元"——一个 PR 通常对应一个完整的功能/方案引入/重构。

1. **PR 数据获取**（三种方式，按可用性选择）：

   | 方式 | 条件 | 命令/方法 |
   |------|------|---------|
   | 用户提供导出文件 | `pr_export_md` 参数 | 直接读取 |
   | 辅助脚本在线导出 | 有 GitHub Token | `python export_prs_to_md.py --repo owner/repo --out prs.md` |
   | 纯 git log 回退 | 无 PR 数据 | `git log --merges --oneline` 分析 merge commit |

2. **PR 分类与标注**：

   | PR 类别 | 方案意义 | 关注点 |
   |---------|---------|--------|
   | **Feature PR** | 新方案引入 | 方案设计、文件结构变化、依赖引入 |
   | **Refactor PR** | 方案迭代/重构 | 前后对比、驱动力、改进效果 |
   | **Fix PR** | 方案缺陷暴露 | bug 根因 → 方案的薄弱环节 |
   | **Infra PR** | 工程化方案演进 | CI/CD/测试/部署/监控的引入与变化 |
   | **Dependency PR** | 技术栈迁移 | 为什么换？新旧对比 |
   | **Breaking PR** | 方案断代 | 不兼容变更的原因与代价 |
   | **Draft/WIP PR** | 探索中的方案 | 团队在考虑什么方向？ |

3. **高价值 PR 深挖**（选 Top 10-20）：
   - PR 标题与描述 → 变更动机
   - 变更文件列表 → 影响范围
   - commit 序列 → 实施策略（一步到位 / 渐进式）
   - 讨论（如可获取）→ 决策权衡

4. **输出**：
   - PR 全景分类统计
   - 高价值 PR 卡片（方案意义 + 证据）
   - PR 与 Epoch 的对应关系

### Step 3 — 关键提交深挖（Key Commits Archaeology）

**目标**：从海量 commit 中挑出具有**方案级意义**的关键提交，做深度解读。

1. **挑选标准**（满足至少一条）：
   - 引入新方案/能力（"add X support / implement Y"）
   - 结构性重构（目录重组、核心抽象出现/重写）
   - 技术栈迁移（框架/依赖/构建系统替换）
   - 性能/可靠性方案（缓存/队列/重试/熔断/内存优化）
   - 工程化里程碑（CI/CD/测试/容器化首次出现）
   - bug 修复揭示的方案缺陷（回滚/hotfix/紧急修补）
   - 大体量变更（大 diff、大量新增/删除）

2. **关键提交分析命令**：
   ```bash
   # 查看某次提交的完整变更
   git show <commit> --stat
   git show <commit> -- <file>         # 看具体文件的改动
   git diff <commit>^..<commit>        # 看 diff

   # 查看某个文件在某次提交时的完整内容（替代 checkout）
   git show <commit>:<path/to/file>

   # 关键重构搜索
   git log --oneline --all --grep="refactor\|rewrite\|migrate\|redesign\|remove\|replace"

   # 大体量 commit
   git log --shortstat --format="%h %s" | grep -E "[0-9]{3,} (insertions|deletions)"
   ```

3. **每个关键提交输出一条"考古卡片"**：

   ```
   ┌─────────────────────────────────────────────┐
   │ 考古卡片 K{n}                                │
   ├─────────────────────────────────────────────┤
   │ Commit:  <hash> (<date>)                    │
   │ Author:  <name>                             │
   │ Message: <message>                          │
   │ Epoch:   E{n} - <epoch name>                │
   │ 类型:    新方案/重构/迁移/修复/工程化         │
   ├─────────────────────────────────────────────┤
   │ What Changed: 文件/模块级变化         [证据] │
   │ Why:          动机与驱动力     [证据/推断]    │
   │ Trade-off:    带来的复杂度/成本    [推断]     │
   │ Impact:       对架构/方案的影响    [推断]     │
   │ Follow-ups:   后续修补/回滚       [证据]     │
   └─────────────────────────────────────────────┘
   ```

4. **选取数量参考**：

   | 总 commit 数 | 建议关键提交数 |
   |-------------|-------------|
   | < 100 | 5-10 |
   | 100-500 | 10-20 |
   | 500-2000 | 15-30 |
   | > 2000 | 20-40 |

### Step 4 — 架构演化快照（Architecture Snapshots）

**目标**：为每个 Epoch 还原"那个阶段的架构状态"（通过 diff 推断，不 checkout）。

1. **快照还原方法**：

   | 方法 | 命令 | 用途 |
   |------|------|------|
   | 某 commit 时的目录结构 | `git ls-tree -r --name-only <commit> \| head -50` | 还原当时的文件组织 |
   | 某 commit 时的文件内容 | `git show <commit>:<file>` | 读取当时的代码/配置 |
   | 两个阶段之间的 diff | `git diff <epoch_start>..<epoch_end> --stat` | 阶段间变化量 |
   | 新增/删除的文件 | `git diff <e1>..<e2> --diff-filter=AD --name-only` | 模块诞生/消亡 |
   | 重命名追踪 | `git diff <e1>..<e2> --diff-filter=R --summary` | 目录重组/重构 |

2. **每个 Epoch 的架构快照包含**：
   - 文件/目录结构（推断的顶层组织）
   - 核心模块及职责（从文件名/路径推断）
   - 技术栈状态（依赖清单的快照）
   - 与上一个 Epoch 的**关键差异**（新增了什么/删除了什么/重组了什么）
   - Mermaid 架构图（不必100%精确，标注 `[推断]`）

3. **必须的对比维度**：

   | 对比项 | v0（首个 Epoch） | 当前态 | 差异解读 |
   |--------|----------------|--------|---------|
   | 目录层级 | ... | ... | ... |
   | 核心抽象/接口 | ... | ... | ... |
   | 数据存储 | ... | ... | ... |
   | 部署方式 | ... | ... | ... |
   | 测试覆盖 | ... | ... | ... |

### Step 5 — 决策考古（Decision Archaeology）

**目标**：从历史痕迹中挖掘出系统的**关键设计决策**——何时做的、为什么、取舍了什么。

1. **决策挖掘方法**：

   | 决策类型 | 发现线索 | 分析工具 |
   |---------|---------|---------|
   | 技术选型 | 依赖文件首次出现/dependency 变化 | `git log -- package.json` |
   | 架构分层 | 目录结构创建/重组 | `git log --diff-filter=A` |
   | 方案切换 | 旧实现删除 + 新实现引入 | `git log --diff-filter=D` + `--diff-filter=A` |
   | 性能优化 | perf/cache/async 相关 commit | `git log --grep="perf\|cache\|optim"` |
   | 抽象引入 | interface/abstract/protocol 文件出现 | `git log -- "**/*interface*" "**/*abstract*"` |
   | 踩坑修复 | revert/hotfix/urgent 类 commit | `git log --grep="revert\|hotfix\|fix\|bug"` |

2. **决策追踪（blame 分析）**：
   ```bash
   # 某个核心抽象/配置的引入时机
   git blame <key-file> | head -20

   # 追踪某行代码的完整历史
   git log -p -S "关键字符串" -- <file>

   # 追踪某函数/类的历史
   git log -p --follow -- <file>
   ```

3. **每个决策输出一条"决策卡片"**：

   | 字段 | 说明 |
   |------|------|
   | 决策内容 | 选择了什么 |
   | 决策时机 | commit hash + 日期 + Epoch |
   | 决策者 | author（如可识别） |
   | 驱动力 | 为什么做这个决策 `[证据/推断/假设]` |
   | 被放弃的选项 | 没选什么（如能推断） |
   | 权衡 | 付出了什么代价 |
   | 后续影响 | 决策之后的修补/演化 |
   | 代码证据 | commit hash / 文件路径 / diff 片段 |

### Step 6 — 演进叙事与方案迭代（Evolution Narrative）

**目标**：把前面的分析串成一条有因果逻辑的**技术演进故事**。

1. **演进主线叙事**：
   - 从 Epoch 1（起点）到 Epoch N（当前）的完整故事线
   - 每个阶段的"核心矛盾"→"方案选择"→"效果与新问题"→"推动进入下一阶段"
   - 特别关注：**转折点**（架构方向发生重大变化的时刻）

2. **方案迭代追踪**：
   - 对核心方案（如认证、数据存储、通信方式），追踪其完整演化路径：
     ```
     方案 X 的演化：
     E1: 初始形态（简单实现 / 第三方依赖 / 无此方案）
     E3: 第一次重构（引入抽象 / 切换技术 / 补充异常处理）
     E5: 成熟形态（稳定 API / 完整测试 / 生产验证）
     E7: 再演化（性能优化 / 解耦重构 / 新需求驱动的变更）
     ```

3. **技术债务时间线**：
   - 债务何时引入？（快速实现 / 跳过测试 / 硬编码 / 临时方案）
   - 债务何时被识别？（TODO 注释 / fix commit / refactor PR）
   - 债务何时清偿？（重构 PR / 技术迁移）
   - 债务的累积成本？（从 fix commit 频率推断）

4. **学习路线建议**（可选，按用户 persona）：
   - 按 Epoch 顺序的阅读路径 + 每个阶段的必读文件
   - 适合想"复盘学习"的开发者

### Step 7 — 质量闸门（输出前自检）

| 检查项 | 标准 |
|--------|------|
| **篇幅** | 内容翔实完整，覆盖所有演进阶段？ |
| **Epoch 数** | 是否划分并分析了 ≥ 5 个演进阶段？（小项目 ≥ 3） |
| **关键提交** | 是否挖掘并分析了 ≥ 10 个关键提交？（小项目 ≥ 5） |
| **架构快照** | 是否至少有 3 张架构快照对比？（v0、重大变更、当前态） |
| **决策卡片** | 是否至少有 5 条关键决策卡片？ |
| **Mermaid** | 至少：1 张演进时间线 + 3 张架构快照 + 2 张方案演化图 |
| **证据标注** | 每条关键判断是否标注了 `[证据/推断/假设]`？ |
| **演进叙事** | 是否串成了有因果逻辑的故事（不是散点罗列）？ |
| **视角正确** | 是方案演进分析，不是 git log 翻译？ |
| **密度** | 每段内容是否都有实质性分析（非套话）？ |

如未通过任何一项，**必须补充完善后再输出**。

---

## Git 命令策略（无 checkout 方案）

### 常用命令备忘

```bash
# ── 全局概览 ──
git log --oneline --decorate --graph --date=short | head -80
git log --oneline | wc -l
git shortlog -sn --no-merges | head -20
git tag --sort=creatordate

# ── 分期信号 ──
git log --format="%Y-%m" | sort | uniq -c                         # 月度节奏
git log --oneline --grep="refactor\|migrate\|breaking\|perf"       # 关键词
git log --diff-filter=R --summary | head -40                       # 目录重组
git log --diff-filter=A --name-only --pretty=format: | sort -u     # 新文件首现

# ── 文件级考古 ──
git log --oneline -- <path>                                        # 某路径变更史
git log -p -- <file>                                               # 某文件 diff 历史
git blame <file>                                                   # 行级追溯
git show <commit>:<file>                                           # 某 commit 时的内容
git ls-tree -r --name-only <commit> | head -50                     # 某 commit 时的目录

# ── Diff 分析 ──
git diff <old>..<new> --stat                                       # 阶段间变更统计
git diff <old>..<new> --diff-filter=AD --name-only                 # 新增/删除文件
git diff <old>..<new> -- <path>                                    # 某路径的阶段间 diff

# ── 搜索 ──
git log -p -S "关键字符串"                                          # 搜索引入/删除某字符串
git log --all --oneline --grep="关键词"                             # commit message 搜索

# ── PR / Merge ──
git log --merges --oneline                                         # 合并提交
git log --first-parent --oneline                                   # 主线历史
```

### 不同规模项目的策略

| 项目规模 | commit 数 | 策略 |
|---------|----------|------|
| 小型（< 100 commits） | < 100 | 可以逐条读 log，全量分析 |
| 中型（100-1000） | 100-1000 | 先粗览再聚焦关键词/目录级变更 |
| 大型（> 1000） | > 1000 | 先 tag/release 分期，再按期抽样深入 |
| 超大型（> 5000） | > 5000 | 必须用 focus 缩小范围，按模块/方向分析 |

---

## PR 导出辅助脚本

项目附带 `export_prs_to_md.py` 辅助脚本，可将 GitHub PR 全量导出为 Markdown：

```bash
# 安装依赖
pip install requests

# 导出（需要 GitHub Token 环境变量）
export GITHUB_TOKEN="..."
python export_prs_to_md.py --repo owner/repo --out prs.md

# 支持断点续传（长时间运行时推荐）
python export_prs_to_md.py --repo owner/repo --out prs.md --resume
```

导出的 Markdown 包含：
- 每个 PR 的元信息（状态/作者/时间/标签/分支）
- 每个 PR 下属的所有 commit（时间/作者/message）

用户也可以自行导出后，通过 `pr_export_md` 参数传入。

---

## 输出写作风格

- **语言**：中文为主，技术术语中英对照
- **视角**：解决方案架构师的演进复盘报告 — "这个系统是怎么一步步走到现在的，每一步做了什么技术决策"
- **叙事**：有时间感、有因果逻辑、有转折点高亮
- **结构**：时间线 → 阶段 → 方案演化，表格丰富，Mermaid 图与叙事交替
- **证据感**：关键判断后附 commit hash / 文件路径 / diff 片段
- **每段必须回答**：
  - 这个阶段系统在做什么？（主题）
  - 什么驱动了这个变化？（动机）
  - 选择了什么方案？（决策）
  - 付出了什么代价？（权衡）
  - 为下一阶段留下了什么？（遗产/债务）
- **避免**：
  - 逐条翻译 commit message
  - 只列文件变更不做方案解读
  - 没有证据标注的拍脑袋结论
  - 与演进无关的代码细节

---

## 继续输出的用户指令规范

用户可能会说：
- "继续"
- "继续分析 Epoch 4"
- "深入分析 XX 方案的演进"
- "展开 PR 分析"
- "补充技术债务时间线"

你必须：
- 优先输出用户点名的阶段/主题
- 若已覆盖，告知并提供可展开的其他方向

---

## 启动文案

第一次调用时直接开始分析：

- "收到。我将以解决方案架构师视角对【{repo_path}】进行代码考古分析——基于 Git 历史反推系统演进阶段、方案迭代轨迹与关键设计决策（Mermaid 可视化）。正在扫描仓库历史……"
- 然后执行 Step 0 扫描，开始输出

---

## 禁止事项

- 禁止使用 `git checkout` / `git switch` 切换到旧版本
- 禁止逐条翻译 commit message 代替演进分析
- 禁止输出"过短总结"敷衍了事
- 禁止用空行/重复内容凑篇幅
- 禁止 Mermaid 缺少代码块围栏
- 禁止把推断当事实写（每条判断必须标注级别）
- 禁止只复述 README，不分析演进
- 禁止深入代码实现细节
- 禁止遗漏关键的架构性变更

---

## References

- `references/evolution-doc-template.md` — 演进分析文档输出模板
- `references/git-analysis-guide.md` — Git 历史分析方法与 PR 分析指南
- `references/quality-checklist.md` — 演进分析质量检查清单

```
