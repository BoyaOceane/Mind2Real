# 心笔 Design System

> 目标：把“心笔”的界面语言沉淀成可复用规则。后续新增页面、弹窗、组件时，优先遵守本文档，避免每次重新判断样式。

## 1. 品牌气质

心笔不是工具型 SaaS，也不是强科技感 AI 产品。它更像一张安静的书桌：用户给出欲望、偏好和选择，AI 把它们落成故事。

设计关键词：

| 关键词 | 含义 | UI 表达 |
|---|---|---|
| 温暖 | 不压迫、不冰冷 | 暖白、米色、低对比阴影 |
| 沉稳 | 适合长时间阅读和创作 | 少装饰、弱动效、稳定布局 |
| 文学感 | 有书卷气，不像后台系统 | 衬线字体、留白、章节式标签 |
| 可选择 | 用户只需要会选 | 选项、标签、按钮状态要清晰 |
| AI 隐身 | AI 是笔，不抢用户主导权 | 少用炫光、霓虹、科技渐变 |

避免：

- 大面积蓝紫渐变、霓虹边框、玻璃拟态过重。
- 过度营销式 hero、夸张插画、复杂装饰背景。
- 组件尺寸随机、按钮样式重复发明。
- 正文阅读区被太多操作按钮打断。

## 2. Design Tokens

### 2.1 颜色

```css
:root {
  --bg-primary: #faf8f5;
  --bg-secondary: #f0ebe3;
  --bg-tertiary: #e8e0d6;

  --surface-card: rgba(255, 255, 255, 0.85);
  --surface-card-soft: rgba(255, 255, 255, 0.7);
  --surface-overlay: rgba(45, 42, 38, 0.6);

  --text-primary: #2d2a26;
  --text-secondary: #6b635a;
  --text-muted: #9c948a;
  --text-inverse: #ffffff;

  --accent-primary: #c9a86c;
  --accent-secondary: #b8956a;
  --accent-tertiary: #8b7355;

  --success: #7d9b76;
  --warning: #d4a574;
  --error: #c47d7d;
  --info: #7d8fb3;

  --border-light: #e5dfd6;
  --border-medium: #d4c8b8;
  --border-dark: #b8a898;
}
```

使用原则：

| 用途 | Token | 规则 |
|---|---|---|
| 页面背景 | `--bg-primary` 到 `--bg-secondary` | 纵向渐变，保持温暖底色 |
| 卡片/内容面 | `--surface-card` | 主内容容器，半透明白 |
| 世界信息横幅 | `--surface-card-soft` | 比正文卡片更轻 |
| 主行动 | `--accent-primary`/`--accent-secondary` | 只给主要 CTA 使用 |
| 次级行动 | `--bg-secondary` + `--border-medium` | 不抢注意力 |
| 危险/失败 | `--error` | API 错误、删除确认 |
| 成功 | `--success` | 保存、历史入口 |

### 2.2 字体

```css
:root {
  --font-display: 'Ma Shan Zheng', cursive;
  --font-body: 'Noto Serif SC', 'Songti SC', serif;
  --font-code: 'Consolas', 'Monaco', monospace;
}
```

字体层级：

| 名称 | 大小 | 字重 | 行高 | 场景 |
|---|---:|---:|---:|---|
| `display-xl` | `3.5rem` | regular | `1.3` | 首页品牌标题 |
| `title-lg` | `1.8rem` | regular | `1.3` | 卡片主标题 |
| `title-md` | `1.3rem` | `600` | `1.5` | 弹窗标题、故事页世界名 |
| `body-lg` | `1.05rem` | `400` | `1.9` | 故事正文 |
| `body-md` | `1rem` | `400` | `1.7` | 默认正文 |
| `body-sm` | `0.9rem` | `400` | `1.6` | 说明、描述 |
| `caption` | `0.75rem`-`0.85rem` | `400` | `1.5` | 标签、状态、时间 |

规则：

- 故事正文优先阅读舒适，行高可高于普通 UI。
- 只有品牌标题和少量装饰性标题使用 `--font-display`。
- 表单、按钮、标签仍使用 `--font-body`，保持统一。
- 不使用负字距；正文不随视口宽度动态缩放。

### 2.3 间距

```css
:root {
  --space-xs: 4px;
  --space-sm: 8px;
  --space-md: 16px;
  --space-lg: 24px;
  --space-xl: 32px;
  --space-2xl: 48px;
}
```

使用规则：

