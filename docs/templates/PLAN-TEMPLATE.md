# Plan Template

> 写 Phase Implementation Plan 时复制此模板。Plan 面向 agentic worker 执行。

---

## 板块清单

| # | 板块 | 必须 | 说明 |
|---|------|------|------|
| 1 | Header | ✅ | Goal、Architecture、Tech Stack、Branch |
| 2 | File Map | ✅ | 每个文件的 Action + Responsibility |
| 3 | Tasks | ✅ | 编号，逐步 checklist，code + verify + commit |

---

## 1. Header

```markdown
# Phase N — Title Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 一句话 - 用户能做什么

**Architecture:** 2-3 句 - 路由、组件、数据流

**Tech Stack:** React 19, React Router 7, Supabase, Tailwind CSS 4, Motion, KaiTi

**Branch:** `phaseN`
```

## 2. File Map

| File | Action | Responsibility |
|------|--------|----------------|
| `src/services/XService.ts` | Create | 数据层，Supabase queries |
| `src/pages/XPage.tsx` | Create | 页面容器 |
| `src/components/X/YPanel.tsx` | Create | 功能面板 |
| `src/data/translations.ts` | Modify | 新增翻译 key |
| `src/App.tsx` | Modify | 路由接入 |

## 3. Tasks

### 每个 Task 的固定格式：

```markdown
### Task N: 标题（做什么）

**Files:**
- Create: `path/file.ts` — 说明
- Modify: `path/file.ts` — 说明

- [ ] **Step 1: 步骤描述**

具体操作说明

\`\`\`ts
// 代码块
\`\`\`

- [ ] **Step 2: Verify**

Run: `npx tsc --noEmit`
Expected: 0 errors

- [ ] **Step 3: Commit**

\`\`\`bash
git add ...
git commit -m "feat(phaseN): description"
\`\`\`
```

### Task 编排原则：

- **Task 1 → 翻译 key**（有依赖最小，可先做）
- **Task 2 → 数据层 service**（组件依赖它）
- **Task 3-N → 组件**（从叶子到根，被依赖的先做）
- **最后一个 Task → 路由接入 + 全量 verify**
- **最后一个 Task 后 → smoke test（manual/Playwright）**
