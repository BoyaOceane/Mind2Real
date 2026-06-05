# 即墨 (Jimo) — AI 指令

> AI Agent 在项目中工作时的行为规范与项目知识。

## Commands

纯 HTML/CSS/JS 项目，无构建工具，直接在浏览器中打开即可运行：

| 操作 | 命令 |
|------|------|
| 运行 | `python3 -m http.server 8080` 后浏览器打开 `http://localhost:8080`（必须 `http://`，`file://` 阻止 API 请求） |
| 验证 | 检查浏览器 console 无 JS 错误；检查 API 调用正常返回 |

## Architecture

```
即墨/
├── index.html     ← 创作入口：三卡片配置（世界/主角/叙事）+ API 设置 + 历史记录
├── story.html     ← 故事阅读：流式输出 + 侧边辅助（提纲/线索/人物） + 剧情引导
├── admin.html     ← 提示词管理：4 板块（世界观/主角/续写/风格）查看/编辑/恢复/测试
├── design.html    ← 设计系统文档页面
└── docs/
    ├── specs/     ← 设计规格
    ├── plans/     ← 实现计划
    ├── records/   ← 执行记录
    ├── smoke/     ← Smoke 门禁报告
    ├── reference/ ← 外部参考（Tag 数据、写作准则）
    └── templates/ ← 文档模板
```

**技术要点：**
- 纯前端，无框架。CSS 自定义属性（`--paper-*`, `--ink-*`, `--seal-*`）统一设计系统
- 状态持久化：`localStorage`（配置、历史记录、提示词）
- API：DeepSeek API（流式输出），API Key 存 localStorage
- 设计风格：宋式美学，宣纸底色暖灰 `oklch(96% 0.010 92)`，朱砂点缀 `oklch(48% 0.16 28)`

**Model 命名约定：**
- `index.html` 用 `deepseek-v4-flash`（快速生成）
- `story.html` 用 `deepseek-v4-pro`（高质量续写）
- `admin.html` 测试用 `deepseek-v4-flash`

## Documentation Conventions

遵循 Spec → Plan → Record → Smoke → STATUS 流程：

1. **Spec** (`docs/specs/`) — 设计规格，说明要做什么
2. **Plan** (`docs/plans/`) — 实现计划，面向 agentic worker 执行
3. **Record** (`docs/records/`) — 执行记录，关联 commit
4. **Smoke** (`docs/smoke/`) — 门禁验证报告
5. **STATUS.md** — 项目总控台，跨 Phase 状态追踪。**每次执行任务后必须更新**（日期、状态变化、新增/完成条目）

模板参考 `docs/templates/`。

Commit 风格：`feat:`, `fix:`, `refactor:` 等 conventional commits，中文描述。

## AI 问题追踪

问题与纠正记录在 `AI-ISSUES.md`。每次 AI 出错（理解偏差、虚构、重复犯错）后追加条目并提炼前车之鉴。