| 场景 | 推荐间距 |
|---|---|
| 同组内元素 | `8px` / `12px` |
| 表单项之间 | `16px` |
| 卡片内边距 | 桌面 `28px 32px`，移动端 `20px` |
| 卡片之间 | `20px` / `24px` |
| 页面顶部内容 | 首页 `40px+`，故事页避开顶部栏 |
| 页面底部安全区 | `88px`-`120px` |

### 2.4 圆角与阴影

```css
:root {
  --radius-sm: 8px;
  --radius-md: 12px;
  --radius-lg: 16px;
  --radius-xl: 20px;
  --radius-full: 9999px;

  --shadow-soft: 0 4px 20px rgba(139, 115, 85, 0.08);
  --shadow-medium: 0 8px 30px rgba(139, 115, 85, 0.12);
  --shadow-strong: 0 12px 40px rgba(139, 115, 85, 0.16);
  --shadow-glow: 0 4px 15px rgba(201, 168, 108, 0.3);
}
```

规则：

- 卡片：`--radius-lg`。
- 输入框、选项：`--radius-md`。
- 小按钮、标签、顶部按钮：`--radius-full` 或近似胶囊。
- 阴影只用于层级，不做强装饰。
- `--shadow-glow` 只给主按钮使用。

### 2.5 动效

```css
:root {
  --transition-fast: 0.15s ease;
  --transition-normal: 0.3s ease;
  --transition-slow: 0.5s ease;
}
```

动效规则：

- Hover 只做轻微位移或颜色变化。
- 卡片 hover 可增强阴影，但不要明显跳动。
- 选项 hover 可横向移动 `4px`-`8px`，暗示“选择路径”。
- loading 用 dots 或小 spinner，不使用大面积骨架屏。
- 尊重稳定阅读：故事正文区域不要持续闪烁或漂浮。

## 3. 基础组件

### 3.1 页面容器

```css
.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 40px 20px 100px;
}
```

规则：

- 当前产品以单列沉浸式体验为主，默认 `800px` 宽。
- 不做多栏 dashboard，除非功能确实需要比较信息。
- 移动端容器左右 `16px`。

### 3.2 顶部工具栏

用途：承载全局操作，如返回、保存、导出、历史、设置。

```css
.top-bar {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 100;
}
```

规则：

- 首页顶部操作靠右；故事页左右分区。
- 顶部栏高度稳定，避免内容跳动。
- 移动端可改 `sticky` 或压缩标题，不能遮挡正文。
- 顶部按钮文本短，优先 2-4 个汉字。

### 3.3 卡片 Card

用途：承载一组完整的创作信息，如世界、主角、叙事风格。

```css
.card {
  background: var(--surface-card);
  border: 1px solid var(--border-light);
  border-radius: var(--radius-lg);
  padding: 28px 32px;
  box-shadow: var(--shadow-soft);
}
```

结构：

```html
<section class="card">
  <div class="card-header">
    <div class="card-label">壹 · 世界背景</div>
    <button class="btn btn-refresh">换一组</button>
  </div>
  <div class="card-title">标题</div>
  <div class="card-desc">描述文本</div>
  <div class="tags-row">...</div>
</section>
```

规则：

- 卡片内只放一个主题。
- 不在卡片里再嵌套大卡片。
- `card-label` 用作章节标签，不承担正文说明。
- 描述文本可换行，保持 `white-space: pre-wrap`。

### 3.4 按钮 Button

按钮层级：

| 类型 | Class | 使用场景 |
|---|---|---|
| 主按钮 | `.btn-primary` | 开始创作、继续创作、保存设置 |
| 次按钮 | `.btn-secondary` / `.btn-refresh` | 换一批、测试连接、普通操作 |
| 成功按钮 | `.btn-save` / `.btn-diy` | 保存、辅助正向操作 |
| 小按钮 | `.mini-btn` | 性别切换、金手指开关 |
| 顶部按钮 | `.top-btn` | 顶部工具栏 |

状态必须覆盖：

```css
.btn:disabled,
.top-btn:disabled,
.mini-btn:disabled {
  opacity: 0.58;
  cursor: not-allowed;
  transform: none;
  box-shadow: none;
}
```

规则：

- 同一区域只允许一个主按钮。
- 主按钮必须表达页面下一步，不用于普通刷新。
- loading 时按钮文案改成“生成中...”“测试中...”，并禁用。
- 不用纯颜色区分危险操作，危险操作需要明确文案。

### 3.5 标签 Tag

用途：世界关键词、角色性格、叙事风格。

```css
.tag,
.tag-item {
  display: inline-flex;
  align-items: center;
  padding: 6px 14px;
  background: var(--bg-secondary);
  border: 1px solid var(--border-light);
  border-radius: var(--radius-full);
  font-size: 0.8rem;
  color: var(--text-secondary);
}
```

