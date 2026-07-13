# 先选路再铺路 — 分支优先续写 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 将 story.html 续写交互从"用户输入方向 → AI 直接写正文"改为"AI 给 4 个带钩子分支 + 第 5 个自己写 → 用户选路 → AI 扩写正文"。

**Architecture:** 仅改动 story.html。新增 `generateBranches()` 调用 AI 返回 4 个分支 JSON；重构 `continueStory()` 接收方向参数；新增分支卡片 UI 替换原底部输入区；选中分支或自写后复用现有扩写+剧情打点流程。开场、侧边栏、文风学习、历史归档均不动。

**Tech Stack:** 纯 HTML/CSS/JS，DeepSeek API（分支用 deepseek-v4-flash，正文沿用用户配置），localStorage。

## Global Constraints

- 纯前端，无构建步骤，浏览器直接打开（必须 `http://` 而非 `file://`）
- 不引入新依赖、新文件（仅改 story.html）
- API Key 存 localStorage，不提交
- 不改动 index.html / admin.html / design.html
- 不改动开篇生成、侧边栏、文风学习、历史归档、去 AI 味逻辑
- Commit 风格：`feat:` / `refactor:` conventional commits，中文描述
- 验证方式：手动浏览器验证（`python3 -m http.server 8080` → `http://localhost:8080`），console 无 JS 错误
- 无测试框架，每个任务的"验证"步骤为手动浏览器检查

**Spec:** `docs/superpowers/specs/2026-07-13-branch-first-continuation-design.md`

---

## File Structure

仅修改一个文件：`story.html`

| 区域 | 行号参考 | 改动 |
|------|----------|------|
| CSS（底栏样式） | ~506-555 | 新增分支卡片样式 |
| HTML（底栏结构） | 1192-1201 | 重构为"给走向"按钮 + 分支卡片区 + 自己写输入 |
| `callDeepSeek()` | 2712 | 新增可选 `modelOverride` 参数 |
| `generateBranches()` | 新增（插在 continueStory 前） | 调 AI 返回 4 分支 |
| `continueStory()` | 2347 | 接收 `direction` 参数，不再直接读 customInput |
| `renderBranches()` / 分支交互 | 新增 | 渲染卡片、处理选中 |
| 底栏事件绑定 | ~2699-2707 | 适配新交互 |

---

### Task 1: 给 `callDeepSeek` 增加可选 modelOverride 参数

**Files:**
- Modify: `story.html:2712` (`callDeepSeek` 函数签名与 body.model 赋值)

**Interfaces:**
- Produces: `callDeepSeek(prompt, systemPrompt, onChunk, enableThinking = false, modelOverride = null)` — 当 modelOverride 非空时用其替换 config.model

- [ ] **Step 1: 修改 callDeepSeek 签名与 model 赋值**

将 `story.html:2712` 的函数定义：

```javascript
async function callDeepSeek(prompt, systemPrompt, onChunk, enableThinking = false) {
```

改为：

```javascript
async function callDeepSeek(prompt, systemPrompt, onChunk, enableThinking = false, modelOverride = null) {
```

将 `story.html:2719` 的 `model: config.model,` 改为：

```javascript
model: modelOverride || config.model,
```

- [ ] **Step 2: 验证现有功能不受影响**

Run: `python3 -m http.server 8080` → 浏览器打开 `http://localhost:8080/story.html`
Expected: 开篇生成正常（流式输出），console 无错误。不传 modelOverride 时行为与之前一致。

- [ ] **Step 3: Commit**

```bash
git add story.html
git commit -m "refactor: callDeepSeek 增加可选 modelOverride 参数"
```

---

### Task 2: 新增 `generateBranches()` 函数

**Files:**
- Modify: `story.html` — 在 `continueStory()` 定义前（约 2347 行之前）插入新函数

**Interfaces:**
- Consumes: `currentStory`（history/worldTitle/worldTags/protagonist/narrative）、`storyOutline`、`storyClues`、`characterCards`、`getActiveProfile()`、`formatStylePrompt()`、`callDeepSeek()`、`parseStyleJson()`、`getApiConfig()`
- Produces: `generateBranches()` → `Promise<Array<{direction: string, hooks: string}>>` — 返回 4 个分支对象，失败返回空数组

- [ ] **Step 1: 在 continueStory 前插入 generateBranches 函数**

在 `story.html` 的 `async function continueStory() {`（2347 行）之前插入：

