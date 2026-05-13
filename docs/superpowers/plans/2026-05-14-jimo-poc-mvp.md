# 即墨 (Jimo) POC MVP 实现计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 根据 `docs/superpowers/specs/2026-05-14-jimo-design.md` 实现完整的三页面架构

**Architecture:** 单文件 HTML，内联 CSS/JS，点击即用。index.html（创作入口）、story.html（故事阅读+侧边栏）、admin.html（提示词管理）

**Tech Stack:** HTML/CSS/JS（无框架）、localStorage、DeepSeek API（流式）

---

## 文件结构

```
mind2real/
├── index.html          # 新建：创作入口（三卡片 + 顶栏）
├── story.html          # 改造：删除选项+侧边栏重构
├── admin.html          # 新建：提示词管理（四板块）
├── design.html         # 不动
└── docs/
    ├── superpowers/
    │   ├── specs/
    │   │   └── 2026-05-14-jimo-design.md  # 功能设计（本文档依据）
    │   └── plans/
    │       └── 2026-05-14-jimo-poc-mvp.md  # 本计划
```

---

## Task 1: 重构 index.html 为创作入口

**Files:**
- Create: `index.html`（完全重写）
- Reference: `docs/superpowers/specs/2026-05-14-jimo-design.md` 第三章

### 1.1 顶栏 HTML（约第1-30行）

```html
<div class="top-bar">
    <div class="top-left">
        <span class="top-bar-title">即墨</span>
    </div>
    <div class="top-right">
        <button class="top-btn" id="btnHistory" onclick="toggleHistoryPanel()">历史记录</button>
        <button class="top-btn" id="btnApi" onclick="toggleApiPanel()">API设置</button>
        <a href="admin.html" class="top-btn">提示词管理</a>
    </div>
</div>
```

### 1.2 三卡片配置区 HTML

```html
<div class="main-content">
    <!-- 卡片1：世界背景 -->
    <div class="card card-world">
        <div class="card-header">
            <span class="card-title">世界背景</span>
            <button class="card-badge world" onclick="toggleTagPanel('world')">自定义 ▼</button>
        </div>
        <div class="card-body" id="worldCardBody">
            <div class="card-tags" id="worldTags"></div>
            <textarea class="card-input" id="worldInput" placeholder="输入世界背景描述..."></textarea>
            <button class="btn btn-secondary" onclick="generateWorld()">生成</button>
        </div>
        <!-- Tag选择面板 -->
        <div class="tag-panel" id="worldTagPanel" style="display:none;"></div>
    </div>

    <!-- 卡片2：主角设定 -->
    <div class="card card-protagonist">
        <div class="card-header">
            <span class="card-title">主角设定</span>
            <button class="card-badge protagonist" onclick="toggleTagPanel('protagonist')">自定义 ▼</button>
        </div>
        <div class="card-body" id="protagonistCardBody">
            <div class="card-tags" id="protagonistTags"></div>
            <textarea class="card-input" id="goldenFingerInput" placeholder="输入金手指（主角特殊能力）..."></textarea>
            <button class="btn btn-secondary" onclick="generateProtagonist()">生成主角</button>
        </div>
        <div class="tag-panel" id="protagonistTagPanel" style="display:none;"></div>
    </div>

    <!-- 卡片3：叙事风格 -->
    <div class="card card-narrative">
        <div class="card-header">
            <span class="card-title">叙事风格</span>
            <button class="card-badge narrative" onclick="toggleTagPanel('narrative')">自定义 ▼</button>
        </div>
        <div class="card-body">
            <div class="card-tags" id="narrativeTags"></div>
        </div>
        <div class="tag-panel" id="narrativeTagPanel" style="display:none;"></div>
    </div>
</div>
```

### 1.3 底栏 CTA

```html
<div class="bottom-action-bar">
    <div class="bottom-action-inner">
        <textarea class="custom-input" id="storyDirectionInput" placeholder="输入剧情方向（可选）..." rows="1"></textarea>
        <button class="btn btn-primary" id="btnStart" onclick="startCreation()">开始创作</button>
    </div>
</div>
```

### 1.4 状态管理 JS

