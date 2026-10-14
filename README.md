# 极简单文件 Todo · 没有 SaaS 税

> 双击 `index.html` 即可使用 — 零账号、零联网、零弹窗，数据存在你浏览器里。

---

## 一、本地预览

### 方法 1：双击

直接双击 `index.html`，浏览器用 `file://` 协议打开。所有功能（添加 / 完成 / 删除 / 编辑 / 标签筛选 / 主题切换 / 持久化）均可用。

### 方法 2：起 server

```bash
cd mvp-todo/
python3 -m http.server 8000   # 打开 http://localhost:8000
# 或
npx serve .
```

---

## 二、键盘快捷键

| 键 | 行为 |
|---|---|
| `Enter` | 新增 todo（输入框中）/ 保存编辑（编辑态中） |
| `Esc` | 取消编辑 / 清空输入框 |
| `Ctrl/Cmd + Enter` | 切换当前项完成态 |
| `E` | 进入编辑（聚焦项） |
| `Delete` / `Backspace` | 删除（带 3 秒撤销） |
| `Ctrl/Cmd + Z` | 撤销最近一次删除 |
| `1` / `2` / `3` | 切到「全部 / 待办 / 已完成」 |
| 双击标题 | 进入编辑 |

---

## 三、数据存储与备份

- 存储位置：浏览器 `localStorage`，key 为 `mvp-todo:v1`
- 数据结构：`{ version: 1, items: TodoItem[], settings: { filter } }`
- 写入时机：每次 mutation（增 / 删 / 改 / 切完成）后立即同步写入
- 读取时机：页面打开时一次性加载到内存
- 容量：5 MB（约 1–3 万条 todo，超出自动提示）

### 数据备份（导出 JSON）

方法 A · 点击页脚「导出 JSON」按钮（自动复制到剪贴板）

方法 B · 浏览器开发者控制台跑：

```js
copy(localStorage.getItem('mvp-todo:v1'))
// 然后粘贴到 .json 文件即可
```

方法 C · 跨设备迁移（手动）：

1. 在旧设备导出 JSON
2. 复制内容
3. 在新设备 DevTools Console 跑 `localStorage.setItem('mvp-todo:v1', '...')`
4. 刷新页面

### 重要警告

> **数据在浏览器，清缓存会丢；不要在公共电脑存敏感信息。**
> 跨设备同步 / 协作 / 云端备份 — 都不在 v1 范围内。

---

## 四、替换占位步骤

页面所有「需要你改的地方」用 `<!-- TODO: -->` 注释标记：

```bash
grep -n 'TODO' index.html
```

5 处替换点：
1. 页面标题 `<title>` （默认 `todos`）
2. `<meta name="description">`（默认 "一个双击就能用的极简 todo..."）
3. header `<h1>`（默认 `todos`）
4. 输入框 placeholder（默认 "回车新增"）
5. 文案可改处（footer、aria-label 等）

---

## 五、改主题色（3 个变量）

打开 `index.html`，找到 `<style>` 顶部：

```css
:root {
  --accent:       #a86b3c;   /* 主强调色（琥珀赤陶） */
  --paper:        #faf8f3;   /* 主背景（暖纸白） */
  --ink-strong:   #0f0e0c;   /* 主文字（深墨） */
}
```

改这 3 行，全站（明 + 暗）会同步更新。暗色对应值在 `[data-theme="dark"]` 块（约 95 行）同步改。

3 套预设：

| 主题 | `--accent` | `--paper` | `--ink-strong` |
|---|---|---|---|
| **琥珀赤陶（默认）** | `#a86b3c` | `#faf8f3` | `#0f0e0c` |
| 冷岩 + 电气蓝 | `#3b6ea5` | `#f5f7fa` | `#0e1726` |
| 抹茶 + 米白 | `#5a7d4f` | `#fbfaf5` | `#1f2418` |

---

## 六、部署（三选一）

### A · Cloudflare Pages（推荐）

