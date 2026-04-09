# Git 历史分析方法与 PR 分析指南

> 本文档是 code-archaeology 技能的参考手册。
> 核心原则：**不 checkout 旧版本**，所有历史信息通过 `git log/show/diff/blame/ls-tree` 获取。

---

## 一、无 checkout 考古命令大全

### 1.1 全局概览命令

```bash
# ── 基础统计 ──
git log --oneline | wc -l                          # 总 commit 数
git log --reverse --oneline | head -1               # 首次提交
git log --oneline -1                                # 最近提交
git shortlog -sn --no-merges | head -20             # 贡献者排名
git tag --sort=creatordate                          # 版本标签（时间排序）

# ── 时间跨度 ──
git log --reverse --format="%ai" | head -1          # 最早日期
git log --format="%ai" | head -1                    # 最近日期

# ── 月度节奏 ──
git log --format="%Y-%m" | sort | uniq -c           # 月度 commit 分布

# ── 热点文件（被修改最多的文件）──
git log --name-only --pretty=format: | sort | uniq -c | sort -rn | head -30

# ── 文件类型分布 ──
git ls-files | sed 's/.*\.//' | sort | uniq -c | sort -rn | head -20
```

### 1.2 分期信号发现命令

```bash
# ── 关键词搜索（演进信号）──
git log --oneline --grep="refactor"                 # 重构
git log --oneline --grep="migrate"                  # 迁移
git log --oneline --grep="breaking"                 # 破坏性变更
git log --oneline --grep="perf\|performance"        # 性能优化
git log --oneline --grep="ci\|cd\|pipeline"         # CI/CD
git log --oneline --grep="test\|spec"               # 测试
git log --oneline --grep="docker\|container"        # 容器化
git log --oneline --grep="k8s\|kubernetes"          # 编排
git log --oneline --grep="security\|auth\|jwt"      # 安全
git log --oneline --grep="cache\|redis"             # 缓存
git log --oneline --grep="queue\|async\|worker"     # 异步

# ── 组合搜索 ──
git log --oneline --all --grep="refactor\|migrate\|breaking\|perf\|redesign\|rewrite"

# ── 目录重组信号 ──
git log --diff-filter=R --summary | head -60        # 重命名（目录搬家）
git log --diff-filter=D --name-only --pretty=format: | sort -u | head -30  # 删除的文件

# ── 新文件首现（新模块诞生）──
git log --diff-filter=A --name-only --pretty=format: | sort -u | head -50

# ── 依赖文件变更历史 ──
git log --oneline -- "package.json" "go.mod" "Cargo.toml" "pyproject.toml" "pom.xml" "build.gradle"

# ── 大体量 commit ──
git log --shortstat --format="%h %s" 2>/dev/null | grep -E "[0-9]{2,} file" | head -20
```

### 1.3 文件级考古命令

```bash
# ── 某个文件/目录的完整变更历史 ──
git log --oneline -- <path>
git log --oneline --follow -- <file>                # 跟踪重命名

# ── 某文件的 diff 历史（看每次改了什么）──
git log -p -- <file>
git log -p --follow -- <file>                       # 跟踪重命名

# ── blame：行级追溯 ──
git blame <file>
git blame <file> -L 10,30                           # 指定行范围
git blame <file> --since="2024-01-01"               # 指定时间后

# ── 某 commit 时的文件内容（替代 checkout）──
git show <commit>:<path/to/file>

# ── 某 commit 时的目录结构（替代 checkout）──
git ls-tree -r --name-only <commit> | head -50
git ls-tree --name-only <commit> <directory>/       # 某目录下

# ── 搜索某字符串的引入/删除历史 ──
git log -p -S "关键字符串"                            # pickaxe 搜索
git log -p -S "ClassName"                           # 查找类首次出现
git log -p -G "正则表达式"                            # 正则搜索
```

### 1.4 Diff 分析命令

```bash
# ── 两个 commit 之间的变更统计 ──
git diff <old>..<new> --stat

# ── 两个 commit 之间新增/删除的文件 ──
git diff <old>..<new> --diff-filter=A --name-only   # 新增
git diff <old>..<new> --diff-filter=D --name-only   # 删除
git diff <old>..<new> --diff-filter=M --name-only   # 修改
git diff <old>..<new> --diff-filter=R --summary     # 重命名

# ── 某个路径在两个 commit 之间的变化 ──
git diff <old>..<new> -- <path>
git diff <old>..<new> --stat -- <path>

# ── 某个 commit 的完整 diff ──
git show <commit> --stat
git show <commit> -- <file>                         # 某文件的改动
git diff <commit>^..<commit>                        # 等效写法
```