```javascript
let currentStory = {
    worldTitle: '',
    worldDesc: '',
    worldTags: [],
    protagonist: '',
    protagonistTags: [],
    goldenFinger: '',
    narrative: '',
    narrativeTags: [],
    history: [],
    storyOutline: [],
    storyClues: [],
    characterCards: [],
    characterRelations: []
};

let selectedTags = {
    world: { 物理规律: '', 时代位置: '', 技术路线: '', 超自然力量: '', 文明形态: '', 存在状态: '', 异化方向: '' },
    protagonist: { 气质底色: '', 能量状态: '', 处事风格: '', 人格面具: '', 内在驱动: '' },
    narrative: { 主体聚焦: '', 认知视角: '', 时态视角: '', 叙述可靠性: '', 结构形式: '' }
};
```

### 1.5 Tag 选择面板逻辑

每个卡片的"自定义"按钮点击后，展开对应 tag 面板：
- 按维度分组显示所有可选 tag
- 每个维度内单选（radio）
- 已选 tag 高亮显示
- 关闭面板后卡片更新显示

### 1.6 开始创作逻辑

```javascript
function startCreation() {
    // 检查必需项
    if (!hasValidWorld()) { showToast('请先生成世界背景'); return; }
    if (!hasValidProtagonist()) { showToast('请先生成主角设定'); return; }
    if (!hasValidNarrative()) { showToast('请选择叙事风格'); return; }
    
    const config = getApiConfig();
    if (!config.apiKey) { showToast('请先设置 API Key'); return; }
    
    // 保存到 localStorage
    currentStory.createdAt = new Date().toISOString();
    currentStory.updatedAt = currentStory.createdAt;
    localStorage.setItem('current_story_session', JSON.stringify(currentStory));
    
    // 跳转 story.html
    window.location.href = 'story.html';
}
```

---

## Task 2: 改造 story.html — 删除选项系统

**Files:**
- Modify: `story.html`
- Reference: `docs/superpowers/specs/2026-05-14-jimo-design.md` 第四章

### 2.1 删除选项 UI 容器

**位置：约第619-622行**

```html
<!-- 删除这段 -->
<div id="optionsSection" style="display:none;">
    <div class="options-list" id="optionsList"></div>
</div>
```

### 2.2 删除选项相关变量

**位置：约第670-671行**

```javascript
// 删除这两行
let currentOptions = [];
let selectedOption = -1;
```

### 2.3 删除选项相关函数

删除以下函数定义：
- `parseOptions()` — 约第923-948行
- `displayOptions()` — 约第980-993行
- `selectOption()` — 约第1010-1016行
- `refreshOptions()` — 约第1018-1062行

### 2.4 更新 continueStory 函数

**位置：约第1064-1137行**

更新 `continueStory` 函数，删除所有选项相关逻辑：
- 删除 `parseOptions()` 调用
- 删除 `displayOptions()` 调用
- 删除 `selectedOption` 重置
- 删除 `continueHint` 更新为"已选择：xxx"
- segment 中不再存储 options

```javascript
async function continueStory() {
    if (isLoading) return;

    const customInput = document.getElementById('customInput').value.trim();
    let userChoice = customInput || '继续发展剧情';

    setLoadingState(true, '正在续写...');
    showLoading();

    const config = getApiConfig();
    if (!config.apiKey) {
        showToast('请先在首页设置 API Key');
        setLoadingState(false);
        return;
    }

    const pref = localStorage.getItem('user_preference') || '';
    const storySoFar = currentStory.history.map(h => h.content).join('\n\n');

    const prompt = `${pref}
用户选择：${userChoice}

请继续故事，接续以下内容：
${storySoFar}

要求：
1. 第三人称继续
2. 情节要有推进和转折
3. 文字直白有张力
4. 字数约252字，句尾完整`;

    const systemPrompt = renderPrompt(systemPrompts.story, {
        worldTitle: currentStory.worldTitle,
        worldDesc: currentStory.worldDesc || '',
        protagonist: currentStory.protagonist,
        goldenFinger: currentStory.goldenFinger || '无',
        narrative: currentStory.narrative || ''
    });

    try {
        const result = await callDeepSeek(prompt, systemPrompt, (text) => {
            displayStoryContent(text);
        });

        if (result) {
            const cleanResult = ensureCompleteSentence(result);
            const displayText = stripStructuredMeta(cleanResult);
            const segment = {
                content: displayText,
                timestamp: Date.now()
            };
            currentStory.history.push(segment);
            appendNewMeta(cleanResult);
            saveStoryData();
            autoSave();
        }
    } catch (error) {
        console.error('继续创作失败:', error);
        showToast('继续创作失败，请稍后再试');
    }

    setLoadingState(false);
}
```