规则：

- 标签是信息，不默认当按钮。
- 可点击标签需要有 hover/active 状态，并用 `cursor: pointer`。
- 标签不要超过 8 个，过多时分组或折叠。

### 3.6 输入框与文本域

用途：用户偏好、自定义剧情、API Key、提示词编辑。

```css
.form-input,
.form-select,
textarea {
  width: 100%;
  padding: 12px 16px;
  border: 1px solid var(--border-medium);
  border-radius: var(--radius-md);
  font-family: inherit;
  background: #fff;
}
```

状态：

| 状态 | 表现 |
|---|---|
| 默认 | 中性边框 |
| focus | `--accent-primary` 边框 + 浅金色 focus ring |
| disabled | 降低透明度，禁止输入 |
| error | `--error` 文案 + 边框提示 |

规则：

- 长文本输入应限制字数，并显示计数。
- 自定义剧情输入保留 2-4 行，不抢正文空间。
- 提示词编辑使用等宽或接近等宽字体。

### 3.7 弹窗 Modal

用途：设置、历史、提示词管理。

结构：

```html
<div class="modal active">
  <div class="modal-content">
    <div class="modal-header">
      <h3 class="modal-title">标题</h3>
      <button class="modal-close">×</button>
    </div>
    <div class="modal-body">...</div>
  </div>
</div>
```

规则：

- 弹窗最大宽度默认 `600px`，复杂管理类可到 `700px`。
- 弹窗内容超过视口时内部滚动。
- 点击遮罩关闭，但保存类操作不能因误触丢失重要输入。
- 关闭按钮固定在标题栏右侧。

### 3.8 Toast / 状态提示

用途：保存成功、API 错误、操作反馈。

规则：

- 成功/轻错误优先 toast，不用 `alert()` 打断用户。
- 需要用户立即决策时才用弹窗。
- 状态文案应短：`偏好已保存`、`正在生成新的选项...`。
- 长时间任务要有局部状态提示，而不是只禁用按钮。

### 3.9 选项 Option

用途：故事分支选择。

```css
.option-item {
  display: flex;
  align-items: flex-start;
  gap: 12px;
  padding: 16px 20px;
  background: var(--bg-secondary);
  border: 1px solid var(--border-light);
  border-radius: var(--radius-md);
}
```

规则：

- 每个选项由字母标识 + 标题 + 可选描述组成。
- hover 横移，selected 使用主强调色。
- 选项标题最好 6-15 字。
- 自定义输入和选项互斥：输入自定义剧情时取消选项选中。

## 4. 页面模式

### 4.1 首页 / 创作准备页

目标：帮助用户快速确认“世界、主角、叙事”三件事。

结构顺序：

1. 顶部工具栏：历史、管理、设置。
2. 品牌标题：心笔 + 一句理念。
3. 偏好设置：用户输入方向。
4. 世界背景卡片。
5. 主角卡片。
6. 叙事风格卡片。
7. 底部行动：自定义、开始创作。

设计规则：

- 信息卡片按创作顺序排列，不做横向复杂布局。
- 每个随机生成区域必须有“换一组”。
- 未配置 API 时仍展示可理解的空状态，不让页面像坏了。
- “开始创作”只能在设定准备好后执行。

### 4.2 故事页 / 阅读创作页

目标：让用户读一段、选一次、继续推进。

结构顺序：

1. 顶部栏：返回、标题、保存、导出。
2. 世界信息横幅：世界、标签、主角、叙事。
3. 故事正文。
4. 局部状态提示。
5. 选项区。
6. 自定义剧情输入。
7. 继续/换一批按钮。

设计规则：

- 正文是页面主角，操作区不能抢阅读注意力。
- 刷新选项不能清空正文。
- 续写时可临时替换正文为 loading，但失败后要给反馈。
- 历史故事打开时显示完整正文。

### 4.3 管理页 / 提示词编辑

目标：允许高级用户调整 AI 行为。

规则：

- 明确展示可用变量。
- 每个提示词独立分组。
- 保存后 toast 提示。
- 提示词编辑区可滚动，不撑爆弹窗。

## 5. 内容文案规范

### 5.1 语气

文案要像一个安静的创作助手，而不是系统后台。

推荐：

- `正在构思世界...`
- `正在定义角色...`
- `选择选项或输入自定义内容后点击继续`
- `设定还没准备好，稍等生成完成后再开始`

避免：