### 1.5 PR / Merge 分析命令

```bash
# ── 合并提交 ──
git log --merges --oneline                          # 所有 merge commit
git log --merges --oneline --since="2024-01-01"     # 时间过滤

# ── 主线历史（跳过合并的侧分支）──
git log --first-parent --oneline

# ── 某个 merge commit 包含的所有子 commit ──
git log <merge_commit>^..<merge_commit> --oneline
```

---

## 二、演进分期方法论

### 2.1 分期策略

分期的目标是把**连续的 commit 流**切分为有意义的**方案主题期**。

**推荐分期流程**：

```
Step 1: 用 tag/release 做粗切（版本边界）
    ↓
Step 2: 用关键词 commit 识别主题性变更
    ↓
Step 3: 用目录变更/依赖变更识别架构断层
    ↓
Step 4: 用 月度 commit 分布识别活跃度断层
    ↓
Step 5: 语义合并（相邻的同主题阶段合并）
    ↓
Step 6: 命名（每个 Epoch 取一个主题名）
```

### 2.2 常见 Epoch 主题模板

| 主题模式 | 典型名称 | 信号 |
|---------|---------|------|
| **最小可行** | "MVP / 核心闭环 / v0.1" | 首批 commit，入口文件+核心逻辑 |
| **功能扩展** | "功能补全 / 能力拓展" | 大量新文件，新路由/API |
| **架构重构** | "架构化 / 分层重构 / 模块化" | 目录重组，interface/abstract 出现 |
| **技术迁移** | "XX迁移 / 框架切换" | 依赖大变，旧文件删除+新文件引入 |
| **性能优化** | "性能调优 / 缓存化" | cache/async/batch 相关 commit |
| **工程化** | "工程化建设 / CI/CD" | CI 配置、Dockerfile、测试框架 |
| **稳定化** | "稳定化 / bug 收敛" | fix 类 commit 密集 |
| **平台化** | "平台化 / 多租户 / 插件化" | plugin/tenant/provider 相关 |

### 2.3 分期数量建议

| 项目时长 | commit 数 | 建议 Epoch 数 |
|---------|----------|-------------|
| < 3 个月 | < 100 | 3-5 |
| 3-12 个月 | 100-500 | 5-8 |
| 1-3 年 | 500-2000 | 7-10 |
| > 3 年 | > 2000 | 8-12 |

**原则**：每个 Epoch 应该有一个可以用一句话概括的主题。如果无法概括，考虑再切分。

---

## 三、PR 分析方法

### 3.1 PR 数据来源

| 方式 | 适用条件 | 信息丰富度 |
|------|---------|-----------|
| `export_prs_to_md.py` 脚本 | GitHub 仓库 + Token | ★★★★★（PR 元信息+commit） |
| GitHub API 在线查询 | GitHub 仓库 + Token | ★★★★★ |
| `git log --merges` | 任何 Git 仓库 | ★★☆☆☆（只有 merge commit） |
| 用户手动提供 | 用户整理 | 取决于内容 |

### 3.2 PR 分类框架

| PR 类别 | 识别关键词 | 方案分析价值 |
|---------|----------|------------|
| **Feature** | feat/add/implement/support/introduce | 🔴 高 — 新方案引入 |
| **Refactor** | refactor/restructure/reorganize/rewrite | 🔴 高 — 方案迭代 |
| **Fix** | fix/bug/hotfix/patch/resolve | 🟡 中 — 方案缺陷暴露 |
| **Infra** | ci/cd/docker/deploy/build/lint/format | 🟡 中 — 工程化演进 |
| **Dependency** | bump/update/upgrade/migrate/dep | 🟡 中 — 技术栈演进 |
| **Breaking** | breaking/major/remove/deprecate | 🔴 高 — 方案断代 |
| **Docs** | doc/readme/changelog/comment | 🟢 低 — 通常无方案变化 |
| **Chore** | chore/cleanup/tidy/misc | 🟢 低 — 通常无方案变化 |

