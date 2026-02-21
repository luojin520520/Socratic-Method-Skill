---
name: socratic-method
description: Force a Socratic-style impact analysis before touching code. Use whenever Codex is about to alter shared code, configs, schemas, APIs, or data flows; always map architecture first, surface solutions for every ripple, offer choices, and wait for explicit approval.
---

# Socratic-Method Skill

## Overview

Before executing any change, this skill turns into a Socratic guide: it maps the project architecture, interrogates dependencies, proposes solutions for each ripple effect, lays out options with trade-offs, and halts until the user approves.

## Workflow

1. **Architecture Scan** – build a quick mental model of the project. Identify core directories, entry points, data flows, and module boundaries using workspace symbols, `README/ARCHITECTURE.md`, and `rg`.
2. **Define Change Context** – locate the precise file/function/interface you intend to change and anchor it to the architecture map (layers, services, data contracts).
3. **Dependency Discovery** – list callers, contract consumers, configs, docs, tests, external integrations, and anything that touches the targeted element.
4. **Produce Ripple Report** – for each dependency, propose a concrete fix/verification, note affected files, assign an action (change/verify/test/doc), and capture severity.
5. **Offer Options** – provide 2–3 implementation paths, highlight trade-offs, drop a recommendation, and ask the user to pick one.
6. **Await Explicit Approval** – do not edit anything until the user signs off.
7. **Edit with Checkpoints** – as you modify files, map each edit back to the ripple table. If new impacts appear, pause, expand the table, and reconfirm before continuing.
8. **Verify and Close** – after edits, mark each table row complete and describe verification (tests, lint, manual checks).

## Architecture Scan Template

Before diving into dependencies, answer these three framing steps:
1. **Describe the entry point** – Which top-level file/service/handler is responsible for the change? e.g., `src/api/user.ts::POST /users`.
2. **Map the affected layers** – Sketch the layers it crosses (controllers → services → repositories → clients/frontends).
3. **Sketch the data flow** – Outline how data enters, transforms, and exits, noting serializers, caches, and downstream consumers.

Capture this sketch in text or a mini-table so the later ripple table can reference specific layers.

## Dependency Discovery Rules

Be concrete and bounded; prefer "verify" over "update" when uncertain. Always consider:
- Callers and call sites, including services, handlers, and CLI/HTTP entry points.
- Interfaces, API contracts, request/response shapes, and public exports.
- Data schemas, migrations, databases, caches, and serialization layers.
- Configuration files, environment variables, feature flags, and CI/CD settings.
- Tests, fixtures, golden files, and snapshots.
- Documentation, READMEs, architecture diagrams, and examples.
- Build scripts, lint rules, code generation, and shared utilities.
- Observability: logs, metrics, tracing, alerts.
- External integrations and versioned clients.

Heuristics:
- Use `lsp_find_references`, `rg`, or workspace symbols to trace exports.
- Walk config keys and env var uses.
- Search for event/pipeline producers and consumers.
- Cross-check README/ARCHITECTURE.md for system boundaries.

## Output Format

Mirror the user's language for headings and table labels.

### Primary Template / 主要模板
**Change Request / 变更请求**
- Summary / 摘要: <one sentence>
- Target files / 目标文件: <list known files or TBD>

**Ripple Effects & Suggested Fixes / 涟漪影响与建议方案**
| Item / 影响项 | Why / 原因 | Files / 文件 | Suggested Fix / 建议方案 | Action / 动作 |
| --- | --- | --- | --- | --- |
| <what might break> | <why it breaks> | <modules> | <how to update/verify/tests/docs> | <update / verify / add test / doc update> |

**Options & Trade-offs / 方案与权衡**
- Option A: <scope/cost/risk> (recommend or not)
- Option B: <scope/cost/risk>
- Option C: <optional>

**Need Your Confirmation / 需要确认**
1. Include an option and all table rows.
2. Include an option but only selected rows (list them).
3. Hold off (document risks, follow-up later).

**In-Edit Reminders / 编辑提醒**
- Before modifying a file: reference the table row(s) it affects.
- If new ripple items emerge: pause, add them, and re-confirm scope.
- After edits: mark rows complete and summarize verification steps.

## Author & Purpose
**Author**: Anonymvs1234
This skill exists to interrogate changes like a tutor: it forces the agent to ask “why”, “what breaks”, and “how do I fix it” before writing a single line.

## Behavior Notes
Do not edit anything until the user confirms scope. Always reflect the architecture scan, dependency findings, proposed fixes, option trade-offs, and verification plan in your response.
