# Plan: index.html 功能修复 + 一键生成 + 设计系统对齐

## Context

根据功能文档，index.html 需要：
1. **主角设定逻辑修正**：先选风格(中式/西式)，每维选1个tag（中式7维，西式6维）
2. **世界背景逻辑修正**：从7大类随便选任意数量tag（每大类只选1个）
3. **叙事风格修正**：5维度只选1个tag
4. **generateProtagonist()** 实现：调用DeepSeek API生成主角名字
5. **一键生成按钮**：帮助用户随机选择tag，再根据tag生成对应文字，角色背景必须符合世界观
6. **视觉对齐design.html**：应用设计系统的色彩、字体、间距

## 实现方案

### 1. 主角风格选择（中/西式）

修改 `openTagPanel('protagonist')` 逻辑：
- 打开面板时**先显示风格选择**（中式/西式两个按钮）
- 选择风格后，才显示对应维度的radio列表
- 中式用 `protagonistChineseDims`（7维），西式用 `protagonistWesternDims`（6维）
- 存储 `state.protagonist.style = 'chinese' | 'western'`

修改 `confirmTags()`：
- 如果是 protagonist 且还没选风格，拦截并提示

### 2. 世界背景标签选择

保持当前逻辑不变（从7大类随便选），但修改渲染：
- 每维只显示1个选中的tag（不是多选）
- `buildTagPanelHTML()` 中每组只有一个 `checked`

### 3. 叙事风格

修改 `narrativeDims` 面板：
- 5个维度的tag放在**同一个面板**里选
- 用户只能选1个（单选模式）
- 单选逻辑：每次只保留1个checked

### 4. generateProtagonist() 实现

```javascript
async function generateProtagonist() {
  const s = state.protagonist.selected;
  const style = state.protagonist.style;
  if (!style) {
    showToast('请先选择主角风格（中式/西式）');
    return;
  }
  const required = style === 'chinese' ? 7 : 6;
  if (Object.keys(s).length < required) {
    showToast(`请先完整配置主角设定（${required}个维度）`);
    return;
  }

  // 调用API生成主角名
  const tags = Object.entries(s).map(([k,v]) => `${k}:${v}`).join(', ');
  const prompt = `根据以下性格标签生成1个主角名字和简介...
  标签：${tags}
  风格：${style === 'chinese' ? '中式' : '西式'}`;

  const result = await callDeepSeek(prompt, systemPrompt, onChunk);
  // 解析返回的名字，更新卡片显示
}
```

### 5. 一键生成按钮

**用户点击"一键生成"后的完整流程：**
1. 系统**随机选择**世界标签（从7大类每类随机选1个）
2. 系统**随机选择**叙事风格标签（5维度中随机选1个）
3. 系统**随机选择**主角风格（中式/西式），然后随机选对应维度的tag
4. 根据随机选择的标签，调用 DeepSeek API **生成完整内容**：
   - **第一步**：生成世界背景描述 + 金手指 + 叙事风格描述
   - **第二步**（必须等第一步完成）：根据已生成的世界背景，生成符合该世界观的主角名字 + 简介
     - 主角必须与世界观、金手指保持一致
5. 生成的文字**填充到对应卡片**，状态变为"已生成"
6. 用户可继续手动调整标签或直接点击"开始创作"

**核心价值**：用户完全不需理解标签系统，点击一下就能获得一个完整、可用的创作起点。

### 6. 视觉对齐 design.html

参考 design.html 的 CSS：
- 改用 `--paper-new`, `--ink-*`, `--seal` 变量
- 字体改为 `Noto Serif SC` + `Noto Sans SC`
- 按钮样式对齐
- 卡片样式对齐

## 关键文件

- `d:\moi\tavail\riviereYuan\projet\mind2real\index.html` — 主改文件
- `d:\moi\tavail\riviereYuan\projet\mind2real\story.html` — 参考 API 调用模式 `callDeepSeek()`
- `d:\moi\tavail\riviereYuan\projet\mind2real\design.html` — 设计系统参考

## 验证

1. 打开 index.html，检查顶栏是否有"一键生成"按钮
2. 点击主角卡片的"自定义"→ 应先出现中式/西式选择
3. 选择中式后显示7个维度的单选列表
4. 叙事风格面板只能选1个tag
5. 点击"生成主角"→ 调用API→ 显示名字
6. 点击"一键生成"→ 随机选tag → 自动生成并填充世界背景+主角卡片