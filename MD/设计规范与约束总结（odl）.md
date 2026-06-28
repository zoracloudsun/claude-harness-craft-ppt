# 设计规范与约束总结

> 基于 Claude Code Harness 前言 + 第一章 PPT 全部对话沉淀，仅保留规则、约束与踩坑记录。

---

## 一、设计系统 Token

### 颜色体系（不可随意新增）

| 变量 | 色值 | 用途 |
|------|------|------|
| `--gold` | `#FFD700` | 主强调、标题高亮、重要数字 |
| `--cyan` | `#00F3FF` | 系统级、标签、链接、进度条 |
| `--magenta` | `#FF00C8` | 智能体、编程层、对比强调 |
| `--red` | `#FF4D4D` | 痛点、错误、手动模式 |
| `--green` | `#00C878` | 成功、框架模式、Hooks |
| `--purple` | `#B482FF` | MCP、集成层、辅助色 |
| `--dark` | `#0A0A0A` | 全局背景 |
| `--dark-card` | `rgba(15,15,20,0.85)` | 卡片背景 |

**规则**：新色必须用 `oklch()` 从既有色推导，禁止凭空捏色。

### 字体栈

| 用途 | 字体 | 备选 |
|------|------|------|
| 标题 | `Orbitron` | — |
| 正文 | `Inter` | — |
| 代码/标签 | `JetBrains Mono` | — |

**禁止字体**：Inter、Roboto、Arial、Fraunces、系统字体（除 Inter 已用于正文外）。

---

## 二、字号规范（1920×1080 幻灯片）

### 基准字号（已验证舒适度）

| 元素 | 字号 | 备注 |
|------|------|------|
| tag 标签 | `1rem` | padding 8px 24px |
| slide-title | `3.6rem` | Orbitron, margin 12px 0 8px |
| slide-subtitle | `1.5rem` | margin-bottom 12px |
| slide-subtitle-en | `1.05rem` | opacity .6 |
| divider | `120px` 宽 | margin 20px auto 28px |
| 封面大标题 | `5rem` | Orbitron |
| 封面副标题 | `2.6rem` | — |
| 封面公式 | `3.6rem` | — |
| 卡片标题 | `1.3rem ~ 1.65rem` | 按重要度递增 |
| 卡片描述 | `1.1rem ~ 1.2rem` | 不低于 1rem |
| 卡片角色/标签 | `1rem ~ 1.15rem` | — |
| 引语文字 | `1.15rem ~ 1.3rem` | italic |
| 路径/时间轴名 | `1.55rem` | — |
| 表格单元格 | `1.2rem` | — |
| 注释/脚注 | `1rem` | opacity .6 |

### 强制规则

- **正文绝不低于 `1rem`（16px）**，低于此值在 1920×1080 上不可读
- **H2 标题不超过 `3.6rem`**（中文长句超 100px 必然换行）
- **封面 H1 不超过 `5rem`**（中文 3 个字以上会溢出）
- 字号调整后必须同步更新卡片 `padding`（字号加大 → padding 加大）

---

## 三、全局缩放规则

```js
// 模板和所有子文件统一使用 ×1.15 系数
const scale = Math.min(
  (window.innerWidth - padX) / 1920,
  (window.innerHeight - padY) / 1080
);
deck.style.transform = 'scale(' + (scale * 1.15) + ')';
```

**约束**：所有基于模板的文件必须继承此系数，否则字号看起来会比模板小。

---

## 四、布局陷阱（CJK 专项）

### 陷阱 1：中文大标题不要用负 margin 堆叠

```css
/* ❌ 中文字形相撞 */
.title { margin-top: -20px; }

/* ✅ 用 grid 分列 */
.cover { display: grid; grid-template-columns: 280px 1fr; gap: 80px; align-items: end; }

/* ✅ 或 absolute 精确定位 + 半透明/空心字 */
```

### 陷阱 2：`::after` + `flex: 1` 会打乱 flex 子元素计数

