# 大转盘 — 技术规范文档

> 版本：v1.0  
> 更新日期：2024-06-15

---

## 1. 技术选型

| 项 | 选择 | 理由 |
|------|------|------|
| 文件结构 | 单 HTML 文件 | 用户可直接双击打开，无需任何工具 |
| 样式 | 内嵌 CSS | 无外部依赖，加载最快 |
| 脚本 | 原生 JavaScript (ES6+) | 零依赖，现代浏览器全部支持 |
| 转盘渲染 | HTML5 Canvas | 绘制扇区、文字、动画的唯一可靠方案 |
| 数据存储 | localStorage | 浏览器原生支持，5MB 足够 |
| 图片处理 | Canvas API | 客户端压缩，不依赖后端 |

## 2. 浏览器兼容性

- Chrome 90+
- Edge 90+
- Firefox 90+
- Safari 14+ (iOS 14+)

均支持 ES6、Canvas、localStorage、FileReader API。

## 3. 数据模型

### 3.1 localStorage 结构

**Key**: `luckyWheelData`

```json
{
  "version": 1,
  "currentCategory": "food",
  "categories": {
    "food": {
      "name": "🍜 吃的",
      "options": ["火锅", "麻辣烫", "炸鸡", "寿司", "披萨", "面条", "盖饭", "饺子"]
    },
    "drink": {
      "name": "🧋 喝的",
      "options": ["奶茶", "咖啡", "可乐", "果汁", "柠檬茶", "酸奶", "牛奶"]
    }
  },
  "history": [
    {
      "id": 1718448000000,
      "category": "food",
      "result": "火锅",
      "date": "2024-06-15",
      "photo": null
    }
  ]
}
```

### 3.2 字段说明

| 字段 | 类型 | 说明 |
|------|------|------|
| `version` | number | 数据结构版本，用于未来升级 |
| `currentCategory` | string | 当前选中的分类 key（"food" \| "drink"） |
| `categories.<key>.name` | string | 分类显示名称 |
| `categories.<key>.options` | string[] | 选项列表 |
| `history[].id` | number | 时间戳作为唯一 ID |
| `history[].category` | string | 所属分类 |
| `history[].result` | string | 中选选项名 |
| `history[].date` | string | 日期 YYYY-MM-DD |
| `history[].photo` | string\|null | 压缩后的 Base64 图片，无则为 null |

### 3.3 存储容量估算

- 纯数据（选项 + 历史文字）：约 2-5KB
- 单张压缩照片：约 30-50KB
- 按 50 张照片计算：约 2.5MB
- 总计在 5MB 限制内安全

## 4. 模块架构

```
index.html
├── HTML 结构
│   ├── header（标题 + 分类标签）
│   ├── wheel-area（Canvas 转盘）
│   ├── spin-btn（旋转按钮）
│   ├── edit-panel（选项编辑面板）
│   └── history-panel（历史记录面板）
├── CSS 样式
│   ├── 全局变量（颜色、圆角、字号）
│   ├── 布局（flexbox，移动优先）
│   ├── 组件样式
│   └── 响应式媒体查询
└── JavaScript
    ├── StorageManager（数据读写）
    ├── WheelRenderer（Canvas 绘制 + 动画）
    ├── UIManager（界面切换、事件绑定）
    └── App（入口，初始化协调）
```

## 5. Canvas 转盘实现要点

### 5.1 绘制扇区
- 每个选项一个扇区，角度 = 360° / 选项数量
- 扇区颜色从预设色盘中循环取用（8-10 种颜色）
- 文字沿扇区中线径向绘制

### 5.2 旋转动画
- 使用 `requestAnimationFrame` 实现
- 缓出函数：`easeOutCubic`（`t => 1 - Math.pow(1 - t, 3)`）
- 动画时长：3-4 秒
- 旋转总角度 = 随机基础角度 + 多圈旋转（保证视觉效果）
- 旋转过程中禁用按钮

### 5.3 指针
- 固定在 Canvas 上方（12 点钟方向）
- 用 CSS 三角形或 Canvas 绘制

## 6. 图片压缩策略

```js
// 压缩流程
File 输入 → FileReader → Image → Canvas(300px) → toDataURL('image/jpeg', 0.6) → Base64 存储
```

- 最大宽度：300px（保持宽高比）
- 格式：JPEG
- 质量：0.6
- 预估单张大小：30-50KB

## 7. 安全考量

- 所有数据存在用户本地，不上传任何服务器
- 无 XSS 风险：不使用 innerHTML，只用 textContent
- 图片通过 Canvas 重新渲染，剥离 EXIF 元数据（隐私保护）
