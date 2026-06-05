# 心笔 - UI设计规范文档

## 一、概述

本文档定义"心笔"项目的UI设计语言，作为所有后续开发的统一参考标准。设计理念：**温暖、沉稳、文学感**。避免过度现代化的科技感，保持书卷气和人文温度。

---

## 二、色彩系统

### 2.1 主色板（Primary Palette）

```css
:root {
    /* 背景色 */
    --bg-primary: #faf8f5;      /* 主背景 - 暖白 */
    --bg-secondary: #f0ebe3;    /* 次级背景 - 米色 */
    --bg-tertiary: #e8e0d6;     /* 三级背景 - 深米 */

    /* 文字色 */
    --text-primary: #2d2a26;    /* 主文字 - 深棕黑 */
    --text-secondary: #6b635a;   /* 次级文字 - 灰棕 */
    --text-muted: #9c948a;      /* 弱化文字 - 浅灰棕 */

    /* 强调色 */
    --accent-primary: #c9a86c;   /* 主强调 - 金棕色 */
    --accent-secondary: #b8956a;/* 次强调 - 深金棕 */
    --accent-tertiary: #8b7355; /* 第三强调 - 棕色 */

    /* 功能色 */
    --success: #7d9b76;         /* 成功 - 橄榄绿 */
    --warning: #d4a574;         /* 警告 - 土黄 */
    --error: #c47d7d;           /* 错误 - 暗红 */
    --info: #7d8fb3;            /* 信息 - 灰蓝 */

    /* 边框 */
    --border-light: #e5dfd6;    /* 浅边框 */
    --border-medium: #d4c8b8;   /* 中边框 */
    --border-dark: #b8a898;     /* 深边框 */

    /* 阴影 */
    --shadow-soft: 0 4px 20px rgba(139, 115, 85, 0.08);
    --shadow-medium: 0 8px 30px rgba(139, 115, 85, 0.12);
    --shadow-strong: 0 12px 40px rgba(139, 115, 85, 0.16);
}
```

### 2.2 色彩使用规范

| 场景 | 颜色 | 说明 |
|------|------|------|
| 页面背景 | `--bg-primary` → `--bg-secondary` 渐变 | 垂直渐变，温暖感 |
| 卡片背景 | `rgba(255,255,255,0.85)` | 半透明白，轻盈感 |
| 主按钮 | `--accent-primary` | 金棕色渐变 |
| 次要按钮 | `--bg-secondary` + `--border-medium` | 朴素低调 |
| 强调文字 | `--accent-tertiary` | 标题、标签 |
| 正文文字 | `--text-primary` | 主要阅读内容 |
| 辅助文字 | `--text-secondary` | 说明文字 |
| 弱化文字 | `--text-muted` | 时间戳、提示 |

---

## 三、字体系统

### 3.1 字体家族

```css
/* 标题字体 - 手写书法风格 */
font-family: 'Ma Shan Zheng', cursive;

/* 正文字体 - 衬线体 */
font-family: 'Noto Serif SC', 'Songti SC', serif;

/* 等宽字体 - 代码块 */
font-family: 'Consolas', 'Monaco', monospace;
```

### 3.2 字体层级

| 级别 | 字体 | 字号 | 字重 | 适用场景 |
|------|------|------|------|----------|
| h1 | Ma Shan Zheng | 3.5rem | - | 页面主标题 |
| h2 | Ma Shan Zheng | 1.8rem | - | 卡片标题 |
| h3 | Noto Serif SC | 1.3rem | 600 | 模态框标题 |
| body | Noto Serif SC | 1rem | 400 | 正文内容 |
| small | Noto Serif SC | 0.9rem | 400 | 辅助说明 |
| caption | Noto Serif SC | 0.8rem | 400 | 标签、时间戳 |
| code | Consolas | 0.85rem | - | 代码/提示词编辑 |

### 3.3 行高规范

```css
/* 标题行高 */
line-height: 1.3;

/* 正文行高 */
line-height: 1.7;

/* 紧凑行高 */
line-height: 1.5;
```

