# Spec Template

> 写 Phase Design Spec 时复制此模板，替换占位符。删减不适用板块，不硬凑。

---

## 板块清单

每次 Spec 必须包含以下板块（打 ✅ 的必须，其余按需）：

| # | 板块 | 必须 | 说明 |
|---|------|------|------|
| 1 | Header | ✅ | 日期、Phase、前置、范围 |
| 2 | Goal | ✅ | 一句话目标 |
| 3 | Architecture | ✅ | 路由/组件/数据流 |
| 4 | New files | ✅ | 新建/修改文件清单 |
| 5 | Data layer | ⚠ | 有新数据模型时 |
| 6 | UI Design | ⚠ | 有新页面/组件时 |
| 7 | Translation keys | ⚠ | 有新文案时 |
| 8 | Error handling | ✅ | 异常场景处理 |
| 9 | Out of scope | ✅ | 明确不做的事 |

---

## 1. Header

```markdown
# Phase N — Title Design Spec

**Date:** YYYY-MM-DD
**Phase:** N of M
**Predecessor:** Phase N-1 (gate validated YYYY-MM-DD)
**Scope:** 一句话 —— this scope only, not scope.
```

## 2. Goal

一段话，≤3 句。用户能做什么，产生什么价值。

## 3. Architecture

```markdown
### Route structure

/page
  └── Tab: X → XComponent

### Component tree

ParentComponent
├── ChildA
└── ChildB
```

## 4. New files

| File | Action | Purpose |
|------|--------|---------|
| `src/pages/X.tsx` | Create | 页面 |
| `src/components/Y.tsx` | Create | 子组件 |
| `src/data/translations.ts` | Modify | 翻译 key |

## 5. Data layer

```ts
// Types
interface Foo { ... }

// Functions
functionName(params): Promise<ReturnType>
```

说明：数据源、查询方式、权限（RLS/不RLS）。

## 6. UI Design

逐面板/页面描述：
- 用什么组件/样式
- 空状态/正常状态/错误状态怎么展示
- 交互细节（modal、tab、checkbox 等）

## 7. Translation keys

| Key | zh | en | fr |
|-----|----|----|-----|
| `ns.key` | 中文 | English | Français |

## 8. Error handling

| Scenario | Behavior |
|----------|----------|
| 网络错误 | 红色错误横幅 |
| 空数据 | "还没有XXX" 提示 |
| 必填未填 | 提交按钮 disabled |

## 9. Out of scope

- XXX — Phase N+1
- YYY — future enhancement