```javascript
// 生成分支走向：基于当前故事状态返回 4 个带钩子的剧情走向
async function generateBranches() {
    const config = getApiConfig();
    if (!config.apiKey) {
        showToast('请先在首页设置 API Key');
        return [];
    }

    const recentStory = currentStory.history.slice(-3).map(h => h.content).join('\n\n');
    const outlineSummary = storyOutline.slice(-5).map(o => `${o.importance || 'normal'}｜${o.summary || ''}`).join('\n') || '（暂无）';
    const clueSummary = storyClues.slice(-6).map(c => `${c.name}（${c.state || '伏笔'}）：${c.desc || ''}`).join('\n') || '（暂无）';
    const charSummary = characterCards.slice(0, 8).map(c => `${c.name}（${c.tag || '角色'}）：${c.desc || ''}`).join('\n') || '（暂无）';

    const styleBlock = formatStylePrompt(getActiveProfile());

    const prompt = `${getPrefPrompt()}${styleBlock}${getAntiAiPromptBlock()}
你是一位故事思维伙伴。基于以下故事状态，给出 4 个不同的剧情走向。

【最近正文】
${recentStory}

【提纲】
${outlineSummary}

【线索】
${clueSummary}

【人物】
${charSummary}

【世界】${currentStory.worldTitle}（${currentStory.worldTags.join('、')}）
【主角】${currentStory.protagonist}${currentStory.protagonistDesc ? ' — ' + currentStory.protagonistDesc : ''}
【叙事】${currentStory.narrative}

要求：
1. 给出 4 个走向，每个 direction 2-3 句
2. 4 个走向在情绪基调（紧张/舒缓/悬疑/温情）、节奏（推进/停顿/回溯）、牵动线索上要有差异，不能都是同一类型
3. 每个走向必须连回至少一条现有线索或人物，不能凭空起
4. 不写正文，只写走向和钩子
5. 只输出合法 JSON 数组，不要 markdown 代码块，不要任何额外文字

JSON 格式：
[
  { "direction": "2-3句走向描述", "hooks": "牵动的线索/人物/情绪" },
  { "direction": "...", "hooks": "..." },
  { "direction": "...", "hooks": "..." },
  { "direction": "...", "hooks": "..." }
]`;

    const systemPrompt = '你是故事思维伙伴，只输出 JSON 数组。';

    try {
        const { content } = await callDeepSeek(prompt, systemPrompt, null, false, 'deepseek-v4-flash');
        if (!content) return [];
        const branches = parseStyleJson(content);
        if (!Array.isArray(branches)) return [];
        return branches.slice(0, 4).map(b => ({
            direction: String(b.direction || '').trim(),
            hooks: String(b.hooks || '').trim()
        })).filter(b => b.direction);
    } catch (e) {
        console.error('生成分支失败:', e);
        return [];
    }
}
```

- [ ] **Step 2: 验证函数可调用**

Run: 浏览器打开 story.html，生成开篇后，在 console 执行：
```javascript
generateBranches().then(console.log)
```
Expected: 返回包含 4 个 `{direction, hooks}` 对象的数组，console 无错误。若 API Key 未设置则返回 `[]` 并弹出 toast。

- [ ] **Step 3: Commit**

```bash
git add story.html
git commit -m "feat: 新增 generateBranches 生成 4 个带钩子剧情走向"
```

---

### Task 3: 重构 `continueStory()` 接收 direction 参数

**Files:**
- Modify: `story.html:2347-2430` (`continueStory` 函数)

**Interfaces:**
- Consumes: 来自 Task 2 的分支 direction 字符串，或用户自写文本
- Produces: `continueStory(direction = '')` — direction 非空时直接用作 userChoice，为空时回退到默认"继续发展剧情"（兼容旧调用）

- [ ] **Step 1: 修改 continueStory 签名与 userChoice 逻辑**

将 `story.html:2347-2357`：

```javascript
async function continueStory() {
    if (isLoading) return;

    const customInput = document.getElementById('customInput').value.trim();
    let userChoice = '';

    if (customInput) {
        userChoice = customInput;
    } else {
        userChoice = '继续发展剧情';
    }
```

改为：

```javascript
async function continueStory(direction = '') {
    if (isLoading) return;

    let userChoice = '';
    if (direction) {
        userChoice = direction;
    } else {
        const customInput = document.getElementById('customInput').value.trim();
        userChoice = customInput || '继续发展剧情';
    }
```

