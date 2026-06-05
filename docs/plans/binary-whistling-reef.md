# 剧情辅助系统方案：时间线 + 人物关系图（流程图式）

## 一、核心思路（用户意图）

用户希望右侧辅助面板与左侧剧情**同时可见**，内容以**流程图/脑图**形式呈现：

- **提纲**：垂直时间线，节点是剧情关键点，从上往下，箭头连接，节点颜色按重要性（红色=重点，橙色=较重点，灰色=普通）
- **线索**：同上，每条线索是一条独立的线，节点是线索状态变化点
- **人物**：**大脑神经元式**流程图，人物是节点，关系是连线，主角在中心，其他角色围绕，直线连接，连线标注关系

**关键交互**：平时节点只是一个圆点，鼠标悬停时展开为标签卡片显示详情。

**AI 打点**：每次剧情生成时，AI 自行判断哪些剧情点值得标记为节点，并给出总结文字和重要性等级。

---

## 二、布局改造

### 2.1 整体布局（story.html）

```
body (flex column, overflow:hidden)
├── .top-bar (flex-shrink:0)
├── .main-area (flex:1, display:flex, overflow:hidden)
│   ├── .container (flex:1, max-width:640px, min-width:0)
│   │   ├── .world-banner
│   │   ├── .story-content (flex:1, overflow-y:auto)
│   │   └── #storyStatus
│   ├── .story-timeline (flex-shrink:0, width:48px, position:relative)
│   │   ├── .timeline-track (垂直居中细线)
│   │   └── .timeline-nodes (节点圆点，绝对定位)
│   ├── .story-sidebar (flex-shrink:0, width:240px, border-left)
│   │   └── 当前激活的 pane 内容（提纲/线索/人物详情列表）
│   └── .edge-tabs (固定在 timeline 右侧边缘的三个书签标签)
└── .bottom-action-bar (max-width:640px)
```

**关键变化：**
- `.container` max-width 从 680px 缩到 **640px**，为右侧让出空间
- `.story-sidebar` 不再是 overlay，而是**始终可见**（width: 240px）
- 新增 `.story-timeline`（48px 宽）作为可视化时间线/流程图区域
- `.edge-tabs` 保留，点击切换 sidebar 中显示哪个 pane + timeline 内容同步切换
- 总右侧宽度：48px (timeline) + 240px (sidebar) = 288px

### 2.2 时间线/流程图视觉设计（提纲 & 线索）

```
|                    ●  ← 节点（圆点，8-12px）
|                    ↓
|                    ●  ← 另一个节点（直线连接）
|                    ↓
|                    ●
```

