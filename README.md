# Trip Map Builder

从零散信息到可部署的交互式行程地图页面。

三阶段流水线：**规划行程 → 小红书调研 → 生成地图页面**。

## Demo

**[tokyo-trip-pi.vercel.app](https://tokyo-trip-pi.vercel.app)** — 东京 4 泊 5 日行程地图

源码：[hiyeshu/tokyo-trip](https://github.com/hiyeshu/tokyo-trip)

## 这个技能做什么

给 AI agent 一套完整的旅行规划工作流：

1. 用户丢过来机票截图、酒店截图、想去的地方
2. Agent 提取硬约束（日期、航班、酒店位置），按区域分组，主动删掉塞不下的点
3. 通过 [OpenCLI](https://github.com/jackwener/OpenCLI) + Chrome CDP 上小红书搜店，提取真实评价
4. 生成单文件 HTML 页面：Leaflet 地图 + 时间轴卡片 + Google Maps 导航 + 小红书链接 + 支付方式标签
5. 推到 GitHub，Vercel 自动部署，手机打开直接用

## 安装

一行命令安装（[skills.sh](https://skills.sh) 生态）：

```bash
npx skills add hiyeshu/trip-map-builder
```

或者手动 clone 到 skills 目录：

```bash
# Cursor
git clone https://github.com/hiyeshu/trip-map-builder.git ~/.cursor/skills/trip-map-builder

# Claude Code
git clone https://github.com/hiyeshu/trip-map-builder.git ~/.claude/skills/trip-map-builder
```

## 触发词

说这些话会激活技能：

- "做个行程" / "行程规划" / "行程地图"
- "plan my trip" / "trip map" / "build itinerary"
- "帮我查一下小红书上这家店怎么样"

## 工作流

### Phase 1：规划行程

从用户给的碎片信息里抽出硬约束，按区域分组，主动删高风险点。

核心原则：
- 一天一个主区域
- 不是越满越好，是越顺越好
- 替用户删东西，说清楚删了什么、为什么删

详见 [`references/trip-planning.md`](references/trip-planning.md)

### Phase 2：小红书调研

用 OpenCLI 的 CDPBridge 连接 Chrome，直接访问小红书搜索结果页路由，拦截 `search/notes` API，提取前排笔记。

关键经验：**不要模拟输入框**，直接进 `search_result?keyword=` 路由更稳定。

两段式流程：先粗筛搜索结果（10-20 条），再精读最相关的 2-3 条详情页。

详见 [`references/xhs-research.md`](references/xhs-research.md)

### Phase 3：生成地图页面

基于 [`assets/template.html`](assets/template.html) 模板填入数据，生成单文件 HTML：

- Leaflet.js 交互地图（无需 API key）
- 按天切换的时间轴卡片
- 每个地点：Google Maps 导航链接、小红书笔记链接、预约按钮
- 支付方式标签（信用卡 / 支付宝 / 交通卡 / 现金）
- 默认 Apple 设计系统，可通过 [awesome-design-md](https://github.com/VoltAgent/awesome-design-md) 切换风格

## 依赖

| 工具 | 用途 | 安装 |
|------|------|------|
| [OpenCLI](https://github.com/jackwener/OpenCLI) | Chrome CDP 桥接，小红书调研 | `npm install -g @jackwener/opencli` |
| Chrome / Chromium | 浏览器 + 远程调试 | — |
| [Leaflet.js](https://leafletjs.com) | 地图渲染 | CDN 引入，无需安装 |
| [gh CLI](https://cli.github.com) | GitHub 仓库创建（可选） | `brew install gh` |

## 目录结构

```
trip-map-builder/
├── SKILL.md                  # 技能入口，三阶段流程
├── README.md                 # 本文件
├── assets/
│   └── template.html         # 可复用 HTML 地图模板
└── references/
    ├── trip-planning.md      # 行程规划方法论
    └── xhs-research.md       # 小红书调研 + OpenCLI 安装
```

## 许可

MIT