### 2.5 更新 init 函数

**位置：约第850-858行**

删除 init 中的选项展示逻辑：

```javascript
// 原来
if (lastSegment.options) {
    currentOptions = lastSegment.options;
    displayOptions(currentOptions);
}

// 改为：不需要选项展示
```

### 2.6 更新 generateOpening 函数

同样删除 `parseOptions()` 调用和选项展示逻辑。

---

## Task 3: 改造 story.html — 侧边栏布局调整

**Files:**
- Modify: `story.html` CSS 和 HTML 结构
- Reference: `docs/plan/binary-whistling-reef.md`

### 3.1 CSS 调整

```css
/* container max-width 从 640px 缩到 560px */
.container {
    max-width: 560px;  /* 原 640px */
}

/* timeline 从 32px 扩到 48px */
.story-timeline {
    width: 48px;  /* 原 32px */
}

/* sidebar 从 220px 扩到 240px */
.story-sidebar {
    width: 240px;  /* 原 220px */
}
```

### 3.2 timeline 节点样式调整

```css
.timeline-node {
    width: 10px;
    height: 10px;
}
.timeline-node:hover {
    width: 16px;
    height: 16px;
}
.node-critical { background: #e74c3c; }
.node-major { background: #e67e22; }
.node-normal { background: #bdc3c7; }
```

---

## Task 4: 改造 story.html — 更新剧情打点格式

**Files:**
- Modify: `story.html` system prompt 和解析器
- Reference: `docs/plan/binary-whistling-reef.md` 第三、四章

### 4.1 更新 systemPrompts.story 中的打点格式

**位置：约第721-754行**

```javascript
【剧情打点 - 智能标记】
在生成剧情的同时，请根据剧情内容智能判断并标记关键节点。

1. 【提纲节点】识别剧情中的关键转折点、高潮、悬念设置点：
   - 类型：转折 / 高潮 / 悬念 / 铺垫 / 揭示
   - 重要性：critical(重点剧情，红色) / major(较重要，橙色) / normal(普通，灰色)
   - 一句话总结（15字以内）

2. 【线索节点】识别线索的状态变化点，每条线索是一条独立时间线：
   - 线索名
   - 状态变化：伏笔→展开 / 展开→揭示 / 揭示→闭合 等
   - 一句话描述
   - 重要性

3. 【人物节点】识别新人物出场：
   - 人物ID（唯一标识，如 char_001）
   - 人物名
   - 首次出场位置（章节序号）
   - 人物标签：主角 / 配角 / 反派 / 中立
   - 一句话简述

4. 【关系边】识别已有节点之间的关系变化：
   - 源人物ID
   - 目标人物ID
   - 关系类型：敌对 / 盟友 / 主仆 / 血缘 / 师徒 / 爱慕 / 竞争 等
   - 关系强度（1-5分，随剧情变化可更新）
   - 首次建立关系的场景描述

输出格式（在正文结束后另起一行）：
【剧情打点】
[提纲节点]
转折 | critical | 主角发现密室暗门
悬念 | major | 神秘人留下警告信

[线索节点]
古宅秘宝 | 伏笔→展开 | 地图碎片出现 | major

[人物节点]
char_001 | 李管家 | 1 | 配角 | 古宅管家，知道秘密

[关系边]
char_001 | protagonist | 主仆 | 3 | 主角初入古宅，李管家前来迎接
```

### 4.2 更新 parsePlotPoints 解析器

```javascript
function parsePlotPoints(fullText) {
    const points = { outline: [], clues: [], characters: [], relations: [] };
    const section = extractSection(fullText, '【剧情打点】', ['【提纲节点】', '【线索追踪】', '【人物卡】']);
    if (!section) return points;

    let currentSub = '';
    section.split('\n').forEach(line => {
        line = line.trim();
        if (!line) return;
        if (line === '[提纲节点]') { currentSub = 'outline'; return; }
        if (line === '[线索节点]') { currentSub = 'clues'; return; }
        if (line === '[人物节点]') { currentSub = 'characters'; return; }
        if (line === '[关系边]') { currentSub = 'relations'; return; }

        const parts = line.split('|').map(s => s.trim());
        if (currentSub === 'outline' && parts.length >= 3) {
            points.outline.push({
                type: parts[0],
                importance: parts[1],
                summary: parts[2],
                _new: true
            });
        } else if (currentSub === 'clues' && parts.length >= 3) {
            points.clues.push({
                name: parts[0],
                stateChange: parts[1],
                desc: parts[2],
                importance: parts[3] || 'normal',
                _new: true
            });
        } else if (currentSub === 'characters' && parts.length >= 4) {
            points.characters.push({
                id: parts[0],
                name: parts[1],
                firstAppear: parseInt(parts[2]) || 0,
                tag: parts[3],
                desc: parts[4] || '',
                _new: true
            });
        } else if (currentSub === 'relations' && parts.length >= 4) {
            points.relations.push({
                source: parts[0],
                target: parts[1],
                type: parts[2],
                strength: parseInt(parts[3]) || 1,
                scene: parts[4] || '',
                _new: true
            });
        }
    });
    return points;
}
```