- [ ] **Step 2: 移除 continueStory 内对 customInput 的清空（改由调用方处理）**

将 `story.html:2416`：

```javascript
document.getElementById('customInput').value = '';
```

改为（保留清空，因为自写路径仍用此输入框，清空无副作用）：

```javascript
const customInputEl = document.getElementById('customInput');
if (customInputEl) customInputEl.value = '';
```

（此步保持清空行为，只是改为安全访问。）

- [ ] **Step 3: 验证 continueStory 仍可工作**

Run: 浏览器打开 story.html，生成开篇后，在底部输入框打字"主角决定探索密室"→ 点"继续创作"
Expected: AI 按该方向续写正文，正常流式输出，侧边栏更新。再在 console 执行 `continueStory('主角转身离开')` 应也能触发续写。

- [ ] **Step 4: Commit**

```bash
git add story.html
git commit -m "refactor: continueStory 接收 direction 参数，解耦输入框"
```

---

### Task 4: 新增分支卡片 UI（HTML + CSS）

**Files:**
- Modify: `story.html` CSS 区（约 555 行后新增样式）
- Modify: `story.html:1192-1201`（底栏 HTML 结构重构）

**Interfaces:**
- Produces: DOM 结构 — `#branchPanel`（分支卡片区，默认隐藏）、`#btnGiveDirection`（给走向按钮）、`#customInputWrap`（自写输入区，默认隐藏）、`#btnConfirmCustom`（自写确认按钮）

- [ ] **Step 1: 在 CSS 区追加分支卡片样式**

在 `story.html` 的 `.bottom-continue-hint { ... }` 规则之后（约 555 行后）追加：

```css
/* 分支卡片区 */
.branch-panel {
    display: none;
    flex-direction: column;
    gap: 8px;
    margin-bottom: 8px;
}
.branch-panel.active { display: flex; }
.branch-card {
    padding: 10px 14px;
    border: 1px solid var(--border-medium);
    border-radius: var(--radius-md);
    background: var(--bg-primary);
    cursor: pointer;
    transition: all var(--transition-fast);
}
.branch-card:hover {
    border-color: var(--accent-primary);
    background: rgba(201, 168, 108, 0.06);
}
.branch-card .branch-direction {
    font-size: 0.88rem;
    color: var(--text-primary);
    line-height: 1.5;
}
.branch-card .branch-hooks {
    font-size: 0.74rem;
    color: var(--text-muted);
    margin-top: 4px;
}
.branch-card.branch-self {
    border-style: dashed;
    text-align: center;
    color: var(--text-muted);
    font-size: 0.85rem;
}
.custom-input-wrap {
    display: none;
    flex-direction: column;
    gap: 8px;
    margin-bottom: 8px;
}
.custom-input-wrap.active { display: flex; }
```

- [ ] **Step 2: 重构底栏 HTML 结构**

将 `story.html:1192-1201`：

```html
<!-- 固定底栏 -->
<div class="bottom-action-bar">
    <div class="bottom-continue-hint" id="continueHint">输入剧情方向，或直接点击继续</div>
    <div class="bottom-action-inner">
        <textarea class="custom-input" id="customInput" rows="1" placeholder="输入你想要的剧情方向..."></textarea>
        <div class="bottom-action-btns">
            <button class="btn btn-primary" id="btnContinue" onclick="isLoading ? stopGeneration() : continueStory()">继续创作</button>
        </div>
    </div>
</div>
```

改为：

```html
<!-- 固定底栏 -->
<div class="bottom-action-bar">
    <div class="bottom-continue-hint" id="continueHint">点"给走向"看可能的发展，或直接自己写</div>
    <!-- 分支卡片区（默认隐藏） -->
    <div class="branch-panel" id="branchPanel"></div>
    <!-- 自写输入区（默认隐藏） -->
    <div class="custom-input-wrap" id="customInputWrap">
        <textarea class="custom-input" id="customInput" rows="2" placeholder="写下你想要的剧情走向..."></textarea>
        <div class="bottom-action-btns">
            <button class="btn btn-primary" id="btnConfirmCustom">按此方向续写</button>
        </div>
    </div>
    <div class="bottom-action-inner">
        <div class="bottom-action-btns" style="width:100%; justify-content:flex-end;">
            <button class="btn btn-primary" id="btnContinue" onclick="isLoading ? stopGeneration() : continueStory()">给走向</button>
        </div>
    </div>
</div>
```