```css
/* ❌ 伪元素变成第三个 flex child */
.row::after { content: ""; flex: 1; }

/* ✅ 用真实 DOM 元素 */
<div class="row">
  <span>LEFT</span>
  <span class="rule"></span>  <!-- 真实元素 -->
  <span>RIGHT</span>
</div>
```

### 陷阱 3：标题强制不换行

```css
.il-title { white-space: nowrap; }  /* "冰山之喻" 等 4 字标题 */
```

---

## 五、幻灯片技术规范

### 防多页同显（关键 CSS）

```css
.slide:not(.active) { display: none !important; }
```

**原因**：每页 `.slide` 常有自己的 `display: grid/flex`，单纯 `.active { display: block }` 会被覆盖。

### 内容撑满高度

```css
.s-xxx { display: flex; flex-direction: column; }
.s-xxx .content-area { flex: 1; align-content: center; }
```

### page-footer 安全距离

所有有 `page-footer` 的 slide 必须设 `padding-bottom ≥ 100px`。

### 持久化播放位置

```js
// 存储键必须唯一，避免前言和第一章冲突
localStorage.setItem('ch1-slide', current);  // 第一章
localStorage.setItem('preface-slide', current); // 前言
```

---

## 六、动画规范

### 入场动画 class 命名

```css
.anim-up    { animation: fadeInUp .6s ease-out both; }
.anim-in    { animation: fadeIn .6s ease-out both; }
.anim-scale { animation: scaleIn .7s ease-out both; }
.anim-left  { animation: slideInL .6s ease-out both; }
.anim-right { animation: slideInR .6s ease-out both; }
```

### 延迟阶梯

```css
.d1 { animation-delay: .1s; }
.d2 { animation-delay: .25s; }
.d3 { animation-delay: .4s; }
.d4 { animation-delay: .55s; }
.d5 { animation-delay: .7s; }
.d6 { animation-delay: .85s; }
.d7 { animation-delay: 1.0s; }
.d8 { animation-delay: 1.15s; }
```

### 交错入场（Stagger Reveal）

```css
.stagger-item { opacity: 0; transform: translateY(20px); }
.stagger-item.revealed { animation: fadeInUp .5s ease-out forwards; }
```

```js
// JS 触发：进入 slide 时逐个添加 revealed class
var items = slide.querySelectorAll('.stagger-item');
items.forEach(function(el) { el.classList.remove('revealed'); });
items.forEach(function(el, i) {
  setTimeout(function() { el.classList.add('revealed'); }, 200 + i * 150);
});
```

### 创新动效清单（已验证可用）

| 效果 | 适用场景 | 实现方式 |
|------|---------|---------|
| 进度条动画 | 数据对比 | `data-width` + CSS transition |
| 环形步骤弹出 | 流程循环 | 绝对定位 + 顺序 setTimeout |
| 级联瀑布 | 流水线 | 每步 300ms 延迟 |
| 分支生长 | 决策树 | 从底部 translateY 升起 |
| 代码块延迟淡入 | 代码展示 | 800ms 后 opacity 变 1 |
| 箭头/符号呼吸 | 连接符 | `@keyframes pulse` 无限循环 |
| SVG 环形进度 | 百分比 | `stroke-dashoffset` 动画 |
| 冰山图 | 隐喻可视化 | SVG + CSS flex 比例对齐 |

---

## 七、表情符号使用规则

### 何时加

- 卡片角色/价值描述前（🎯 最直观、🧠 智能感知）
- 流程步骤标题前（📥 接收输入、🧠 模型推理）
- 场景描述前（🚀 每次发布、🔍 审查大型代码）
- 总结卡片名前（🧊 冰山隐喻、🏢 四层架构）
- 散落组件列表前（⭐ Skill、👥 子智能体）

### 何时不加

- 公式本身（Agent = Model + Harness）
- 代码块内
- 标题主文字（不要让 emoji 抢主标题视觉权重）
- 品牌本身不用 emoji 的场景

### 组件图标统一映射