- 一条垂直细线（2px，浅灰色）贯穿 timeline 区域
- 节点沿时间线均匀分布
- 节点之间用**直线**连接（CSS border 或 SVG line）
- 节点颜色：
  - `.node-critical` — 红色 (#e74c3c)，重点剧情转折
  - `.node-major` — 橙色 (#e67e22)，较重要节点
  - `.node-normal` — 灰色 (#bdc3c7)，普通节点
- 节点 hover：放大 + 显示 tooltip 卡片（节点详情）
- 点击节点：sidebar 高亮对应条目，或 story-content 跳转到对应段落

### 2.3 人物关系图（大脑神经元式）

```
|         ●───朋友───●
|        /  李管家    王捕头
|    主角●
|        \
|         ●───敌人───●
|           神秘人    黑衣刺客
```

- **主角固定在中心**（较大节点，金色）
- 其他角色围绕分布（小节点）
- **直线**连接，连线中间标注关系文字（朋友/敌人/主仆/师徒等）
- 节点 hover：显示人物卡片 tooltip
- 新角色出现时节点有脉冲动画

### 2.4 Sidebar 内容（始终可见，切换 pane）

Sidebar 是固定窄栏，显示当前选中 tab 的详情列表：
- **提纲 pane**：节点列表，带编号、重要性标记、描述，点击跳转
- **线索 pane**：线索卡片列表，按状态着色
- **人物 pane**：人物卡片列表（主角置顶）+ 关系列表

### 2.5 三个 Edge Tabs

- 固定在 timeline 右侧边缘
- 点击切换 sidebar 内容 + timeline/关系图 可视化内容
- 活跃 tab 高亮

---

## 三、AI 打点机制

### 3.1 提示词改造

在 system prompt 中新增【剧情打点】section：

```
【剧情打点 - 智能标记】
在生成剧情的同时，请根据剧情内容智能判断并标记关键节点。

1. 【提纲节点】识别剧情中的关键转折点、高潮、悬念设置点：
   - 类型：转折 / 高潮 / 悬念 / 铺垫 / 揭示
   - 重要性：critical(红色) / major(橙色) / normal(灰色)
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
...

[线索节点]
古宅秘宝 | 伏笔→展开 | 地图碎片出现 | major
...

[人物节点]
char_001 | 李管家 | 1 | 配角 | 古宅管家，知道秘密
...

[关系边]
char_001 | protagonist | 主仆 | 3 | 主角初入古宅，李管家前来迎接
...
```

### 3.2 解析器

```javascript
function parsePlotPoints(fullText) {
    const points = { outline: [], clues: [], characters: [], relations: [] };
    const section = extractSection(fullText, '【剧情打点】');
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
                summary: parts[2]
            });
        } else if (currentSub === 'clues' && parts.length >= 3) {
            points.clues.push({
                name: parts[0],
                stateChange: parts[1],
                desc: parts[2],
                importance: parts[3] || 'normal'
            });
        } else if (currentSub === 'characters' && parts.length >= 4) {
            points.characters.push({
                id: parts[0],
                name: parts[1],
                firstAppear: parseInt(parts[2]) || 0,
                tag: parts[3],
                desc: parts[4] || ''
            });
        } else if (currentSub === 'relations' && parts.length >= 4) {
            points.relations.push({
                source: parts[0],
                target: parts[1],
                type: parts[2],
                strength: parseInt(parts[3]) || 1,
                scene: parts[4] || ''
            });
        }
    });
    return points;
}
```

---

## 四、时间线渲染（提纲 & 线索）

### 4.1 HTML 结构

```html
<div class="story-timeline" id="storyTimeline">
    <div class="timeline-track"></div>
    <div class="timeline-nodes" id="timelineNodes"></div>
</div>
```

### 4.2 渲染逻辑

```javascript
function renderTimeline() {
    const container = document.getElementById('timelineNodes');
    const track = document.querySelector('.timeline-track');
    const nodes = currentSidebarTab === 'outline' ? storyOutline :
                  currentSidebarTab === 'clues' ? storyClues : [];
    
    if (nodes.length === 0) {
        container.innerHTML = '';
        return;
    }
    
    const trackHeight = track.clientHeight;
    const spacing = trackHeight / (nodes.length + 1);
    
    container.innerHTML = nodes.map((node, i) => {
        const top = spacing * (i + 1);
        const importance = node.importance || 'normal';
        const title = node.summary || node.name || node.desc || '';
        return `
            <div class="timeline-node node-${importance}"
                 style="top: ${top}px;"
                 data-index="${i}"
                 onmouseenter="showNodeTooltip(this, '${escapeHTML(title)}')"
                 onmouseleave="hideNodeTooltip()">
            </div>
        `;
    }).join('');
}
```

### 4.3 CSS

```css
.story-timeline {
    width: 48px;
    position: relative;
    background: rgba(248, 246, 242, 0.5);
    border-left: 1px solid var(--border-light);
    flex-shrink: 0;
}
.timeline-track {
    position: absolute;
    left: 50%;
    top: 20px;
    bottom: 20px;
    width: 2px;
    background: linear-gradient(to bottom, transparent, var(--border-light) 5%, var(--border-light) 95%, transparent);
    transform: translateX(-50%);
}
.timeline-node {
    position: absolute;
    left: 50%;
    width: 10px;
    height: 10px;
    border-radius: 50%;
    transform: translate(-50%, -50%);
    cursor: pointer;
    transition: all 0.2s ease;
    border: 2px solid #fff;
    box-shadow: 0 1px 4px rgba(0,0,0,0.15);
}
.timeline-node:hover {
    width: 16px;
    height: 16px;
    box-shadow: 0 2px 8px rgba(0,0,0,0.2);
}
.node-critical { background: #e74c3c; }
.node-major { background: #e67e22; }
.node-normal { background: #bdc3c7; }
```

---

## 五、人物关系图渲染（大脑神经元式）

### 5.1 HTML 结构

```html
<div class="story-timeline" id="storyTimeline">
    <svg class="relation-svg" id="relationSvg"></svg>
    <div class="relation-nodes" id="relationNodes"></div>
</div>
```

### 5.2 布局算法（简化版）

```javascript
function layoutRelationNodes() {
    const chars = getAllCharacters(); // 人物节点数组（含 id, name, tag）
    const relations = getAllRelations(); // 关系边数组（含 source, target, type, strength）
    const centerX = 24; // timeline 宽度一半
    const centerY = 150; // 主角位置
    const radius = 80; // 其他角色围绕半径
    
    const positions = [];
    
    // 主角在中心
    const protagonist = chars.find(c => c.tag === '主角') || chars[0];
    positions.push({ id: protagonist.id, name: protagonist.name, x: centerX, y: centerY, isProtagonist: true });
    
    // 其他角色围绕分布
    const others = chars.filter(c => c.id !== protagonist.id);
    const angleStep = (2 * Math.PI) / Math.max(others.length, 1);
    others.forEach((ch, i) => {
        const angle = angleStep * i - Math.PI / 2; // 从上方开始
        positions.push({
            id: ch.id,
            name: ch.name,
            x: centerX + radius * Math.cos(angle),
            y: centerY + radius * Math.sin(angle),
            isProtagonist: false
        });
    });
    
    return { positions, relations };
}
```

### 5.3 渲染逻辑

```javascript
function renderRelationGraph() {
    const container = document.getElementById('relationNodes');
    const svg = document.getElementById('relationSvg');
    const { positions, relations } = layoutRelationNodes();
    const posMap = new Map(positions.map(p => [p.id, p]));
    
    // 渲染连线（SVG），按关系强度调整线粗细
    const lines = relations.map(r => {
        const src = posMap.get(r.source);
        const tgt = posMap.get(r.target);
        if (!src || !tgt) return '';
        const strokeWidth = Math.max(1, r.strength * 0.6); // 强度1→0.6px, 5→3px
        return `<line x1="${src.x}" y1="${src.y}" 
                      x2="${tgt.x}" y2="${tgt.y}" 
                      stroke="#ddd" stroke-width="${strokeWidth}" 
                      data-type="${escapeHTML(r.type)}" />`;
    }).join('');
    svg.innerHTML = lines;
    
    // 渲染节点（div）
    container.innerHTML = positions.map(p => `
        <div class="relation-node ${p.isProtagonist ? 'protagonist-node' : ''}"
             style="left: ${p.x}px; top: ${p.y}px;"
             onmouseenter="showNodeTooltip(this, '${escapeHTML(p.name)}')"
             onmouseleave="hideNodeTooltip()">
        </div>
    `).join('');
}
```

### 5.4 CSS

```css
.relation-svg {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    pointer-events: none;
}
.relation-nodes {
    position: relative;
    width: 100%;
    height: 100%;
}
.relation-node {
    position: absolute;
    width: 12px;
    height: 12px;
    border-radius: 50%;
    background: var(--accent-primary);
    border: 2px solid #fff;
    box-shadow: 0 1px 4px rgba(0,0,0,0.15);
    transform: translate(-50%, -50%);
    cursor: pointer;
    transition: all 0.2s ease;
}
.relation-node:hover {
    width: 18px;
    height: 18px;
}
.protagonist-node {
    width: 18px;
    height: 18px;
    background: linear-gradient(135deg, var(--accent-primary), var(--accent-secondary));
    box-shadow: 0 2px 8px rgba(196, 151, 30, 0.3);
}
```

---

## 六、Tooltip 系统

```javascript
function showNodeTooltip(el, text) {
    let tooltip = document.getElementById('nodeTooltip');
    if (!tooltip) {
        tooltip = document.createElement('div');
        tooltip.id = 'nodeTooltip';
        tooltip.className = 'node-tooltip';
        document.body.appendChild(tooltip);
    }
    tooltip.textContent = text;
    const rect = el.getBoundingClientRect();
    tooltip.style.left = (rect.left - 210) + 'px';
    tooltip.style.top = (rect.top + rect.height/2 - 20) + 'px';
    tooltip.classList.add('show');
}

function hideNodeTooltip() {
    const tooltip = document.getElementById('nodeTooltip');
    if (tooltip) tooltip.classList.remove('show');
}
```

```css
.node-tooltip {
    position: fixed;
    background: var(--surface-card);
    border: 1px solid var(--border-light);
    border-radius: 8px;
    padding: 8px 12px;
    font-size: 0.8rem;
    color: var(--text-secondary);
    box-shadow: 0 4px 16px rgba(0,0,0,0.1);
    z-index: 200;
    max-width: 200px;
    pointer-events: none;
    opacity: 0;
    transition: opacity 0.2s ease;
}
.node-tooltip.show { opacity: 1; }
```

---

## 七、文件修改清单

### story.html

| 修改内容 | 位置 |
|----------|------|
| CSS: `.story-timeline`, `.timeline-track`, `.timeline-node`, `.relation-svg`, `.relation-node`, `.node-tooltip` | 新增 |
| CSS: 修改 `.story-sidebar` 为始终可见（去掉 translateX，width:240px） | 修改 |
| CSS: `.container` max-width 从 680px 改为 640px | 修改 |
| CSS: `.edge-tabs` 位置调整 | 修改 |
| HTML: 添加 `.story-timeline` 结构 | story.html body |
| HTML: 修改 `.story-sidebar` 为始终可见 | story.html body |
| JS: 新增 `parsePlotPoints()` 解析器（替换旧解析器，支持人物节点+关系边） | 新增 |
| JS: 新增 `renderTimeline()` 渲染函数 | 新增 |
| JS: 新增 `renderRelationGraph()` 渲染函数（支持关系强度可视化） | 新增 |
| JS: 新增 `showNodeTooltip()` / `hideNodeTooltip()` | 新增 |
| JS: 修改 `openTabPanel()` 同时切换 sidebar pane + timeline/关系图 | 修改 |
| JS: systemPrompts.story 更新为【剧情打点】格式 | 修改 |
| JS: `appendNewMeta()` 更新为使用新解析器 | 修改 |

---

## 八、验证方案

1. 打开 story.html，右侧应始终可见 timeline（细线+节点）+ sidebar（内容区）
2. 点击三个 edge-tab，sidebar 内容切换，timeline 内容同步切换
3. 生成剧情后，时间线上出现彩色节点（红/橙/灰）
4. 悬停节点显示 tooltip 卡片
5. 切换到人物 tab，显示大脑神经元式关系图（主角居中，其他角色围绕，直线连接，连线粗细按关系强度显示）
6. 节点颜色按 AI 判定的重要性显示