---

## 四、间距系统

### 4.1 基础间距单位

使用 **8px** 作为基础单位。

```css
--space-xs: 4px;   /* 紧凑间距 */
--space-sm: 8px;   /* 小间距 */
--space-md: 16px;  /* 中等间距 */
--space-lg: 24px;  /* 大间距 */
--space-xl: 32px;  /* 更大间距 */
--space-2xl: 48px; /* 最大间距 */
```

### 4.2 间距使用场景

| 场景 | 间距 | 说明 |
|------|------|------|
| 组件内边距 | 12px / 16px / 20px | 小/中/大卡片 |
| 组件间距 | 20px | 卡片之间 |
| 区块间距 | 40px | 大区块之间 |
| 元素间距 | 8px / 12px | 同组元素 |
| 按钮间距 | 20px | 并排按钮 |

---

## 五、圆角系统

```css
--radius-sm: 8px;      /* 小圆角 - 输入框 */
--radius-md: 12px;     /* 中圆角 - 按钮 */
--radius-lg: 16px;     /* 大圆角 - 卡片 */
--radius-xl: 20px;     /* 特大圆角 - 模态框 */
--radius-full: 9999px;  /* 完全圆角 - 标签/胶囊按钮 */
```

---

## 六、阴影系统

```css
--shadow-xs: 0 2px 8px rgba(139, 115, 85, 0.06);
--shadow-soft: 0 4px 20px rgba(139, 115, 85, 0.08);
--shadow-medium: 0 8px 30px rgba(139, 115, 85, 0.12);
--shadow-strong: 0 12px 40px rgba(139, 115, 85, 0.16);
--shadow-glow: 0 4px 15px rgba(201, 168, 108, 0.3);
```

---

## 七、组件规范

### 7.1 按钮（Button）

#### 主要按钮
```css
.btn-primary {
    background: linear-gradient(135deg, var(--accent-primary) 0%, var(--accent-secondary) 100%);
    color: #fff;
    padding: 14px 40px;
    font-size: 1.1rem;
    border-radius: 25px;
    box-shadow: var(--shadow-glow);
    border: none;
    cursor: pointer;
    transition: all 0.3s ease;
}
.btn-primary:hover {
    transform: translateY(-3px);
    box-shadow: 0 6px 20px rgba(201, 168, 108, 0.4);
}
```

#### 次要按钮
```css
.btn-secondary {
    background: var(--success);
    color: #fff;
    padding: 14px 30px;
    font-size: 1rem;
    border-radius: 25px;
    border: none;
    cursor: pointer;
    transition: all 0.3s ease;
}
```

#### 刷新按钮
```css
.btn-refresh {
    background: var(--bg-secondary);
    color: var(--text-secondary);
    border: 1px solid var(--border-medium);
    padding: 10px 20px;
    border-radius: 8px;
    cursor: pointer;
    font-size: 0.9rem;
    transition: all 0.3s ease;
}
.btn-refresh:hover {
    background: var(--accent-tertiary);
    color: #fff;
    border-color: var(--accent-tertiary);
}
```

#### 小型按钮
```css
.mini-btn {
    padding: 4px 12px;
    background: var(--bg-secondary);
    border: 1px solid var(--border-medium);
    border-radius: 12px;
    cursor: pointer;
    font-size: 0.75rem;
    color: var(--text-secondary);
    transition: all 0.2s ease;
}
.mini-btn:hover {
    background: var(--accent-tertiary);
    color: #fff;
    border-color: var(--accent-tertiary);
}
.mini-btn.active {
    background: var(--accent-primary);
    color: #fff;
    border-color: var(--accent-primary);
}
```

### 7.2 卡片（Card）

```css
.card {
    background: rgba(255, 255, 255, 0.85);
    border: 1px solid var(--border-light);
    border-radius: 16px;
    padding: 28px 32px;
    margin-bottom: 20px;
    box-shadow: var(--shadow-soft);
    transition: all 0.3s ease;
}
.card:hover {
    box-shadow: var(--shadow-medium);
}
```