### 4.3 更新 appendNewMeta 函数

```javascript
function appendNewMeta(result) {
    const plotPoints = parsePlotPoints(result);
    let hasNewData = false;

    if (plotPoints.outline.length > 0) {
        storyOutline.push(...plotPoints.outline);
        renderOutline();
        renderTimeline();
        hasNewData = true;
    }
    if (plotPoints.clues.length > 0) {
        plotPoints.clues.forEach(newClue => {
            const exist = storyClues.find(c => c.name === newClue.name);
            if (exist) {
                exist.stateChange = newClue.stateChange;
                exist.desc = newClue.desc;
                exist.importance = newClue.importance;
            } else {
                storyClues.push(newClue);
            }
        });
        renderClues();
        renderTimeline();
        hasNewData = true;
    }
    if (plotPoints.characters.length > 0) {
        plotPoints.characters.forEach(newChar => {
            const exist = characterCards.find(c => c.id === newChar.id);
            if (!exist) {
                characterCards.push({ ...newChar, new: true });
            }
        });
        renderChars();
        renderRelationGraph();
        setTimeout(() => { characterCards.forEach(c => delete c.new); }, 3500);
        hasNewData = true;
    }
    if (plotPoints.relations.length > 0) {
        plotPoints.relations.forEach(newRel => {
            const exist = characterRelations.find(r => 
                r.source === newRel.source && r.target === newRel.target
            );
            if (exist) {
                exist.type = newRel.type;
                exist.strength = newRel.strength;
                exist.scene = newRel.scene;
            } else {
                characterRelations.push({ ...newRel, new: true });
            }
        });
        renderRelationGraph();
        setTimeout(() => { characterRelations.forEach(r => delete r.new); }, 3500);
        hasNewData = true;
    }
    // fallback to old format if no new data...
}
```

### 4.4 添加 characterRelations 状态

```javascript
let characterRelations = [];  // 新增
```

### 4.5 更新 renderChars 函数

```javascript
function renderChars() {
    const list = document.getElementById('charsList');
    if (!list) return;
    let html = '';
    
    // 主角置顶
    if (currentStory.protagonist) {
        const protagonistChar = characterCards.find(c => c.tag === '主角');
        html += '<div class="ch-item"><span class="ch-avatar">主</span><span class="ch-name">' + 
            escapeHTML(currentStory.protagonist) + '</span><span class="ch-rel">主角</span></div>';
    }
    
    // 其他角色
    characterCards.forEach(c => {
        if (c.tag !== '主角') {
            const ini = c.name ? c.name[0] : '?';
            html += '<div class="ch-item"><span class="ch-avatar">' + escapeHTML(ini) + '</span>' +
                '<span class="ch-name">' + escapeHTML(c.name) + '</span>' +
                '<span class="ch-rel">' + escapeHTML(c.tag || '') + '</span></div>';
        }
    });
    
    list.innerHTML = html || '<div style="text-align:center;padding:20px;color:var(--text-muted);font-size:0.78rem;">暂无角色</div>';
}
```

### 4.6 更新 renderRelationGraph 函数

