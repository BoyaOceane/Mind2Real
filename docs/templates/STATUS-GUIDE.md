# STATUS.md — 总控台编写指南

> **首要读者是 AI Agent，其次才是人类。**

STATUS.md 是跨 Phase 追踪项目全局状态的单一文件。结构高度一致、每段信息在固定位置、状态标记无歧义。Agent 读取后必须能精确重建项目当前状态。

---

## 固定顺序（10 个区块）

### 区块 1：Header

```markdown
# 项目名 Execution Status

> **Last updated:** YYYY-MM-DD (简述最近变更)
> **Active branches:** `main` · `phase-N` HEAD `commithash` (简述)
> **Source:** index of phase execution records under this folder. Gate verdicts here are authoritative.
```

### 区块 2：Phase ledger

| Phase | Plan | Execution record(s) | Implementation | Smoke gate | Notes |
|-------|------|---------------------|----------------|------------|-------|
| **N — Title** | plan 链接 | record 链接 · gate 锚点链接 | ✅ done (N commits on `branch`) | ✅ **validated YYYY-MM-DD** (简述) | 关键成果列举 |

- 已完成 Phase 用 `✅ done` + commit 数 + 分支名
- 未开始 Phase 用 `⏳` + `_not yet written_` + `–`
- 执行记录列可包含多个链接
- 表末附 Legend：`✅ validated · ⚠ conditionally validated · ⏸ implementation done, gate open · ⏳ planned · ❌ blocked`

### 区块 3：Branching

ASCII 分支图：

```
main          ← phase-N merged YYYY-MM-DD (PR #N, merge commit hash)
├── phase-2  ← milestone (kept on origin)
└── phase-3  ← milestone (kept on origin)
```

约定：one branch per phase, gate-passed = merge to `main`.

### 区块 4：Programmatic-check status

| Check | Result |
|-------|--------|
| `npm run typecheck` | ✅ |
| `npm run lint` | ✅ exit 0 |
| `npm run build` | ✅ |

### 区块 5：Open items rolling forward

按来源 Phase 分 subsection，**最新 Phase 排最前**（倒序）：

```
### New from phase-6 gate validation (YYYY-MM-DD)  ← 最新
| # | Item | Phase to fix | Risk |

### New from phase-5 gate validation (YYYY-MM-DD)  ← 次新
| # | Item | Phase to fix | Risk |

### Carried since phase 3
| # | Item | Phase to fix | Risk |

### Carried since phase 2  ← 最旧
| # | Item | Phase to fix | Risk |
```

- 编号统一（P5-1, P6-1 等），不按 Phase 重置
- 当前 Phase 内临时问题用 `P5-1`, `P5-2` 前缀
- 已关闭项 **保留在表中不删除**，用 `~~删除线~~` + `✅ closed phase-N` 标记
- 每条包含：编号 | 描述 | 修复目标 Phase | Risk
- **Smoke 报告中的 findings 必须同步到本区块**。写完 smoke 报告后，逐条将 issue 搬入对应 Phase 的 subsection

### 区块 6：Current phase implementation summary

仅存在于正在实现中、尚未关门的 Phase：

```markdown
## Phase-N implementation summary (YYYY-MM-DD)

**Branch:** `phase-N`
**Plan:** plan 链接
**Spec:** spec 链接

### Task ledger
| Task | Title | Commit | Notes |

### Programmatic checks at HEAD

### Plan deviations (to verify in gate)

### Outstanding before phase-N gate verdict
- [ ] ...
```

### 区块 7：Gate validation results

已关门的 Phase 各保留一个 `## Phase-N gate validation result` 区块。按时间倒序。

```markdown
## Phase-N gate validation result (YYYY-MM-DD)

### Verdict: ✅ Validated → ready to merge

### Programmatic checks（重新确认）

### Manual smoke results
| Walk | Status | Result | Notes |

### New opens surfaced during this gate session

### Inline fixes landed during the gate session

### Outstanding before gate verdict closes
```

### 区块 8：How to validate

```markdown
## How to validate the open phase-N gate
\`\`\`bash
npm run typecheck && npm run lint && npm run build
# Manual smoke: golden path / skip path / failure path / trilingual
\`\`\`
```

### 区块 9：Conventions

简短列表，≤5-6 条。

### 区块 10：Source-of-truth pointers

```markdown
- **Design:** 设计文档链接
- **Plans:** plans 目录链接
- **Execution records:** 本目录
```

---

## 状态标记

| 标记 | 含义 |
|------|------|
| ✅ | 已验证/已完成 |
| ⚠ | 条件通过 |
| ⏸ | 实现完成，门禁未关 |
| ⏳ | 计划中 |
| ❌ | 阻塞 |