### 7.3 标签（Tag）

```css
.tag {
    display: inline-flex;
    align-items: center;
    padding: 6px 14px;
    background: var(--bg-secondary);
    border: 1px solid var(--border-light);
    border-radius: 20px;
    font-size: 0.8rem;
    color: var(--text-secondary);
    transition: all 0.2s ease;
}
.tag:hover {
    background: var(--accent-primary);
    color: #fff;
    border-color: var(--accent-primary);
}
```

### 7.4 输入框（Input）

```css
.form-input {
    width: 100%;
    padding: 12px 16px;
    border: 1px solid var(--border-medium);
    border-radius: 10px;
    font-size: 0.95rem;
    font-family: inherit;
    transition: all 0.2s ease;
    background: #fff;
}
.form-input:focus {
    outline: none;
    border-color: var(--accent-primary);
    box-shadow: 0 0 0 3px rgba(201, 168, 108, 0.15);
}
```

### 7.5 模态框（Modal）

```css
.modal {
    display: none;
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(45, 42, 38, 0.6);
    backdrop-filter: blur(4px);
    z-index: 1000;
    align-items: center;
    justify-content: center;
}
.modal.active { display: flex; }

.modal-content {
    background: #fff;
    border-radius: 20px;
    padding: 32px;
    max-width: 600px;
    width: 90%;
    max-height: 85vh;
    overflow-y: auto;
    box-shadow: 0 20px 60px rgba(0,0,0,0.15);
}
```

---

## 八、页面布局

### 8.1 页面结构

```
┌─────────────────────────────────────────┐
│  顶部工具栏 (固定)                        │
│  [历史] [管理] [设置]                    │
├─────────────────────────────────────────┤
│                                         │
│            页面标题                       │
│            页面副标题                      │
│                                         │
├─────────────────────────────────────────┤
│                                         │
│   ┌─────────────────────────────┐       │
│   │       卡片1                  │       │
│   └─────────────────────────────┘       │
│                                         │
│   ┌─────────────────────────────┐       │
│   │       卡片2                  │       │
│   └─────────────────────────────┘       │
│                                         │
│   ┌─────────────────────────────┐       │
│   │       卡片3                  │       │
│   └─────────────────────────────┘       │
│                                         │
├─────────────────────────────────────────┤
│                                         │
│        [自定义]    [开始创作]            │
│                                         │
└─────────────────────────────────────────┘
```

### 8.2 容器宽度

```css
.container {
    max-width: 800px;
    margin: 0 auto;
    padding: 40px 20px 100px;  /* 底部留白给移动端安全区 */
}
```

### 8.3 卡片头部

```css
.card-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 20px;
}
.card-label {
    font-size: 0.9rem;
    color: var(--accent-tertiary);
    font-weight: 600;
    letter-spacing: 1px;
}
```

---

## 九、动画规范

### 9.1 过渡时长

```css
--transition-fast: 0.15s ease;    /* 快速：hover状态 */
--transition-normal: 0.3s ease;   /* 正常：大部分过渡 */
--transition-slow: 0.5s ease;      /* 慢速：大型元素 */
```

### 9.2 悬停效果

```css
/* 卡片悬停 */
.card:hover {
    transform: translateY(-2px);
    box-shadow: var(--shadow-medium);
}

/* 按钮悬停 */
.btn:hover {
    transform: translateY(-3px);
}

/* 标签悬停 */
.tag:hover {
    background: var(--accent-primary);
    color: #fff;
}
```

### 9.3 加载动画

```css
.loading {
    display: inline-block;
    width: 20px;
    height: 20px;
    border: 2px solid var(--border-light);
    border-top-color: var(--accent-primary);
    border-radius: 50%;
    animation: spin 0.8s linear infinite;
}
@keyframes spin {
    to { transform: rotate(360deg); }
}
```

---

## 十、响应式断点