```javascript
function renderRelationGraph() {
    const container = document.getElementById('relationNodes');
    const svg = document.getElementById('relationSvg');
    if (!container || !svg) return;

    const all = [];
    if (currentStory.protagonist) all.push({ id: 'protagonist', name: currentStory.protagonist, isP: true });
    characterCards.forEach(c => all.push({ id: c.id, name: c.name, isP: false }));

    if (all.length < 1) {
        container.innerHTML = '';
        svg.innerHTML = '';
        return;
    }

    // 布局计算
    const centerX = 24;
    const centerY = 150;
    const radius = 80;
    const positions = all.map((a, i) => {
        if (a.isP) return { id: a.id, name: a.name, x: centerX, y: centerY };
        const angle = (2 * Math.PI * (i - 1) / Math.max(all.length - 1, 1)) - Math.PI / 2;
        return {
            id: a.id,
            name: a.name,
            x: centerX + radius * Math.cos(angle),
            y: centerY + radius * Math.sin(angle)
        };
    });
    const posMap = new Map(positions.map(p => [p.id, p]));

    // 渲染连线
    let svgContent = '<svg style="position:absolute;top:0;left:0;width:48px;height:100%;overflow:visible;">';
    characterRelations.forEach(r => {
        const src = posMap.get(r.source);
        const tgt = posMap.get(r.target);
        if (src && tgt) {
            const strokeWidth = Math.max(1, r.strength * 0.6);
            svgContent += '<line x1="' + src.x + '" y1="' + src.y + '" x2="' + tgt.x + '" y2="' + tgt.y + '" ' +
                'stroke="#ddd" stroke-width="' + strokeWidth + '" />';
        }
    });
    svgContent += '</svg>';
    svg.innerHTML = svgContent;

    // 渲染节点
    let nodeHtml = positions.map(p => {
        const size = p.isP ? 14 : 10;
        const bg = p.isP ? 'linear-gradient(135deg, var(--accent-primary), var(--accent-secondary))' : 'var(--accent-primary)';
        return '<div style="position:absolute;left:' + p.x + 'px;top:' + p.y + 'px;' +
            'width:' + size + 'px;height:' + size + 'px;border-radius:50%;background:' + bg + ';' +
            'border:2px solid #fff;transform:translate(-50%,-50%);cursor:pointer;box-shadow:0 1px 4px rgba(0,0,0,0.15);" ' +
            'onmouseenter="showNodeTooltip(this,\'' + escapeHTML(p.name) + '\')" ' +
            'onmouseleave="hideNodeTooltip()"></div>';
    }).join('');
    container.innerHTML = svgContent + nodeHtml;
}
```

---

## Task 5: 新建 admin.html 提示词管理

**Files:**
- Create: `admin.html`
- Reference: `docs/superpowers/specs/2026-05-14-jimo-design.md` 第五章

### 5.1 HTML 结构

```html
<!doctype html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>即墨 - 提示词管理</title>
    <link href="https://fonts.googleapis.com/css2?family=Noto+Serif+SC:wght@400;600&family=Noto+Sans+SC:wght@300;400;500&display=swap" rel="stylesheet">
    <!-- CSS 复用 story.html :root 变量 -->
</head>
<body>
    <div class="top-bar">
        <div class="top-left">
            <a href="javascript:history.back()" class="top-btn">← 返回</a>
        </div>
        <span class="top-bar-title">提示词管理</span>
        <div></div>
    </div>

    <main class="admin-main">
        <!-- 世界观生成 -->
        <div class="prompt-block" id="blockWorld">
            <div class="prompt-block-header">
                <span class="prompt-block-title">世界观生成</span>
                <div class="prompt-block-actions">
                    <button class="btn-small" onclick="toggleEdit('World')">编辑</button>
                    <button class="btn-small" onclick="resetBlock('World', 'worldPrompt')">恢复默认</button>
                </div>
            </div>
            <div class="prompt-block-body">
                <pre class="prompt-readonly" id="readWorld"></pre>
                <div class="prompt-editor">
                    <textarea id="editWorld"></textarea>
                    <button class="btn-small primary" onclick="saveBlock('World', 'worldPrompt')">保存</button>
                </div>
            </div>
        </div>

        <!-- 主角生成 -->
        <div class="prompt-block" id="blockProtagonist">
            <div class="prompt-block-header">
                <span class="prompt-block-title">主角生成</span>
                <div class="prompt-block-actions">
                    <button class="btn-small" onclick="toggleEdit('Protagonist')">编辑</button>
                    <button class="btn-small" onclick="resetBlock('Protagonist', 'protagonistPrompt')">恢复默认</button>
                </div>
            </div>
            <div class="prompt-block-body">
                <pre class="prompt-readonly" id="readProtagonist"></pre>
                <div class="prompt-editor">
                    <textarea id="editProtagonist"></textarea>
                    <button class="btn-small primary" onclick="saveBlock('Protagonist', 'protagonistPrompt')">保存</button>
                </div>
            </div>
        </div>

        <!-- 故事续写 -->
        <div class="prompt-block" id="blockStory">
            <div class="prompt-block-header">
                <span class="prompt-block-title">故事续写</span>
                <div class="prompt-block-actions">
                    <button class="btn-small" onclick="toggleEdit('Story')">编辑</button>
                    <button class="btn-small" onclick="resetBlock('Story', 'storySystemPrompt')">恢复默认</button>
                </div>
            </div>
            <div class="prompt-block-body">
                <pre class="prompt-readonly" id="readStory"></pre>
                <div class="prompt-editor">
                    <textarea id="editStory"></textarea>
                    <button class="btn-small primary" onclick="saveBlock('Story', 'storySystemPrompt')">保存</button>
                </div>
            </div>
        </div>

        <!-- 叙事风格 -->
        <div class="prompt-block" id="blockNarrative">
            <div class="prompt-block-header">
                <span class="prompt-block-title">叙事风格</span>
                <div class="prompt-block-actions">
                    <button class="btn-small" onclick="toggleEdit('Narrative')">编辑</button>
                    <button class="btn-small" onclick="resetBlock('Narrative', 'narrativeStylePrompt')">恢复默认</button>
                </div>
            </div>
            <div class="prompt-block-body">
                <pre class="prompt-readonly" id="readNarrative"></pre>
                <div class="prompt-editor">
                    <textarea id="editNarrative"></textarea>
                    <button class="btn-small primary" onclick="saveBlock('Narrative', 'narrativeStylePrompt')">保存</button>
                </div>
            </div>
        </div>
    </main>

    <script>
        // 默认提示词（从 story.html systemPrompts 提取）
        const DEFAULT_PROMPTS = {
            worldPrompt: `你是世界构建专家...`,
            protagonistPrompt: `你是角色设计专家...`,
            storySystemPrompt: `你是专业网文作家...`,
            narrativeStylePrompt: `叙事方式配置...`
        };

        // 初始化、编辑、保存、重置逻辑...
    </script>
</body>
</html>
```

