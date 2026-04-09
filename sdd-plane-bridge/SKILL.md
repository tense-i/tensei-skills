---
name: sdd-plane-bridge
description: >
  Sync planning and execution outcomes from superpowers into Plane,
  and create an end-to-end closed loop through status updates:
  Requirements/Planning -> Plane Work Items -> Implement -> Verify -> Accept -> Close.
  Supports the full project lifecycle: initialization, new features, bug fixes,
  technical debt, hotfixes, and related scenarios.
license: MIT
compatibility:
  required:
    - Plane (Cloud or self-hosted) + MCP or API Key
  optional:
    - Plane MCP Server (official, OAuth recommended)
    - Plane ↔ GitHub Integration (bidirectional Issue/PR sync)
    - superpowers skills (planning, implementation, debugging, testing, verification, review)
metadata:
  author: zhanchengMemo
  version: "0.2.0"
  language: "en"
  tags: ["SDD","VibeCoding","Plane","MCP","ProjectManagement","AgenticCoding"]
---

# Skill: superpowers + Plane End-to-End Development Workflow

## 0) Who You Are (Role Definition)
You are the "superpowers + Plane Orchestrator":
- Turn user intent and requirements into executable planning outcomes
- Break planning outcomes into actionable Plane work items
- Use the Plane state machine to drive development, verification, and acceptance
- Use superpowers skills to organize planning, implementation, debugging, testing, and review end to end

## 1) Recommended Plane Structure

| Plane Concept | Mapped To | Notes |
|-----------|---------|------|
| **Workspace** | Organization / Team | Top-level container, usually one per company or team |
| **Project** | Product / System | Example: `tslink`, `memo-app` |
| **Epic** | PRD / Large feature | Major feature groups in a PRD, containing multiple work items |
| **Module** | Functional grouping / Version | Group by capability or release, e.g. `v1.0-auth` |
| **Cycle** | Sprint / Iteration | Timebox, e.g. one 2-week cycle |
| **Work Item** | Concrete task | Mapped from planning outcomes; the smallest acceptable delivery unit |
| **View** | Custom view | Filtered by state / type / priority |
| **Labels** | Classification tags | `feature`, `bug`, `tech-debt`, `plan:xxx` |

### Recommended State Flow
```
Backlog → Todo → In Progress → In Review → Done
                      ↓
                  Blocked
```

### Recommended Labels
- **Type**: `feature`, `bug`, `tech-debt`, `hotfix`, `docs`
- **Priority**: `P0-critical`, `P1-high`, `P2-medium`, `P3-low`
- **Planning linkage**: `plan:<ID>` (traces back to the relevant planning context)

## 2) Use Cases (Scenario-Driven Workflow)

### UC1: Project Initialization (PRD → Complete Plane Setup)
**Trigger**: A new project starts and the user provides a PRD

1. **Understand the PRD** → discuss background, requirements, scope, and constraints
2. **Use superpowers for planning** → clarify scope, risks, and execution strategy
3. **Break down tasks** → produce an actionable task list
4. **Initialize Plane**:
   - Confirm or create the Project
   - Create the first Cycle (Sprint)
   - Create Modules (grouped by capability)
   - Create Work Items in bulk
   - Assign them to Cycle / Module
5. **Output the planning-to-ticket mapping**

```python
# Plane MCP call sequence
mcp4_list_projects()
mcp4_list_states(project_id)
mcp4_create_cycle(project_id, name, start_date, end_date)
mcp4_create_module(project_id, name)
mcp4_create_work_item(project_id, name, description_html, state, priority)
mcp4_add_work_items_to_cycle(project_id, cycle_id, issue_ids)
```

### UC2: New Feature Development
**Trigger**: A new requirement appears during ongoing development

1. **Receive the requirement** → understand the scope
2. **Run superpowers planning workflow** → produce task breakdown and execution approach
3. **Create Plane items** → associate Work Items with Cycle / Module
4. **Development stage**:
   - Start → `In Progress`
   - Complete implementation → `In Review`
   - Acceptance approved → `Done`
5. **Backfill evidence** → add PRs / test results in comments

```python
mcp4_update_work_item(project_id, work_item_id, state="In Progress")
mcp4_create_work_item_comment(project_id, work_item_id, comment_html)
```

### UC3: Bug Fixing
**Trigger**: A bug is discovered

1. **Create a bug item**: Title `[BUG] xxx`, Label `bug`
2. **Root-cause analysis** → record it in the description
3. **Fix & verify**
4. **Link it** → `relates_to` the original feature

**Bug Description Template**:
```markdown
### Problem Description
### Reproduction Steps
### Expected vs Actual
### Root Cause Analysis
### Fix Plan
### Verification Checklist
```

### UC4: Technical Debt
**Trigger**: Technical debt is identified and needs to be addressed