```css
/* 手机端 */
@media (max-width: 480px) {
    .container { padding: 20px 16px 80px; }
    .card { padding: 20px; }
    .header h1 { font-size: 2.5rem; }
}

/* 平板端 */
@media (max-width: 768px) {
    .container { max-width: 100%; }
}

/* 桌面端 */
@media (min-width: 769px) {
    .container { max-width: 800px; }
}
```

---

## 十一、图标规范

### 11.1 图标风格

- 使用 Unicode 字符作为图标（如 ×, ✓, ↓）
- 或使用 Lucide Icons / Feather Icons 等开源图标库
- 图标尺寸：16px / 20px / 24px
- 图标颜色：继承父元素文字颜色

### 11.2 常用图标

| 图标 | Unicode | 用途 |
|------|---------|------|
| 关闭 | × | 模态框关闭 |
| 刷新 | ↻ | 刷新按钮 |
| 保存 | ✓ | 保存按钮 |
| 设置 | ⚙ | 设置按钮 |

---

## 十二、滚动条样式

```css
::-webkit-scrollbar { width: 8px; height: 8px; }
::-webkit-scrollbar-track { background: var(--bg-secondary); }
::-webkit-scrollbar-thumb {
    background: var(--border-medium);
    border-radius: 4px;
}
::-webkit-scrollbar-thumb:hover { background: var(--accent-tertiary); }
```

---

## 十三、代码规范

### 13.1 CSS 变量命名

```css
/* 类别前缀 */
--bg-*       /* 背景色 */
--text-*     /* 文字色 */
--accent-*   /* 强调色 */
--border-*   /* 边框色 */
--shadow-*   /* 阴影 */
--radius-*   /* 圆角 */
--space-*    /* 间距 */
--transition-* /* 过渡 */
```

### 13.2 Class 命名

```css
/* 块级 */
.card { }
.modal { }
.header { }
.footer { }
.container { }

/* 元素 */
.card-header { }
.card-title { }
.card-body { }
.card-footer { }

/* 修饰符 */
.card-highlight { }
.btn-primary { }
.btn-disabled { }
.tag-active { }

/* 工具类 */
.text-center { }
.text-muted { }
.mt-20 { }
.p-16 { }
```

---

## 十四、设计原则

1. **温暖感**：避免冷色调和科技感，保持书卷气
2. **一致性**：所有页面使用统一的色彩、字体、间距
3. **可读性**：文字与背景对比度足够，保证阅读舒适
4. **层次感**：通过字号、颜色、间距区分信息层级
5. **简洁**：避免过度装饰，功能优先
6. **反馈**：所有交互都有视觉反馈（hover/active/disabled）

---

## 十五、附录：完整CSS模板

```css
:root {
    /* 背景色 */
    --bg-primary: #faf8f5;
    --bg-secondary: #f0ebe3;
    --bg-tertiary: #e8e0d6;

    /* 文字色 */
    --text-primary: #2d2a26;
    --text-secondary: #6b635a;
    --text-muted: #9c948a;

    /* 强调色 */
    --accent-primary: #c9a86c;
    --accent-secondary: #b8956a;
    --accent-tertiary: #8b7355;

    /* 功能色 */
    --success: #7d9b76;
    --warning: #d4a574;
    --error: #c47d7d;
    --info: #7d8fb3;

    /* 边框 */
    --border-light: #e5dfd6;
    --border-medium: #d4c8b8;
    --border-dark: #b8a898;

    /* 阴影 */
    --shadow-soft: 0 4px 20px rgba(139, 115, 85, 0.08);
    --shadow-medium: 0 8px 30px rgba(139, 115, 85, 0.12);
    --shadow-strong: 0 12px 40px rgba(139, 115, 85, 0.16);

    /* 间距 */
    --space-xs: 4px;
    --space-sm: 8px;
    --space-md: 16px;
    --space-lg: 24px;
    --space-xl: 32px;

    /* 圆角 */
    --radius-sm: 8px;
    --radius-md: 12px;
    --radius-lg: 16px;
    --radius-xl: 20px;
}
```