### 5.2 localStorage 存储结构

```javascript
localStorage.setItem('prompt_config', JSON.stringify({
    worldPrompt: String,
    protagonistPrompt: String,
    storySystemPrompt: String,
    narrativeStylePrompt: String,
    updatedAt: String  // "YYYY-MM-DD HH:mm"
}));
```

---

## Task 6: 更新 story.html 顶栏按钮

**Files:**
- Modify: `story.html:579-590`
- Reference: `docs/superpowers/specs/2026-05-14-jimo-design.md` 4.1

### 6.1 顶栏 HTML

```html
<div class="top-bar">
    <div class="top-left">
        <a href="index.html" class="back-btn">← 返回首页</a>
        <span class="story-title" id="storyTitle">故事创作</span>
    </div>
    <div class="top-right">
        <button class="top-btn" onclick="toggleApiPanel()">API设置</button>
        <a href="admin.html" class="top-btn">提示词管理</a>
        <button class="top-btn btn-save" onclick="saveStory()">保存</button>
        <button class="top-btn btn-export" onclick="exportStory()">导出</button>
    </div>
</div>
```

### 6.2 添加 API 设置面板

在 story.html 中添加 API 设置模态框/面板，供用户修改 API Key。

---

## Task 7: 集成测试

**Files:**
- Test: `index.html`, `story.html`, `admin.html`

### 测试清单

- [ ] index.html 三卡片配置正常显示
- [ ] 世界背景/主角/叙事风格的 Tag 选择面板能正确展开和选择
- [ ] 点击开始创作跳转到 story.html
- [ ] story.html 顶栏按钮正确（API设置/提示词管理/保存/导出/返回）
- [ ] story.html 无选项 UI
- [ ] story.html 生成故事后侧边栏更新（提纲/线索/人物）
- [ ] story.html timeline 节点可点击跳转
- [ ] story.html 人物关系图显示正确
- [ ] story.html 底栏输入框可用
- [ ] story.html 保存/导出功能正常
- [ ] admin.html 四个板块正常显示
- [ ] admin.html 编辑/保存/重置功能正常
- [ ] 从 admin.html 返回上一页正常

---

## 执行选项

**Plan complete and saved to `docs/superpowers/plans/2026-05-14-jimo-poc-mvp.md`**

**Two execution options:**

**1. Subagent-Driven (recommended)** — I dispatch a fresh subagent per task, review between tasks, fast iteration

**2. Inline Execution** — Execute tasks in this session using executing-plans, batch execution with checkpoints

Which approach?