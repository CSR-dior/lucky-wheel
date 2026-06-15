# 大转盘（Lucky Spin Wheel）— 项目指引

## 项目简介

一个单文件 HTML 大转盘应用，帮助用户随机决定"吃什么/喝什么"。
**目标用户**：不懂电脑的小白，追求极简操作体验。
**打开方式**：双击 `index.html` 即可在浏览器中使用。

---

## 项目文档路径

| 文档 | 路径 | 用途 |
|------|------|------|
| 需求规格 | [docs/requirements.md](docs/requirements.md) | 功能需求、用户场景、优先级 |
| 技术规范 | [docs/technical-spec.md](docs/technical-spec.md) | 数据模型、存储策略、模块架构 |
| 设计规范 | [docs/design-spec.md](docs/design-spec.md) | 配色、排版、布局、交互规范 |
| 执行计划 | [docs/execution-plan.md](docs/execution-plan.md) | 分阶段执行步骤、验收标准 |
| 开发日志 | [dev/journal.md](dev/journal.md) | 每日开发记录 |

---

## 核心约束（必须遵守）

1. **单文件**：所有 HTML/CSS/JS 在 `index.html` 一个文件内
2. **零依赖**：不使用任何第三方库或框架
3. **离线可用**：所有数据存 localStorage，无需网络
4. **移动优先**：设计以手机屏幕为准，最大宽度 480px
5. **只使用原生 API**：Canvas、localStorage、FileReader、requestAnimationFrame

---

## 开发工作流

### 开始任何开发任务前
1. 阅读 [docs/execution-plan.md](docs/execution-plan.md) 确认当前阶段
2. 阅读 [docs/design-spec.md](docs/design-spec.md) 确认配色和布局规范
3. 阅读 [docs/technical-spec.md](docs/technical-spec.md) 确认数据模型

### 完成任务后
1. 对照验收标准检查
2. 在 [dev/journal.md](dev/journal.md) 记录：
   - 日期
   - 完成事项
   - 遇到的问题
   - 下一步计划

### 修改规范文档时
- 更新文档末尾的「更新日期」
- 如有结构变更，同步更新本文件的路径引用

---

## 关键文件

### `index.html`
唯一的生产文件。结构：
```
<style>   ← 所有 CSS，优先用 CSS 变量 </style>
<body>    ← HTML 结构 </body>
<script>  ← 所有 JS，分 4 个模块 </script>
```

### JavaScript 模块划分
- `StorageManager`：数据读写（localStorage）
- `WheelRenderer`：Canvas 绘制 + 旋转动画
- `UIManager`：界面切换、DOM 操作、事件绑定
- `App`：入口，初始化协调以上模块

---

## 禁止事项

- ❌ 不要引入 npm / webpack / CDN 等任何外部资源
- ❌ 不要使用 innerHTML 插入用户数据（安全）
- ❌ 不要使用 `document.write()`
- ❌ 不要修改 localStorage key 名称（会导致数据丢失）
- ❌ 不要在转盘动画结束前允许再次点击