---

## 十五、页面模板

### 15.1 首页 (index.html)

**页面结构：**
- 顶部工具栏（历史、管理、设置）
- 页面标题区（心笔 + 副标题）
- 偏好设置卡片
- 世界背景卡片
- 主角卡片
- 叙事风格卡片
- 底部操作按钮（自定义、开始创作）

**特殊组件：**
- 标签组件（tag）- 带hover变色效果
- 次级信息行（sub-info）- 包含小型按钮

### 15.2 故事页 (story.html)

**页面结构：**
- 顶部工具栏（返回、故事标题、保存、导出）
- 世界信息横幅
- 故事内容区
- 选项区

**组件样式：**

```css
/* 世界信息横幅 */
.world-banner {
    background: rgba(255, 255, 255, 0.7);
    border: 1px solid var(--border-light);
    border-radius: 16px;
    padding: 24px 28px;
    margin-bottom: 24px;
    box-shadow: var(--shadow-soft);
}

/* 故事内容区 */
.story-content {
    background: rgba(255, 255, 255, 0.85);
    border: 1px solid var(--border-light);
    border-radius: 16px;
    padding: 32px 36px;
    margin-bottom: 24px;
    box-shadow: var(--shadow-soft);
    min-height: 300px;
}

/* 选项组件 */
.option-item {
    display: flex;
    align-items: flex-start;
    gap: 12px;
    padding: 16px 20px;
    background: var(--bg-secondary);
    border: 1px solid var(--border-light);
    border-radius: 12px;
    cursor: pointer;
    transition: all 0.3s ease;
}
.option-item:hover {
    background: var(--accent-primary);
    color: #fff;
    border-color: var(--accent-primary);
    transform: translateX(8px);
}
.option-item.selected {
    background: var(--accent-primary);
    color: #fff;
    border-color: var(--accent-primary);
}

/* 加载动画 */
.loading-dots {
    display: flex;
    gap: 4px;
}
.loading-dots span {
    width: 8px;
    height: 8px;
    background: var(--accent-primary);
    border-radius: 50%;
    animation: bounce 1.4s infinite ease-in-out;
}
@keyframes bounce {
    0%, 80%, 100% { transform: scale(0); }
    40% { transform: scale(1); }
}
```

### 15.3 模态框

所有模态框统一使用以下结构：

```html
<div class="modal" id="xxxModal">
    <div class="modal-content">
        <div class="modal-header">
            <h3 class="modal-title">标题</h3>
            <button class="modal-close">×</button>
        </div>
        <div class="modal-body">
            <!-- 内容区 -->
        </div>
    </div>
</div>
```

---

## 十六、页面跳转与数据传递

### 16.1 首页 → 故事页

1. 点击「开始创作」保存当前配置到 `localStorage`
2. 跳转至 `story.html`
3. story.html 读取配置并初始化

```javascript
// 保存
localStorage.setItem('current_story_session', JSON.stringify(storyData));
window.location.href = 'story.html';

// 读取
const storyData = JSON.parse(localStorage.getItem('current_story_session'));
```

### 16.2 历史记录

- 使用 `localStorage` 存储 `story_history` 数组
- 最多保存50条记录
- 每条记录包含：title, date, content, worldTitle, protagonist

---

## 十七、文件结构

```
mind2real/
├── index.html          # 首页 - 创作准备页
├── story.html          # 故事页 - 实际创作
├── docs/
│   ├── Design-System.md # 后续开发优先参考的设计系统
│   ├── UI设计规范.md   # 本文档
│   ├── 写作规范.md     # 写作相关规范
│   ├── 世界背景.md     # 世界设定数据
│   └── ...
└── assets/            # 静态资源
```

---

> 后续新增 UI 建议优先参考 `Design-System.md`，本文档保留为视觉基准与历史规范。

*文档版本：1.1*
*最后更新：2026-05-13*
