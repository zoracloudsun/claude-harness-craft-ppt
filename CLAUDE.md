# CLAUDE.md — ClaudeCodeHarness 项目规范

> 基于前言 + 第一章 PPT 对话沉淀，提炼为可执行约束。

---

## 1. 设计 Token

1. 颜色只用以下 8 个 CSS 变量，禁止新增自定义色值。

| 变量 | 色值 | 用途 |
|------|------|------|
| `--gold` | `#FFD700` | 主强调、标题高亮 |
| `--cyan` | `#00F3FF` | 系统级、标签、链接 |
| `--magenta` | `#FF00C8` | 智能体、编程层 |
| `--red` | `#FF4D4D` | 痛点、错误 |
| `--green` | `#00C878` | 成功、Hooks |
| `--purple` | `#B482FF` | MCP、辅助色 |
| `--dark` | `#0A0A0A` | 全局背景 |
| `--dark-card` | `rgba(15,15,20,0.85)` | 卡片背景 |

---

## 2. 字体与字号

1. 标题 `Orbitron`，正文 `Inter`，代码/标签 `JetBrains Mono`。
2. 正文字号 ≥ `1rem`（16px），H2 ≤ `3.6rem`，封面 H1 ≤ `5rem`。

---

## 3. 缩放

1. 所有基于模板的文件必须继承 `scale × 1.15` 系数。

---

## 4. 幻灯片引擎

1. 非当前页必须 `display: none !important`。
2. 内容区 `flex: 1; align-content: center` 撑满高度。
3. 有 `page-footer` 的 slide `padding-bottom ≥ 100px`。
4. localStorage 键名按章节区分（`preface-slide`、`ch1-slide`…）。

---

## 5. 动画

1. 入场 class：`.anim-up` / `.anim-in` / `.anim-scale` / `.anim-left` / `.anim-right`。
2. 延迟阶梯 `.d1` ~ `.d8`（0.1s ~ 1.15s），同一页内不重复。

---

## 6. 表情符号

1. 卡片描述、流程步骤、场景描述、组件列表前加 emoji；公式、代码块、标题主文字不加。
2. 组件图标映射：📄CLAUDE.md ⚡Commands ⭐Skills 👥子智能体 🔄Hooks 🔗MCP 🖥️Headless 👩‍💻Agent SDK 📦Plugins。

---

## 7. 文件管理

1. 重大修改另存版本号，旧文件保留。
2. 模板文件 `模板.html` 全局唯一，单个 HTML ≤ 2800 行。

---

## 8. 导航

1. 键盘快捷键必须实现：`→/↓/空格` 下一页、`←/↑` 上一页、`Home` 首页、`End` 末页。