| 组件 | 标准图标 |
|------|---------|
| CLAUDE.md | 📄 |
| Commands | ⚡ |
| Skills | ⭐ |
| 子智能体 | 👥 |
| Hooks | 🔄 |
| MCP | 🔗 |
| Headless | 🖥️ |
| Agent SDK | 👩‍💻 |
| Plugins | 📦 |

---

## 八、文件组织规范

### 命名

- 文件名可含空格：`第一章\1-10.html`
- 重大修改另存版本号，旧文件保留
- 模板文件名：`模板.html`（全局唯一）

### 目录结构

```
前言\
  1.html          ← 原始封面
  2-11.html       ← 前言完整 deck（11页）
  prologue.html   ← 用户自建副本

第一章\
  1-10.html       ← 第一章完整 deck（27页）

模板.html          ← 全局模板（字体/token/引擎）
```

### 行数限制

单个 HTML 文件不超过 1000 行为佳。当前第一章 27 页约 2800 行，已属上限。

---

## 九、导航引擎标准实现

```html
<div class="nav" id="nav">
  <button id="prevBtn" title="上一页">‹</button>
  <div class="dots" id="dots"></div>
  <div class="page-info" id="pageInfo">1 / 27</div>
  <button id="nextBtn" title="下一页">›</button>
</div>
```

### 键盘快捷键（必须实现）

| 键 | 动作 |
|----|------|
| `→` / `↓` / `空格` | 下一页 |
| `←` / `↑` | 上一页 |
| `Home` | 第一页 |
| `End` | 最后一页 |

### 页码格式

```
当前页 / 总页数（含预留）
```

例：第一章 10 页内容 + 17 页预留 = 显示 `1 / 27`

---

## 十、雪花粒子标准参数

```js
var count = 45;           // 粒子数量
var size = 1.5 + Math.random() * 4;   // 1.5~5.5px
var dur = 10 + Math.random() * 18;    // 10~28s 下落
var sway = 3 + Math.random() * 6;     // 3~9s 摇摆
var opacity = .15 + Math.random() * .5; // 0.15~0.65
```

双动画叠加：`snowfall`（垂直）+ `snowSway`（水平 S 型）。

---

## 十一、已知踩坑记录

| # | 问题 | 原因 | 解法 |
|---|------|------|------|
| 1 | 封面"冰山之喻"换行 | 3.4rem + 280px 列宽不够 | `white-space: nowrap` + 列宽调整 |
| 2 | 冰山水面线与右侧不对齐 | SVG 固定坐标 vs flex 比例 | 统一用百分比：SVG y=36% / flex: 36 |
| 3 | `questions-left .ql-quote` 选择器失效 | HTML class 写重复了 | 内层 div 去掉多余的 `questions-left` class |
| 4 | 多个 `<script type="text/babel">` 不共享作用域 | Babel 编译隔离 | `Object.assign(window, { Comp })` |
| 5 | `const styles` 全局撞名 | 多组件同名 | 每组件用唯一前缀 |
| 6 | `scrollIntoView()` 破坏父容器 | 滚动目标错误 | 改用其他 DOM scroll 方法 |
| 7 | 前言和第一章 localStorage 键冲突 | 同名 `preface-slide` | 第一章改用 `ch1-slide` |
| 8 | 模板字号比实际文件小 | 模板未同步更新 | 模板统一拉大 + rescale ×1.15 |

---

## 十二、提交前自检清单

- [ ] 所有字号 ≥ 1rem（正文）/ ≥ 1.15rem（描述）
- [ ] `.slide:not(.active) { display: none !important }` 已加
- [ ] 每页动画 class 不重复（同一页内无两个 `d3`）
- [ ] localStorage 键名唯一
- [ ] rescale 系数 ×1.15
- [ ] 表情符号覆盖所有"干瘪"文字区域
- [ ] 组件图标映射一致（📄⚡⭐👥🔄🔗🖥️📦）
- [ ] 控制台无报错
- [ ] 雪花粒子 45 个、双动画叠加
- [ ] 键盘快捷键 ←→空格 Home End 全部可用