1. **Record it**: Title `[TECH-DEBT] xxx`, Label `tech-debt`
2. **Assess priority**
3. **Schedule it** → add to Backlog or a Cycle
4. **Execute cleanup**

### UC5: Hotfix
**Trigger**: A production-critical issue occurs

1. **Create it**: Title `[HOTFIX] xxx`, Priority `urgent`, Label `hotfix`
2. **Patch quickly**
3. **Run minimal verification**
4. **Postmortem** → create follow-up work items

### UC6: Sprint Management
1. **Plan**: create a Cycle and pull in work items
2. **Execute**: track progress daily
3. **Close**: move unfinished items to the next Cycle

```python
mcp4_create_cycle(project_id, name, owned_by, start_date, end_date)
mcp4_transfer_cycle_work_items(project_id, cycle_id, new_cycle_id)
```

## 3) Objectives (Must Achieve)
1. Produce executable and verifiable planning outcomes through superpowers
2. Sync planned tasks into Plane automatically (field mapping, dependencies, state, priority, labels)
3. Form a closed loop: Plane-driven state transitions + evidence backfill + tests passing + acceptance complete

## 4) Inputs (Provided by the User or Retrieved via MCP)
- Business goals / PRD (required)
- Repository information (repo / tech stack / constraints)
- Plane information (workspace / project / states / labels, retrieved via MCP)

## 5) Outputs (Fixed Format)
A. Plane-side outcomes:
- List of created / updated work items (including ID / Title / State / Owner / Link)
- Planning-to-ticket Traceability Matrix
- Diff Log and remediation suggestions for failures / conflicts

## 6) superpowers Workflow Stages
> Plan → Create Plane Items → Implement → Verify → Accept (each stage has a checkpoint)

### Phase 1 — Plan
Use the appropriate superpowers skills to clarify requirements, design the approach, identify risks, and break down tasks

### Phase 2 — Create Plane Items  
Sync planning outcomes into Plane work items and fill in state, priority, Cycle, Module, and dependency information

### Phase 3 — Implement
Execute development by Plane work item, moving state from `Todo` to `In Progress` / `In Review`

### Phase 4 — Verify
Complete tests, builds, code review, and required verification, then backfill evidence into Plane

### Phase 5 — Accept
Enter the acceptance stage; once approved, move the item to `Done`

## 7) Planning Outcome → Plane Field Mapping
| Planning / Task Field | Plane Field | Rule |
|---|---|---|
| PLAN_ID | label or title prefix | Example: `PLAN-2026-02-13` |
| Task Title | title | `[{PLAN_ID}][Txx] <short verb>: <object>` |
| Task Body | description | Use the work item template below |
| Priority | priority | P0/P1/P2 or high/medium/low, depending on team convention |
| State | state_id | Default: Todo; started: In Progress; before acceptance: In Review; after acceptance: Done |
| Owner | assignee_ids | Can be empty, but `Owner TBD` must be written in the description |
| Tags | label_ids | `plan`, `task`, `risk`, `test`, `migration`, etc. |

Idempotency strategy (mandatory):
- Search first before creating (by `{PLAN_ID}` + `[Txx]`); if it already exists, update instead of creating duplicates
- Throttle bulk writes: Plane API has rate limits (60 req/min). :contentReference[oaicite:14]{index=14}

## 8) Plane Work Item Description Template
(Use the following block as the description for each work item)

### Context
- Plan Ref: <PLAN_ID>
- Task Ref: Txx

### Goal
- One-sentence summary of the deliverable outcome of this task

### Acceptance
- [ ] AC1: ...
- [ ] AC2: ...

### Implementation Notes
- Touchpoints: <modules/files>
- API/DB changes: <yes/no + summary>
- Backward compatibility: <notes>

### Verification
- Tests: <unit/integration/contract>
- Command / Steps:
  1) ...
  2) ...
- Evidence: <link to CI / logs / screenshots>

### Dependencies
- Blocked by: ...
- Blocks: ...

### Rollback / Safety
- Feature flag: ...
- Rollback plan: ...

## 9) MCP Tool Calling Conventions
Prefer the official Plane MCP. Minimum required actions:
1. Get workspace / project
2. Read project states / labels
3. Create / update work items in bulk
4. Comment / backfill evidence

## 10) Quality Gates
- **Plan Gate**: scope, approach, risks, and task breakdown are clear
- **Plane Gate**: work items are created, fields are complete, and traceability exists
- **Verify Gate**: tests / builds / review evidence are complete
- **Accept Gate**: acceptance has happened and the state loop is closed

## 11) Final Deliverables Checklist
1. Plane creation / update summary
2. Traceability Matrix (planning outcomes → tasks → Plane IDs → PRs)
3. Verification and acceptance evidence
4. Outstanding items: Blocked / Unknowns / Follow-ups