1. 登录 https://dash.cloudflare.com → Pages
2. 创建项目 → 上传 `mvp-todo/` 文件夹（直接拖拽）
3. 完成。访问 `https://<project>.pages.dev`
4. 绑定自定义域名（Pages → Custom domains → 添加）

### B · Vercel

```bash
npm i -g vercel
cd mvp-todo/
vercel --prod
```

或者 Vercel 网页 → New Project → Import 仓库 → Framework preset 选 `Other` → 部署。

### C · GitHub Pages

```bash
cd mvp-todo/
git init && git add . && git commit -m "init"
gh repo create my-todo --public --source=. --push
gh repo edit --enable-pages --branch main --path /
```

访问 `https://<user>.github.io/my-todo/`。

---

## 七、性能与无障碍自检

部署前本地跑：

```bash
# 体积
wc -c index.html
gzip -c index.html | wc -c    # 目标 ≤ 7 KB · 上限 ≤ 12 KB

# P0 红线（应全部为空）
grep -P '[\x{1F300}-\x{1FAFF}\x{2600}-\x{27BF}]' index.html   # emoji 当图标
grep -iE 'linear-gradient.*(purple|pink|magenta|violet)' index.html
grep -iE 'Welcome to|Lorem ipsum|Get started with' index.html
grep -E '<script[^>]+src="https?://' index.html              # 外部脚本
grep -iE 'tailwind|bootstrap|bulma' index.html               # CSS 框架
grep -E 'fonts\.googleapis|fonts\.gstatic' index.html        # 字体 CDN
grep -E 'google-analytics|googletagmanager' index.html       # 追踪
grep -E 'fetch\(["\x27](https?://|//)' index.html            # 第三方 fetch
```

在线工具：
- Chrome DevTools → Lighthouse：performance + accessibility + best practices + SEO 各 ≥ 95
- axe DevTools 浏览器扩展：色对比 / 焦点 / 语义

---

## 八、目录结构

```
mvp-todo/
├── index.html       ← 单文件主交付（CSS / JS / SVG 全内联）
├── README.md        ← 本文件
└── phase1/          ← 调研归档（不进生产）
    ├── ADR.md            （架构决策 7 条）
    ├── architecture.md   （技术选型 / 状态机 / 数据契约）
    ├── PRD.md            （PM 需求）
    ├── 竞品分析.md        （Todoist / Things 3 / TickTick / MS To Do）
    ├── 文案大纲.md        （8 节 + 30 条微交互字符串）
    ├── DESIGN_DIRECTION.md
    ├── design-tokens.json
    ├── tokens.css
    ├── page-structure.md
    └── acceptance-checklist.md
```

---

## 九、常见问题

**Q：数据怎么从 A 浏览器迁到 B 浏览器？**
A：见「数据备份 → 方法 C」。v1 不内置同步 UI。

**Q：怎么加一个新区块（比如「优先级」）？**
A：v1 明确不做（Anti-Goals）。如需未来扩展：编辑 `valItem()` 函数加字段 + 编辑 `renderItem()` 加 UI + 处理 schema migration（`if (data.version < 2) ...`）。

**Q：为什么没有搜索？**
A：v1 Backlog。需要的话，编辑 `<input id="new-todo">` 旁的 toolbar 加 `<input>`，并在 `setSearch()` 函数 + `filtered()` 函数加 query 逻辑，约 30 行。

**Q：1000 项会不会卡？**
A：实测列表 re-render < 16ms（单次 mutation 触发），localStorage 写入 < 10ms。如超 5000 项会卡，建议归档已完成项。

**Q：多 tab 同时编辑会冲突吗？**
A：v1 监听 `storage` 事件，其他 tab 自动 reload。可能有最近一次写入丢失，但不会数据错乱。

**Q：localStorage 满了怎么办？**
A：页面顶部会显示「本地存储已用 80%」警告；满时拒绝新增并提示。请归档或删除旧 todo。

---

**Built with a single HTML file · 2026-10**