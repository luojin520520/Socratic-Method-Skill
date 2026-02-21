---
name: ripple-effect-guard
description: Force an impact-aware workflow for any code or configuration change. Use whenever Codex is about to modify code, configs, schemas, APIs, or refactor existing logic. Always require listing dependent modules, call sites, tests, interfaces, and external integrations that could be affected, propose solutions for each item, and ask the user to confirm whether to change them together.
---

# Ripple Effect Guard

## Overview

Require a pre-change impact listing before editing code. Enumerate likely dependencies, propose concrete fixes per item, and let the user confirm scope before any edits.

## Workflow

1. Detect intent to change code or configuration.
2. Pause before edits.
3. Produce a ripple table that includes suggested fixes (not just risks).
4. Provide 2–3 options with trade-offs and a recommendation.
5. Ask the user to confirm which option and which ripple items to include.
6. Proceed only after confirmation.
7. During edits: keep the table as a checklist; if new ripple items are discovered, stop and re-confirm scope.

## Dependency Discovery Rules

Be concrete and bounded; do not over-list. Prefer "verify" over "update" when uncertain.

Always consider and list if applicable:
- Callers and call sites, including public entry points and CLI/HTTP handlers.
- Interfaces and API contracts, including request/response shapes and public exports.
- Data schemas, migrations, and storage compatibility.
- Configuration files, feature flags, and environment variables.
- Tests, fixtures, golden files, and snapshots.
- Documentation, READMEs, and example snippets.
- Build, lint, CI rules, and code generation steps.
- Observability integrations: logs, metrics, tracing, alerts.
- Dependency graph ripple: shared utilities, base classes, cross-cutting middleware.
- External integrations and versioned client libraries.

Heuristics to find dependencies quickly:
- Search symbol usage and exports (function/type names, constants).
- Search config keys and env var names.
- Check public modules and package exports.
- Check tests that reference the behavior (unit/integration/e2e).
- Check docs/examples that mention the old behavior.

## Output Format

Mirror the user's language for all headings and table labels. Do not mix languages.

### Chinese Template (Primary)

**变更提议**
- 摘要: <一句话>
- 目标文件: <已知文件列表; 若不确定写 TBD>

**涟漪影响与建议方案（表格）**

| 影响项 | 原因 | 涉及文件/模块 | 建议方案（解决方案） | 动作 |
| --- | --- | --- | --- | --- |
| <会受影响的点> | <一句原因> | <可能的路径/模块> | <具体怎么改/怎么验证/怎么补齐> | <修改 / 验证 / 补测试 / 更文档> |

**选项与权衡**
- 方案 A: <范围/成本/风险>（推荐或不推荐）
- 方案 B: <范围/成本/风险>
- 方案 C: <可选>

**请确认范围**
1. 选择方案并全部纳入表格项。
2. 选择方案但只纳入部分（请列出要纳入的行/影响项）。
3. 暂不处理（我会明确风险与后续跟进清单）。

**改动中提醒（加入思维链的检查点）**
- 每次准备改一个文件前: 对照表格，标明这次改动对应哪几行。
- 若发现新增依赖/影响项: 先追加到表格并再次请求确认范围，再继续。
- 完成后: 按表格逐条勾掉，并给出验证步骤（哪些测试/哪些手工验证）。

### English Template

**Proposed Change**
- Summary: <one sentence>
- Target files: <known list; use TBD if uncertain>

**Ripple Effects & Suggested Fixes (Table)**

| Item | Why | Files/Modules | Suggested Fix | Action |
| --- | --- | --- | --- | --- |
| <what might change> | <one short reason> | <likely paths/modules> | <how to update/verify/tests/docs> | <update / verify / add test / doc update> |

**Options & Trade-offs**
- Option A: <scope/cost/risk> (recommend or not)
- Option B: <scope/cost/risk>
- Option C: <optional>

**Need Your Confirmation**
1. Choose an option and include all items.
2. Choose an option but include only selected items.
3. Skip for now (state risks and follow-up list).

**In-Edit Reminders (Checklist Hooks)**
- Before each file edit: map the edit to one or more table rows.
- If new ripple items are discovered: append to the table and re-confirm scope.
- After changes: check off each row and provide a verification plan.

## Behavior Notes

Do not perform any edits until the user confirms the scope.