- [ ] **Step 3: 修改 setLoadingState 的按钮文案**

将 `story.html:1387`：

```javascript
btn.textContent = loading ? '停止创作' : '继续创作';
```

改为：

```javascript
btn.textContent = loading ? '停止创作' : '给走向';
```

- [ ] **Step 4: 验证 UI 渲染**

Run: 浏览器打开 story.html
Expected: 底栏显示"给走向"按钮（非"继续创作"），hint 文字更新，分支卡片区和自写区默认不可见。开篇生成时按钮变"停止创作"。console 无错误。

- [ ] **Step 5: Commit**

```bash
git add story.html
git commit -m "feat: 底栏重构为给走向按钮 + 分支卡片区 + 自写输入区"
```

---

### Task 5: 分支渲染与选中交互

**Files:**
- Modify: `story.html` — 在 `generateBranches()` 之后插入 `renderBranches()` 和 `clearBranches()`
- Modify: `story.html` 底栏事件绑定区（约 2699-2707）

**Interfaces:**
- Consumes: `generateBranches()`（Task 2）、`continueStory(direction)`（Task 3）
- Produces: `renderBranches(branches)` — 渲染 4 卡片 + 自己写；`clearBranches()` — 收起分支区；`showCustomInput()` / `hideCustomInput()` — 切换自写区

- [ ] **Step 1: 在 generateBranches 后插入渲染与状态函数**

在 `generateBranches()` 函数之后（`continueStory` 之前）插入：

```javascript
// 渲染分支卡片 + 自己写
function renderBranches(branches) {
    const panel = document.getElementById('branchPanel');
    panel.innerHTML = '';

    branches.forEach((b, i) => {
        const card = document.createElement('div');
        card.className = 'branch-card';
        card.innerHTML = `<div class="branch-direction">${b.direction}</div>` +
            (b.hooks ? `<div class="branch-hooks">牵动：${b.hooks}</div>` : '');
        card.onclick = () => {
            clearBranches();
            continueStory(b.direction);
        };
        panel.appendChild(card);
    });

    // 第 5 项：自己写
    const selfCard = document.createElement('div');
    selfCard.className = 'branch-card branch-self';
    selfCard.textContent = '＋ 自己写一个走向';
    selfCard.onclick = () => {
        clearBranches();
        showCustomInput();
    };
    panel.appendChild(selfCard);

    panel.classList.add('active');
}

function clearBranches() {
    const panel = document.getElementById('branchPanel');
    panel.innerHTML = '';
    panel.classList.remove('active');
}

function showCustomInput() {
    document.getElementById('customInputWrap').classList.add('active');
    document.getElementById('customInput').focus();
}

function hideCustomInput() {
    document.getElementById('customInputWrap').classList.remove('active');
    document.getElementById('customInput').value = '';
    document.getElementById('continueHint').textContent = fmtContinueHint(false);
}
```

- [ ] **Step 2: 给"给走向"按钮绑定生成分支逻辑**

将 `story.html:1198` 行按钮（Task 4 已改为）：

```html
<button class="btn btn-primary" id="btnContinue" onclick="isLoading ? stopGeneration() : continueStory()">给走向</button>
```

改为：

```html
<button class="btn btn-primary" id="btnContinue" onclick="isLoading ? stopGeneration() : onGiveDirection()">给走向</button>
```

在 `renderBranches` 函数之后插入 `onGiveDirection`：

```javascript
async function onGiveDirection() {
    setLoadingState(true, '正在生成走向...');
    const branches = await generateBranches();
    setLoadingState(false);
    if (branches.length === 0) {
        showToast('生成走向失败，请稍后再试');
        return;
    }
    renderBranches(branches);
}
```

- [ ] **Step 3: 绑定自写确认按钮**

在底栏事件绑定区（约 2699 行附近，`customInput` 的 input 监听之后）追加：

```javascript
document.getElementById('btnConfirmCustom').addEventListener('click', () => {
    const val = document.getElementById('customInput').value.trim();
    if (!val) {
        showToast('请先写下你的走向');
        return;
    }
    hideCustomInput();
    continueStory(val);
});
```

- [ ] **Step 4: 保留 Ctrl+Enter 快捷键（自写区）**

找到 `story.html:2704` 附近的 keydown 监听：

