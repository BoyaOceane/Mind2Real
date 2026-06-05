# 执行记录 & 修复记录编写指南

> **首要读者是 AI Agent，其次才是人类。**

---

## Execution Records — `YYYY-MM-DD-phase-N-slug.md`

每个 Phase 一个文件，放在 `docs/records/` 下。10 项固定结构：

### 1. Header block

```markdown
# 项目名 — Phase N 执行记录: 标题

> **Phase:** N — 阶段名
> **Plan:** `path/to/plan.md`
> **Design ref:** `path/to/design.md`
> **Predecessor:** `path/to/prev-record.md`
> **Date:** YYYY-MM-DD
> **Status:** ✅ Gate validated | Branch: `phase-N` (N commits)
```

### 2. Goal

一段话说明本阶段目标。

### 3. What was implemented

| # | Surface | Commit |
|---|---------|--------|
| 1 | 功能描述 | `abc1234` |

### 4. Smoke gate

**Programmatic checks：**

| Check | Result |
|-------|--------|
| `npx tsc --noEmit` | ✅ |
| `npm run lint` | ✅ |
| `npm run build` | ✅ |

**Manual smoke：**

| Route / Path | Behavior | Status |
|-------------|----------|--------|

分为：Golden path / Skip path / Failure path / Trilingual sanity

### 5. Verdict

`✅ Gate validated YYYY-MM-DD.` 或 `⏸ Awaiting user validation.`

### 6. Plan deviations

| # | Deviation | Reason |
|---|-----------|--------|

### 7. Open items

| # | Item | Phase to fix | Risk |
|---|------|-------------|------|

### 8. Architecture state

ASCII 目录树：

```
project/
├── src/
│   ├── new-module/   ← NEW
│   └── existing/     ← unchanged
```

### 9. Handoff to next phase

从什么基线开始，下一阶段要做什么。

### 10. Decisions reaffirmed

```
- ✓ 决策1 描述
- ✓ 决策2 描述
```

---

## Bug 修复记录规范

在执行记录中追加，或单独成文件。每条修复包含：

```markdown
### Bug-ID: 标题 (FIXED)

**Root cause:** 根本原因

**Fix applied:**
- 步骤1
- 步骤2

**Files changed:** `path/file1.ts`, `path/file2.ts`

**Verification:** 如何确认修复生效
```

---

## 共享约定

- 每个实现步骤关联 commit hash
- 状态标记统一：✅ ⚠ ⏸ ⏳ ❌
- 日期格式：`YYYY-MM-DD`
- 表格优先于段落文字
- API 调用量/费用在涉及 LLM 时记录
- 文件命名：执行记录 `YYYY-MM-DD-phase-N-slug.md`，其他文档 `UPPERCASE-NAME.md`
