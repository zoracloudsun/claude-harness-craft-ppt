# ClaudeCodeHarness

> 基于 HTML 的 Claude Code 技术解析幻灯片项目 — 从内容提取到高保真演示的完整工作流。

---

## 项目简介

本项目是一套**纯 HTML 幻灯片**，用于系统讲解 Claude Code 的技术架构与工程实践。内容从飞书文档截图出发，经过文字提取、排版分析、设计系统约束，最终产出可在浏览器直接打开的 1920×1080 演示文件。

不依赖任何 PPT 软件，不依赖构建工具 — 一个 HTML 文件 = 一套完整的演示 deck。

---

## 目录结构

```text
ClaudeCodeHarness/
├── CLAUDE.md                          # Claude Code 项目规范（13 条硬约束）
├── 模板.html                          # 全局模板（字体/Token/缩放引擎/导航）
│
├── MD/                                # 内容源文件（从图片提取的飞书风文字）
│   ├── 前言.md                        #   前言：五个痛点 + Claude Code 定义
│   ├── 第一章：ClaudeCode技术架构全景.md #   第 1 章：Harness 架构拆解
│   └── 第二章：记忆系统工程实践.md      #   第 2 章：CLAUDE.md / Memory 工程
│
├── PPT/                               # 最终产出（HTML 幻灯片）
│   ├── 前言/
│   │   ├── 母版.html                  #   前言母版
│   │   └── prologue.html             #   前言完整 deck
│   ├── 第一章：ClaudeCode技术架构全景/
│   │   └── chapter1：ClaudeCode技术架构全景.html
│   └── 第二章：记忆系统工程实践/
│       └── chapter2：记忆系统工程实践.html
│
├── 排版布局分析与飞书风格.md           # 飞书风设计方法论
└── 设计规范与约束总结.md               # 详细设计规范（已提炼为 CLAUDE.md）
```

---

## 工作流

```text
截图/图片  →  MD 文字提取  →  模板 + 设计规范  →  PPT HTML 产出
              (飞书风)         (CLAUDE.md)         (浏览器打开)
```

### 1. 内容提取

从飞书文档截图中提取文字内容，保留原始排版风格（标题层级、卡片结构、图标符号）。每张图片对应一页或一组幻灯片。

### 2. 排版分析

分析每张图片的视觉要素（布局、配色、字号、动线），转化为 CSS 可实现的设计指令。

### 3. 幻灯片生成

基于 `模板.html` 的设计系统（8 色 Token + 3 字体栈 + ×1.15 缩放），将内容填充为可交互的 HTML 幻灯片。

---

## 设计系统

### 颜色 Token

| 变量 | 色值 | 用途 |
| --- | --- | --- |
| `--gold` | `#FFD700` | 主强调、标题高亮 |
| `--cyan` | `#00F3FF` | 系统级、标签、链接 |
| `--magenta` | `#FF00C8` | 智能体、编程层 |
| `--red` | `#FF4D4D` | 痛点、错误 |
| `--green` | `#00C878` | 成功、Hooks |
| `--purple` | `#B482FF` | MCP、辅助色 |
| `--dark` | `#0A0A0A` | 全局背景 |
| `--dark-card` | `rgba(15,15,20,0.85)` | 卡片背景 |

### 字体栈

| 用途 | 字体 |
| --- | --- |
| 标题 | Orbitron |
| 正文 | Inter |
| 代码/标签 | JetBrains Mono |

### 关键约束

- 所有模板文件统一 `scale × 1.15` 缩放系数
- 非当前页 `display: none !important`（防多页同显）
- 键盘导航：`→/↓/空格` 下一页、`←/↑` 上一页、`Home` 首页、`End` 末页
- localStorage 按章节区分键名，刷新不丢播放位置

---

## 使用方式

1. 直接用浏览器打开 `PPT/` 目录下的任意 `.html` 文件
2. 键盘 `→` / `←` 翻页，`Home` / `End` 跳转首尾页
3. 无需安装任何依赖，无需启动服务器

---

## 规范文件

- [CLAUDE.md](CLAUDE.md) — 13 条项目硬约束（Token / 字体 / 缩放 / 引擎 / 动画 / 文件管理）
- [设计规范与约束总结.md](设计规范与约束总结.md) — 完整设计规范（含踩坑记录、自检清单）
- [排版布局分析与飞书风格.md](排版布局分析与飞书风格.md) — 飞书风设计方法论
- [.claude/skills/axi-front-design-main/SKILL.md](.claude/skills/axi-front-design-main/SKILL.md) — AI 设计工作流 Skill