```javascript
document.getElementById('customInput').addEventListener('keydown', (event) => {
    if ((event.ctrlKey || event.metaKey) && event.key === 'Enter') {
        continueStory();
    }
});
```

改为：

```javascript
document.getElementById('customInput').addEventListener('keydown', (event) => {
    if ((event.ctrlKey || event.metaKey) && event.key === 'Enter') {
        document.getElementById('btnConfirmCustom').click();
    }
});
```

- [ ] **Step 5: 验证完整分支流程**

Run: 浏览器打开 story.html，生成开篇后：
1. 点"给走向" → 按钮变"停止创作"短暂出现 → 4 张分支卡片 + "自己写"出现
2. 点某分支 → 卡片消失 → AI 按该走向流式续写 → 侧边栏更新 → 底栏回到"给走向"
3. 再点"给走向" → 点"自己写" → 输入框展开 → 打字 → 点"按此方向续写" → AI 续写
4. Ctrl+Enter 在自写区可触发续写

Expected: 上述全流程正常，console 无错误，开篇/润色/保存/导出不受影响。

- [ ] **Step 6: Commit**

```bash
git add story.html
git commit -m "feat: 分支卡片渲染与选中交互，给走向按钮触发生成"
```

---

### Task 6: 收尾与文档更新

**Files:**
- Modify: `STATUS.md`

- [ ] **Step 1: 更新 STATUS.md**

在 Phase ledger 表格追加一行：

```markdown
| **P6 — 分支优先续写** | `docs/superpowers/plans/2026-07-13-branch-first-continuation-plan.md` | – | ✅ done | 手动验证 | 给走向 → 4 分支 + 自己写 → 选路 → AI 扩写 |
```

更新顶部 Last updated 与 Active branches：

```markdown
> **Last updated:** 2026-07-13 — P6 分支优先续写：给走向 → 4 分支 + 自己写 → 选路 → AI 扩写
> **Active branches:** `feat/style-learning-loop` HEAD (feat: 分支优先续写)
```

- [ ] **Step 2: 全量 smoke 验证**

Run: 浏览器打开 `http://localhost:8080`
1. index.html：配置三卡片 → API Key → 开始创作（不受影响）
2. story.html：开篇生成 → 给走向 → 选分支 → 续写 → 给走向 → 自己写 → 续写
3. 润色按钮（P4）仍可用
4. 保存 / 导出 / 历史记录（P5）仍可用
5. admin.html 提示词管理不受影响
6. console 全程无 JS 错误

Expected: 全部通过。

- [ ] **Step 3: Commit**

```bash
git add STATUS.md
git commit -m "docs: 更新 STATUS.md 记录 P6 分支优先续写完成"
```

---

## Self-Review

**Spec coverage:**
- §3.1 续写循环（给走向 → 4 分支 + 自己写 → 选路 → 扩写）→ Task 2+4+5
- §3.2 开场不变 → 不改动开篇逻辑，仅 Task 1 的 modelOverride 不影响开篇 ✓
- §3.3 分支卡片格式（走向 + 钩子）→ Task 5 renderBranches ✓
- §3.4 五个选项约束（4 差异分支 + 自己写）→ Task 2 prompt 约束 + Task 5 第 5 卡 ✓
- §4 不动的部分 → 计划未触碰这些模块 ✓
- §5.1 底部交互重构 → Task 4 ✓
- §5.2 generateBranches → Task 2 ✓
- §5.3 continueStory 改造 → Task 3 ✓
- §5.4 分支状态管理（临时，不持久化）→ Task 5 clearBranches，不写 localStorage ✓
- §6 分支 prompt → Task 2 ✓
- §7 无新增持久化 → 计划无 localStorage 新增 ✓
- §8.1 分支用 flash → Task 1 modelOverride + Task 2 传 'deepseek-v4-flash' ✓
- §8.2 正文扩写复用现有 → Task 3 continueStory 沿用 callDeepSeek ✓
- §11 验证标准 → 各 Task 验证步骤 + Task 6 全量 smoke ✓

**Placeholder scan:** 无 TBD/TODO，每个步骤含具体代码或命令。✓

**Type consistency:** `generateBranches()` 返回 `Array<{direction, hooks}>`（Task 2），`renderBranches(branches)` 消费同一形状（Task 5），`continueStory(direction)` 接收字符串（Task 3），分支点击传 `b.direction`（Task 5）—— 一致。✓