### 3.3 高价值 PR 识别规则

满足以下任一条件的 PR 应被标为"高价值"：

1. **变更文件 > 10 个**（大范围改动）
2. **变更类型 = Feature/Refactor/Breaking**
3. **标题含"架构信号"关键词**（refactor/migrate/redesign/replace/remove）
4. **涉及核心目录**（src/core/、src/domain/、配置文件）
5. **引入新依赖**（package.json/go.mod 变更 + 新代码）
6. **存在后续 fix/revert PR**（说明有复杂度/风险）

### 3.4 PR 分析输出格式

对每个高价值 PR，输出：

```
PR #{number}: {title}
- 类别：Feature/Refactor/Fix/Infra/Breaking
- Epoch：E?
- 方案意义：这个 PR 在方案层面做了什么（一句话）
- 实施策略：一步到位 / 渐进式分步（如何看出）
- 变更范围：涉及的模块/文件（概要）
- 决策推断：为什么做这个变更 [证据/推断]
- 后续影响：有无后续 fix/补充 PR
```

---

## 四、决策挖掘方法

### 4.1 六种决策类型与发现路径

| 决策类型 | 发现方法 | 典型线索 |
|---------|---------|---------|
| **技术选型** | 依赖文件首现 / 框架配置首现 | package.json 首次出现某依赖 |
| **架构分层** | 目录结构创建 / 重组 | `src/domain/`、`src/infra/` 目录首次出现 |
| **方案引入** | 新文件/新模块引入 | 中间件/拦截器/策略类首次出现 |
| **方案切换** | 旧实现删除 + 新实现引入 | 同一 commit 中删除旧文件、新增替代文件 |
| **性能决策** | perf/cache/async 相关 commit | 缓存层引入、异步处理引入 |
| **踩坑修复** | revert/hotfix/urgent | 回滚 commit、紧急修补 PR |

### 4.2 决策时机推断

```bash
# 某个核心文件何时被创建
git log --diff-filter=A -- <file>

# 某个依赖何时被引入
git log -p -- package.json | grep -A5 '"新依赖"'

# 某个抽象/接口何时出现
git log -p -S "interface UserRepository" -- <file>

# 某行代码何时写入
git blame <file> -L <start>,<end>
```

### 4.3 决策链追踪

很多决策不是"一次性"的，而是"引入→调整→稳定"的链条：

```
git log --oneline -- <涉及的文件/目录>
```

按时间排列，可以看到决策从引入到稳定的完整过程。

---

## 五、架构快照还原方法（无 checkout）

### 5.1 还原某个 commit 的目录结构

```bash
# 完整目录
git ls-tree -r --name-only <commit>

# 顶层目录
git ls-tree --name-only <commit>

# 某个子目录
git ls-tree --name-only <commit> src/
```

### 5.2 还原某个 commit 的文件内容

```bash
# 读取某文件
git show <commit>:<path/to/file>

# 读取配置文件（了解当时的技术栈）
git show <commit>:package.json
git show <commit>:go.mod
git show <commit>:docker-compose.yml
```

### 5.3 对比两个阶段的架构差异

```bash
# 文件级变更统计
git diff <epoch1_end>..<epoch2_end> --stat

# 新增了哪些文件/目录（新模块诞生）
git diff <e1>..<e2> --diff-filter=A --name-only

# 删除了哪些文件/目录（旧方案退场）
git diff <e1>..<e2> --diff-filter=D --name-only

# 重命名（目录重组）
git diff <e1>..<e2> --diff-filter=R --summary
```

---

## 六、常见误区

| 误区 | 正确做法 |
|------|---------|
| 逐条 commit 翻译成中文 | 聚焦有"方案意义"的 commit |
| 把所有 commit 都列为"关键" | 严格按挑选标准筛选 |
| 只看 commit message 不看 diff | message 可能不准确，diff 是一级证据 |
| 推断动机不标注 | 必须标注 `[证据/推断/假设]` |
| 按月份机械切分 Epoch | 按方案主题的自然断层切分 |
| 只关注代码变更不看 CI/配置 | 工程化变更同样是重要演进信号 |
| 忽略 revert/hotfix | 踩坑痕迹是决策质量的重要证据 |