- `Error: invalid request`
- `提交失败`
- `操作成功！`
- `点击此处进行下一步流程`

### 5.2 标签与按钮文字

规则：

- 按钮：动词优先，如 `保存`、`导出`、`继续创作`。
- 状态：短句优先，如 `创作中...`。
- 卡片 label：可使用中文章节感，如 `壹 · 世界背景`。
- 不把长解释塞进按钮。

## 6. 响应式规则

断点：

```css
@media (max-width: 640px) { ... } /* 首页 */
@media (max-width: 480px) { ... } /* 故事页 */
```

移动端规则：

- 容器左右 `16px`。
- 卡片内边距降到 `20px`。
- 主操作按钮纵向排列，宽度 `100%`。
- 顶部标题允许省略号，不挤压操作按钮。
- 卡片 header 可上下或紧凑排列。
- 不出现横向滚动。

## 7. 状态与错误处理

所有交互组件至少考虑：

| 状态 | 要求 |
|---|---|
| 默认 | 能看出可点击/可输入 |
| hover | 鼠标反馈明确 |
| focus | 键盘可见焦点 |
| active/selected | 选中状态清楚 |
| loading | 禁用重复操作，显示进行中 |
| disabled | 不可操作原因尽量有提示 |
| empty | 空状态有下一步指引 |
| error | 错误信息可理解，不只显示状态码 |

API 相关规则：

- 无 API Key：引导用户去设置，不让功能静默失败。
- 请求中：禁用相关按钮。
- 请求失败：toast 或局部状态提示，并恢复按钮。
- AI 返回文本渲染前必须做 HTML 转义。

## 8. 命名规范

### 8.1 CSS class

使用语义命名，优先复用现有结构。

| 类型 | 示例 |
|---|---|
| 布局 | `.container`, `.top-bar`, `.action-row` |
| 组件 | `.card`, `.modal`, `.toast`, `.option-item` |
| 组件元素 | `.card-header`, `.card-title`, `.modal-content` |
| 状态 | `.active`, `.selected`, `.show` |
| 变体 | `.btn-primary`, `.btn-secondary`, `.btn-refresh` |

避免：

- `.big-box2`
- `.new-style`
- `.left-card-special`
- 大量内联 `style`，除非是临时兼容旧结构。

### 8.2 JavaScript DOM 渲染

规则：

- 插入 AI 或用户文本前使用 `escapeHTML()`。
- 标签统一通过 `renderTags()` 渲染。
- 多处复用状态反馈时使用 `showToast()`。
- loading 状态使用统一函数管理按钮禁用。

## 9. 新增 UI 决策清单

新增任何界面前，先回答：

1. 这是阅读内容、创作选择，还是系统设置？
2. 它应该是卡片、弹窗、顶部操作，还是故事页选项？
3. 页面中是否已经有一个主按钮？
4. 有没有 loading、empty、error、disabled 状态？
5. 移动端按钮和文本会不会挤压？
6. 这里展示的是 AI/用户文本吗？是否已转义？
7. 是否符合“温暖、沉稳、文学感”，还是变成了后台/科技产品？

## 10. 当前组件地图

| 组件 | 当前位置 | 后续建议 |
|---|---|---|
| 顶部工具栏 | `index.html`, `story.html` | 抽成公共样式块 |
| 卡片 | `index.html` | 作为首页主要信息容器 |
| 世界横幅 | `story.html` | 作为故事上下文摘要 |
| 标签 | 两页都有 | 统一 `.tag` 和 `.tag-item` |
| 按钮 | 两页都有 | 统一 button 变体 |
| Modal | `index.html` | 设置/历史/提示词共用 |
| Toast | 两页都有 | 后续可抽成公共函数 |
| Option | `story.html` | 后续分支、章节选择沿用 |
| Story Text | `story.html` | 保持高行高和正文优先 |

## 11. 后续演进建议

短期：

- 把两页重复的 `:root` tokens 抽到 `assets/styles/tokens.css`。
- 把按钮、卡片、弹窗样式抽到 `assets/styles/components.css`。
- 把 `escapeHTML()`、`showToast()`、API 调用抽到公共 JS。

中期：

- 建一个 `docs/components-gallery.html`，展示所有组件状态。
- 给每个组件补齐 hover/focus/disabled/loading 示例。
- 统一 `.tag` 与 `.tag-item`，减少重复样式。

长期：

- 如果项目转为框架开发，按组件拆分为：
  - `TopBar`
  - `Card`
  - `Button`
  - `Tag`
  - `Modal`
  - `Toast`
  - `StoryText`
  - `OptionList`

