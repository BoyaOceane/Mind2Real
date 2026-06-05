# Smoke Test Report Template

> 每 Phase 关门验证时复制此模板。放在 `docs/smoke/phase-N-smoke.md`。

---

## 板块清单

| # | 板块 | 必须 | 说明 |
|---|------|------|------|
| 1 | Header | ✅ | 日期、测试者、分支、环境 |
| 2 | 测试工具 | ⚠ | Playwright 等自动化时说明脚本 + assertion 数 |
| 3 | 构建验证 | ✅ | tsc、lint、build 结果 |
| 4 | 测试场景 | ✅ | 逐场景表格 |
| 5 | Verdict | ✅ | 一句话结论 |
| 6 | 已知配置变更 | ⚠ | 有环境变更时记录 |
| 7 | 截图索引 | ⚠ | 有截图时 |

---

## 1. Header

```markdown
# Smoke Test Report — Phase N

**日期:** YYYY-MM-DD
**测试者:** Name / Agent Name (Playwright 自动化 / 手动)
**分支:** `phase-N`
**环境:** Windows 10, Node XX, localhost:3000
**Supabase 配置:** 关键开关说明（email confirm on/off 等）
```

## 2. 测试工具

仅当使用自动化工具时：

```markdown
| 脚本 | 覆盖范围 | Assertions |
|------|---------|------------|
| `test_smoke_phaseN.py` | 场景1, 场景2 | N |
```

## 3. 构建验证

```markdown
| 命令 | 结果 |
|------|------|
| `npx tsc --noEmit` | ✅ 0 errors |
| `npm run lint` | ✅ exit 0 (附注 warning) |
| `npm run build` | ✅ 成功 |
```

## 4. 测试场景

每场景一个 subsection：

```markdown
## Test N — 场景名称

| 参数 | 检查项 | 结果 | 截图/备注 |
|------|--------|------|-----------|
| zh | 中文文本可见 | ✅ | `screenshot.png` |
| en | English text visible | ✅ | |
| fr | Texte français visible | ✅ | |
```

### 测试场景清单（按 Phase 类型）：

**Phase 有 UI 时：**
- 页面渲染（每语言）
- 交互流程（golden path）
- 错误/边界（empty state、error state）
- 旧页面不受影响

**Phase 有数据时：**
- 数据量核对
- seed / validate 执行结果
- 在线/离线双模式

**Phase 有权限时：**
- 匿名重定向
- 角色权限验证
- 登出恢复

**所有 Phase：**
- 三语切换（zh/en/fr）

## 5. Verdict

```markdown
## Verdict

✅ **Gate validated YYYY-MM-DD** — N/N tests pass, N/N assertions pass.
```

或：

```markdown
⏸ **Gate open** — N/N pass, N item(s) outstanding (see below).
```

## 6. 已知配置变更

| 变更 | 原因 | 后续 |
|------|------|------|
| Supabase email confirm 关闭 | 测试期间 rate limit | 生产需开启 |

## 7. 截图索引

```markdown
| 文件 | 内容 |
|------|------|
| `migration/screenshots/01_xxx.png` | 描述 |
```
