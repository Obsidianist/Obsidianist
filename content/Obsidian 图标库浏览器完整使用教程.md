# Obsidian 图标库浏览器完整使用教程

> 本教程适用于 **Obsidian Icon Browser**——一款基于 DataviewJS 构建的交互式图标浏览工具，可帮助你快速搜索、预览并复制 Obsidian 内置的全部图标名称。

---

## 目录

1. [什么是 Obsidian 图标库浏览器](Obsidian%20图标库浏览器完整使用教程.md#1.%20什么是%20Obsidian%20图标库浏览器)
2. [前提条件与必要插件](Obsidian%20图标库浏览器完整使用教程.md#2.%20前提条件与必要插件)
3. [安装与配置步骤](Obsidian%20图标库浏览器完整使用教程.md#3.%20安装与配置步骤)
4. [创建图标库浏览器笔记](Obsidian%20图标库浏览器完整使用教程.md#4.%20创建图标库浏览器笔记)
5. [界面功能详解](Obsidian%20图标库浏览器完整使用教程.md#5.%20界面功能详解)
6. [图标名称格式说明](Obsidian%20图标库浏览器完整使用教程.md#6.%20图标名称格式说明)
7. [图标名称的实际应用场景](Obsidian%20图标库浏览器完整使用教程.md#7.%20图标名称的实际应用场景)
8. [常见问题排查](Obsidian%20图标库浏览器完整使用教程.md#8.%20常见问题排查)

---

## 1. 什么是 Obsidian 图标库浏览器

Obsidian 内置了大量来自 [Lucide](https://lucide.dev/) 图标集的 SVG 图标，这些图标被广泛用于：

- 标注块（Callouts）的图标
- Iconize、Iconic 等插件为文件/文件夹设置图标
- 其他社区插件中需要填写图标名称的配置项

然而 Obsidian 官方并未提供可视化的图标浏览界面，导致用户每次需要图标名称时都要去翻文档或猜测。**图标库浏览器**正是为了解决这个问题而生——它通过 DataviewJS 在笔记内部渲染出一个功能完整的图标浏览器，支持搜索、分类、尺寸切换、一键复制等多种操作，完全不需要离开 Obsidian。

---

## 2. 前提条件与必要插件

在使用本小工具之前，需要确认以下环境已就绪。

### 2.1 Obsidian 版本要求

- 建议使用 **Obsidian 1.0.0 及以上版本**
- 工具调用了 `obsidian.getIconIds()` 和 `obsidian.setIcon()` 这两个 Obsidian 原生 API，较旧版本可能不支持

### 2.2 必须安装的插件：Dataview

本工具**唯一的依赖插件**是 **[Dataview](https://github.com/blacksmithgu/obsidian-dataview)**。

Dataview 是 Obsidian 社区最受欢迎的插件之一，它允许你把 Vault 当作数据库来查询，并通过 DataviewJS 代码块在笔记中运行任意 JavaScript 逻辑、动态渲染 HTML 界面。

> ⚠️ **注意**：普通的 Dataview 查询语言（DQL）无法实现本工具的功能，必须启用 **JavaScript API（DataviewJS）**。

### 2.3 无需其他插件

本工具不依赖 Templater、CustomJS、MetaBind 或任何其他插件，代码是完全自包含的。

---

## 3. 安装与配置步骤

### 第一步：安装 Dataview 插件

1. 打开 Obsidian，点击左下角的**设置齿轮图标**（⚙️）
2. 在左侧菜单中选择 **第三方插件**（Community plugins）
3. 如果提示 " 受限模式已开启 "，点击**关闭受限模式**按钮（此操作允许安装社区插件）
4. 点击**浏览**按钮，打开社区插件市场
5. 在搜索框中输入 `Dataview`
6. 找到由 **blacksmithgu** 开发的 Dataview 插件，点击**安装**
7. 安装完成后，点击**启用**

### 第二步：启用 DataviewJS

安装并启用 Dataview 后，还必须开启其 JavaScript 功能：

1. 返回**设置** → 在左侧插件列表中找到并点击 **Dataview**
2. 找到 **"Enable JavaScript Queries"**（启用 JavaScript 查询）选项
3. 将其开关切换为**开启**（蓝色/高亮状态）

> 💡 如果该选项已经是开启状态，跳过此步骤即可。

配置完成后，Dataview 的设置界面关键项如下所示：

|设置项|要求状态|
|---|---|
|Enable Dataview|✅ 开启|
|Enable JavaScript Queries|✅ 开启（**必须**）|
|Enable Inline JavaScript Queries|可选|
|Automatic View Refreshing|建议开启|

---

## 4. 创建图标库浏览器笔记

### 第一步：新建一个笔记

在你的 Vault 中任意位置新建一个 Markdown 笔记，例如命名为 `Obsidian图标库浏览器.md`，或放入工具箱文件夹中，如 `Tools/图标库浏览器.md`。

### 第二步：粘贴代码

将 `Obsidian图标库浏览器完整代码` 的代码（即文件中的 ` ```dataviewjs … ``` ` 代码块，包括开头的三个反引号和 `dataviewjs` 标识符）完整粘贴到笔记中。

> ⚠️ **完整代码获取方法**：
> 1. 为提升阅读体验，将 `Obsidian图标库浏览器完整代码` 放在本文最后的附录当中。
> 2. 关注本微信公众号：**Obsidianist**，回复关键词：**图标库浏览器**，后台即会自动回复完整代码 Markdown 文件下载地址，直接将该 Markdown 文件复制到 Obsidian 当中即可使用。

> ⚠️ **务必注意**：代码块的语言标识符必须是 `dataviewjs`（全小写），而不是 `javascript` 或 `js`，否则 Dataview 将无法识别并执行代码。

### 第三步：切换到阅读/预览模式

粘贴完成后，通过以下任意方式切换到**阅读视图**（Reading View）：

- 点击右上角的**书本图标**
- 使用快捷键 `Ctrl+E`（Windows/Linux）或 `Cmd+E`（macOS）

切换成功后，DataviewJS 代码块会被自动执行，图标库浏览器界面随即渲染出来。

> 💡 **提示**：如果你希望每次打开这个笔记都直接进入阅读视图，可以在笔记的 YAML Frontmatter 中添加如下配置：
>
> ```yaml
> ---
> cssclasses:
> ---
> ```
>
> 或者通过 Obsidian 设置中的 " 默认编辑模式 " 统一调整。

---

## 5. 界面功能详解

图标库浏览器的界面分为三个区域：**顶部工具栏**、**图标网格区**、**最近复制面板**。

---

### 5.1 工具栏第一行：搜索栏

搜索栏是最常用的功能区域，位于整个工具栏的最顶部。

**搜索图标**

- 在搜索框中输入关键词，图标网格会**实时过滤**，只显示名称中包含该关键词的图标
- 搜索不区分大小写，例如输入 `arrow` 或 `Arrow` 效果相同
- 搜索框右侧实时显示**匹配数量统计**，格式为 " 找到 X / 总数 "

**快捷键提示**

- 搜索框右侧显示 `ESC` 快捷键提示，按下 **Esc 键**即可一键清空搜索框并重置图标列表
- 搜索内容不为空时，搜索框末尾会出现一个 **×（清除按钮）**，点击同样可以清空

---

### 5.2 工具栏第二行：分类标签（Tabs）

分类标签用于快速筛选图标来源：

|标签|含义|
|---|---|
|**全部**|显示所有可用图标（默认状态）|
|**Lucide**|仅显示以 `lucide-` 开头的内置图标（数量最多，覆盖最广）|
|**其他**|显示非 Lucide 的图标，通常是 Obsidian 自定义或插件注册的图标|

点击对应标签后，该标签会高亮显示，图标网格同步更新。

---

### 5.3 工具栏第三行：控件区

控件区包含多组操作按钮，从左到右依次为：

#### 尺寸切换（S / M / L）

控制图标网格中每个图标的显示大小：

|档位|图标尺寸|图标格子尺寸|适用场景|
|---|---|---|---|
|**S**（小）|15×15 px|32×32 px|浏览大量图标，快速扫描|
|**M**（中）|21×21 px|52×52 px|默认尺寸，兼顾浏览与辨识|
|**L**（大）|30×30 px|76×76 px|仔细查看单个图标细节|

#### 深色背景（暗/亮）

- 默认开启**深色背景**（`#1e1e2e`），图标颜色为浅蓝白色，对比度高，便于辨别图标轮廓
- 点击可切换为浅色背景，图标颜色随主题变化

#### 标签显示（名称显隐）

- 开启后，M 档和 L 档的每个图标下方会显示**简短的图标名称**（Lucide 图标会自动去除 `lucide-` 前缀）
- S 档由于空间太小，标签始终不显示

#### 复制格式（Format）

点击此下拉框，可选择点击图标时复制到剪贴板的**文字格式**：

|格式选项|复制内容示例|适用场景|
|---|---|---|
|**short**（简短格式）|`home`|用于 Iconize 等插件的图标名称字段|
|**full**（完整格式）|`lucide-home`|用于需要完整前缀的 API 调用场景|
|**lucide:XXX**|`lucide:home`|某些插件使用冒号分隔格式|

> 💡 **建议**：在使用图标名称之前，先确认目标插件要求哪种格式，再切换对应选项。

#### 排序方式（Sort）

点击排序下拉框，可选择图标的显示顺序：

|排序方式|说明|
|---|---|
|**A → Z**|按图标名称字母正序排列（默认）|
|**Z → A**|按图标名称字母倒序排列|
|**复制次数**|将你复制最多次的图标优先显示在前面，方便快速访问常用图标|

#### 随机图标（🎲 随机）

点击此按钮会随机高亮并定位到一个图标，同时自动将其复制到剪贴板。常用于探索不熟悉的图标，或寻找灵感。

---

### 5.4 图标网格区

图标网格是浏览器的核心展示区域。

**交互操作**

- **鼠标悬停**：图标放大 1.1 倍，并显示 tooltip（鼠标停留 60ms 后），tooltip 内容为完整的图标 ID
- **点击图标**：按照当前选中的 " 复制格式 "，将图标名称复制到系统剪贴板，同时触发动画效果（图标闪烁并弹回）
- **复制角标**：每个图标右上角有一个小角标，显示该图标**在本次会话中被复制的次数**（首次复制后才出现）
- **高亮边框**：最近复制过的图标会有一圈蓝色/主题色的轮廓高亮，方便找回

**复制反馈**

每次点击图标复制后，屏幕底部中央会弹出一个 **Toast 通知**，显示刚刚复制的图标和复制的文字内容，约 2 秒后自动消失。

---

### 5.5 最近复制面板

图标网格下方是 " 最近复制 " 历史面板，默认隐藏，复制第一个图标后自动出现。

- 面板最多保存**最近 12 个**复制过的图标
- 每个历史项以 " 图标 + 名称 " 的标签形式展示
- 点击任意历史标签可**再次复制**该图标名称
- 点击面板右上角的**清除按钮**可清空历史记录

> ⚠️ **注意**：复制历史和复制计数仅在**当前页面会话**中有效。关闭并重新打开笔记后，记录会重置。如需持久化记录，需结合 Obsidian 存储 API 进行改造（超出本工具当前版本范围）。

---

## 6. 图标名称格式说明

### 6.1 Lucide 图标

Obsidian 内置的绝大多数图标来自 [Lucide 图标集](https://lucide.dev/)。在 Obsidian API 中，这些图标的 ID 带有 `lucide-` 前缀，例如：

|图标名（API ID）|含义|
|---|---|
|`lucide-home`|房子/主页|
|`lucide-search`|搜索|
|`lucide-calendar`|日历|
|`lucide-file-text`|文本文件|
|`lucide-folder`|文件夹|
|`lucide-star`|星形|
|`lucide-check`|勾选|
|`lucide-alert-triangle`|警告三角|

你可以访问 [lucide.dev](https://lucide.dev/icons/) 浏览所有 Lucide 图标的视觉预览。注意：Obsidian 内置的 Lucide 版本不一定是最新版，少数最新图标可能尚未加入。

### 6.2 其他图标

" 其他 " 分类包含 Obsidian 自定义的非 Lucide 图标，以及由已安装的第三方插件通过 `addIcon()` API 注册的图标。这类图标数量较少，名称格式不统一，通常是插件的专属图标。

### 6.3 复制格式对照表

|使用场景|推荐复制格式|示例|
|---|---|---|
|标注块（Callout）图标|`short` 或 `full`|`home` 或 `lucide-home`|
|Iconize 插件|内置 Lucide 图标直接用名称|`home`|
|Iconic 插件|直接用图标名|`home`|
|插件开发 / API|`full`（完整 ID）|`lucide-home`|

---

## 7. 图标名称的实际应用场景

找到图标名称之后，下面介绍最常见的几种使用场景。

### 7.1 在标注块（Callout）中使用图标

Obsidian 的标注块支持通过类型名称自动匹配图标。例如：

```markdown
> [!note] 这是一个笔记
> 内容...

> [!warning] 这是一个警告
> 内容...

> [!tip] 提示
> 内容...
```

Obsidian 会根据标注类型自动选择对应的 Lucide 图标。如果你使用 **Custom CSS** 或支持自定义标注的主题，也可以将图标名称绑定到自定义标注类型中。

### 7.2 配合 Iconize 插件使用

[Iconize](https://github.com/FlorianWoelki/obsidian-iconize) 是一款可以为 Vault 中的文件和文件夹添加图标的插件。

使用步骤：

1. 安装并启用 Iconize 插件
2. 在 Iconize 插件设置中，选择 "Icon Packs" 并下载 **Lucide** 图标包
3. 在文件管理器中，右键点击任意文件或文件夹
4. 选择 "Change icon"，在搜索框中输入你从图标库浏览器中复制的名称
5. 选择对应图标确认即可

### 7.3 配合 Iconic 插件使用

[Iconic](https://github.com/gfxholo/iconic) 是另一款功能更丰富的图标管理插件，支持为标签页、侧边栏、功能区、书签、属性（Properties）、标签（Tags）等位置自定义图标和颜色。

使用步骤：

1. 安装并启用 Iconic 插件
2. 直接点击界面上几乎任何位置的图标，即可弹出图标选择器
3. 在选择器中输入你从图标库浏览器复制的名称，快速定位目标图标

### 7.4 在插件开发中使用

如果你在开发 Obsidian 插件，可以通过以下 API 设置图标：

```javascript
import { setIcon } from "obsidian";

// 在某个 HTML 元素上设置图标
const el = this.containerEl.createDiv();
setIcon(el, "lucide-home"); // 使用完整 ID

// 或者使用简短名称（Obsidian 内部会自动补全 lucide- 前缀）
setIcon(el, "home");
```

---

## 8. 常见问题排查

### 打开笔记后只显示代码，没有界面

**原因**：可能处于**编辑模式**而非阅读模式，或者 Dataview 插件未安装/未启用。

**解决方法**：

1. 确认 Dataview 插件已安装并启用（设置 → 第三方插件）
2. 按 `Ctrl+E` / `Cmd+E` 切换到阅读视图
3. 检查代码块标识符是否为 `dataviewjs`（不是 `js` 或 `javascript`）

---

### 提示 "DataviewJS is disabled"

**原因**：Dataview 的 JavaScript 查询功能未开启。

**解决方法**：

1. 进入 设置 → Dataview 插件设置
2. 将 **"Enable JavaScript Queries"** 切换为开启

---

### 图标网格显示为空

**原因**：`obsidian.getIconIds()` 返回了空数组，通常发生在 Obsidian 版本过旧时。

**解决方法**：

- 将 Obsidian 更新到最新版本（菜单 → 帮助 → 检查更新）

---

### 点击图标后剪贴板没有内容

**原因**：浏览器安全策略限制了 `navigator.clipboard` API 的使用（在某些 Linux 系统上较常见）。

**解决方法**：

- 确保 Obsidian 处于**前台焦点窗口**状态再点击
- 在 Linux 上，尝试使用 Obsidian 的桌面版而非基于 Web 的版本

---

### 切换深色/浅色背景后图标颜色异常

**原因**：部分主题自定义了 CSS 变量，可能与工具内置样式有冲突。

**解决方法**：

- 尝试切换到默认主题测试效果
- 或在工具代码中手动修改 `.ib-grid.dark` 相关的 CSS 颜色变量

---

### 复制历史在重新打开笔记后消失

这是**预期行为**。当前版本的图标库浏览器不持久化存储复制历史，记录仅在当前会话中有效。如有需要，可以利用 Obsidian 的 `localStorage` 或者 `app.saveData()` API 对代码进行扩展。

---

## 附录 1：工具功能速查

|功能|操作方式|
|---|---|
|搜索图标|在搜索框输入关键词|
|清空搜索|按 `Esc` 键，或点击 × 按钮|
|切换分类|点击分类标签（全部 / Lucide / 其他）|
|调整图标大小|点击 S / M / L 按钮|
|切换背景色|点击 " 暗/亮 " 按钮|
|显示/隐藏名称|点击 " 名称 " 按钮|
|切换复制格式|点击 Format 下拉框|
|切换排序方式|点击 Sort 下拉框|
|随机图标|点击 " 随机 " 按钮|
|复制图标名称|直接点击任意图标|
|再次复制历史图标|点击底部历史标签|
|清空历史记录|点击历史面板的 " 清除 " 按钮|

---

## 附录 2：Obsidian 图标库浏览器完整代码

`````
```dataviewjs
const css = [
  ".ib { font-family: var(--font-interface); padding: 2px 0 28px; }",

  ".ib-bar { display:flex; flex-direction:column; gap:7px; margin-bottom:12px; }",

  ".ib-row1 { display:flex; align-items:center; gap:8px; padding:8px 12px; background:var(--background-secondary); border:1px solid var(--background-modifier-border); border-radius:var(--radius-m); }",
  ".ib-ico-prefix { display:flex; align-items:center; flex-shrink:0; color:var(--text-muted); }",
  ".ib-ico-prefix svg { width:15px; height:15px; }",
  ".ib-search { flex:1; padding:3px 4px; border:none; background:transparent; color:var(--text-normal); font-size:var(--font-ui-small); outline:none; min-width:0; }",
  ".ib-search::placeholder { color:var(--text-faint); }",
  ".ib-kbd { padding:1px 5px; border-radius:3px; background:var(--background-modifier-border); color:var(--text-faint); font-size:10px; border:1px solid var(--background-modifier-border); white-space:nowrap; flex-shrink:0; }",
  ".ib-vsep { width:1px; height:14px; background:var(--background-modifier-border); flex-shrink:0; }",
  ".ib-stats { font-size:11px; color:var(--text-muted); white-space:nowrap; flex-shrink:0; }",
  ".ib-stats b { color:var(--interactive-accent); font-weight:600; }",
  ".ib-clear { background:none; border:none; cursor:pointer; color:var(--text-faint); font-size:14px; padding:0 2px; line-height:1; display:none; flex-shrink:0; }",
  ".ib-clear.on { display:block; }",
  ".ib-clear:hover { color:var(--text-normal); }",

  ".ib-row2 { display:flex; align-items:center; gap:6px; flex-wrap:wrap; }",
  ".ib-tabs { display:flex; gap:2px; background:var(--background-secondary); border:1px solid var(--background-modifier-border); border-radius:var(--radius-s); padding:3px; }",
  ".ib-tab { padding:3px 11px; border:none; background:transparent; color:var(--text-muted); font-size:12px; cursor:pointer; border-radius:calc(var(--radius-s) - 1px); transition:all 0.12s; white-space:nowrap; }",
  ".ib-tab:hover { color:var(--text-normal); }",
  ".ib-tab.on { background:var(--interactive-accent); color:var(--text-on-accent); border-color:var(--interactive-accent); }",

  ".ib-row3 { display:flex; align-items:center; flex-wrap:wrap; gap:8px; padding:7px 11px; background:var(--background-secondary); border:1px solid var(--background-modifier-border); border-radius:var(--radius-m); }",
  ".ib-cg { display:flex; align-items:center; gap:3px; }",
  ".ib-cl { font-size:11px; color:var(--text-faint); margin-right:1px; white-space:nowrap; }",
  ".ib-btn { padding:3px 9px; border-radius:var(--radius-s); border:1px solid var(--background-modifier-border); background:var(--background-primary); color:var(--text-muted); cursor:pointer; font-size:11px; transition:all 0.12s; white-space:nowrap; }",
  ".ib-btn:hover { color:var(--text-normal); background:var(--background-modifier-hover); }",
  ".ib-btn.on { background:var(--interactive-accent); color:var(--text-on-accent); border-color:var(--interactive-accent); }",
  ".ib-select { padding:3px 6px; border-radius:var(--radius-s); border:1px solid var(--background-modifier-border); background:var(--background-primary); color:var(--text-normal); font-size:11px; cursor:pointer; outline:none; }",
  ".ib-divider { width:1px; height:18px; background:var(--background-modifier-border); }",
  ".ib-btn.ib-rand:hover { background:var(--color-purple); color:#fff; border-color:var(--color-purple); }",

  ".ib-grid { min-height:80px; transition:background 0.2s; }",
  ".ib-grid.dark { background:#1e1e2e; border-radius:var(--radius-m); }",
  ".ib-item { display:flex; flex-direction:column; align-items:center; justify-content:center; border-radius:var(--radius-s); cursor:pointer; transition:background 0.1s, transform 0.12s; user-select:none; flex-shrink:0; position:relative; }",
  ".ib-item:hover { transform:scale(1.1); z-index:2; }",
  ".ib-item:active { transform:scale(0.93); }",
  ".ib-item:hover { background:var(--background-modifier-hover); }",
  ".ib-grid.dark .ib-item { color:#cdd6f4; }",
  ".ib-grid.dark .ib-item svg { color:#cdd6f4 !important; }",
  ".ib-grid.dark .ib-item:hover { background:#313244; }",
  ".ib-grid.dark .ib-lbl { color:#585b70 !important; }",
  ".ib-item.s { width:32px; height:32px; }",
  ".ib-item.m { width:52px; height:52px; gap:3px; }",
  ".ib-item.l { width:76px; height:76px; gap:5px; }",
  ".ib-item.s svg { width:15px; height:15px; }",
  ".ib-item.m svg { width:21px; height:21px; }",
  ".ib-item.l svg { width:30px; height:30px; }",
  ".ib-lbl { font-size:9px; color:var(--text-muted); max-width:100%; overflow:hidden; text-overflow:ellipsis; white-space:nowrap; text-align:center; line-height:1.3; display:none; padding:0 3px; }",
  ".ib-item.s .ib-lbl { display:none !important; }",
  ".ib-item.m.lbl .ib-lbl { display:block; max-width:50px; }",
  ".ib-item.l.lbl .ib-lbl { display:block; max-width:72px; font-size:10px; }",
  ".ib-item.m.lbl { height:64px; }",
  ".ib-item.l.lbl { height:92px; }",
  ".ib-badge { position:absolute; top:2px; right:2px; background:var(--interactive-accent); color:var(--text-on-accent); font-size:8px; min-width:13px; height:13px; border-radius:7px; display:flex; align-items:center; justify-content:center; padding:0 3px; line-height:1; font-weight:700; display:none; }",
  ".ib-item.has-badge .ib-badge { display:flex; }",
  ".ib-item.highlight { outline:2px solid var(--interactive-accent); outline-offset:1px; }",
  "@keyframes ib-pop { 0%{background:var(--interactive-accent);transform:scale(1.2);} 60%{transform:scale(1.04);} 100%{background:transparent;transform:scale(1);} }",
  ".ib-item.copied { animation:ib-pop 0.42s ease-out forwards; }",

  ".ib-toast { position:fixed; bottom:24px; left:50%; transform:translateX(-50%) translateY(14px); background:var(--background-primary); color:var(--text-normal); border:1px solid var(--background-modifier-border); box-shadow:0 4px 20px rgba(0,0,0,0.18); padding:7px 16px 7px 12px; border-radius:var(--radius-m); font-size:12px; opacity:0; pointer-events:none; transition:opacity 0.16s,transform 0.16s; z-index:9999; white-space:nowrap; display:flex; align-items:center; gap:8px; }",
  ".ib-toast.on { opacity:1; transform:translateX(-50%) translateY(0); }",
  ".ib-toast-icon { display:flex; align-items:center; }",
  ".ib-toast-icon svg { width:15px; height:15px; color:var(--interactive-accent); }",
  ".ib-toast b { color:var(--interactive-accent); }",

  ".ib-empty { width:100%; padding:52px 0; text-align:center; color:var(--text-muted); }",
  ".ib-empty svg { width:30px; height:30px; margin:0 auto 10px; display:block; opacity:0.35; }",
  ".ib-empty p { font-size:13px; margin:0; }",

  ".ib-recent { margin-top:10px; padding:10px 13px; background:var(--background-secondary); border-radius:var(--radius-m); border:1px solid var(--background-modifier-border); }",
  ".ib-rec-head { display:flex; align-items:center; justify-content:space-between; margin-bottom:7px; }",
  ".ib-rec-title { display:flex; align-items:center; gap:5px; font-size:11px; color:var(--text-muted); font-weight:500; letter-spacing:0.05em; text-transform:uppercase; }",
  ".ib-rec-title svg { width:12px; height:12px; }",
  ".ib-rec-clear { font-size:11px; color:var(--text-faint); background:none; border:none; cursor:pointer; padding:0; }",
  ".ib-rec-clear:hover { color:var(--text-muted); }",
  ".ib-rec-list { display:flex; flex-wrap:wrap; gap:5px; }",
  ".ib-tag { display:inline-flex; align-items:center; gap:5px; padding:3px 9px 3px 7px; border-radius:var(--radius-s); background:var(--background-primary); border:1px solid var(--background-modifier-border); font-size:11px; color:var(--text-normal); cursor:pointer; transition:all 0.1s; }",
  ".ib-tag:hover { background:var(--background-modifier-hover); border-color:var(--interactive-accent); }",
  ".ib-tag svg { width:13px; height:13px; flex-shrink:0; }",

  ".dv-sw-wrap { display:flex; flex-direction:column; gap:2px; }",
  ".dv-ml { font-size:11px; color:var(--text-faint); white-space:nowrap; }",
  ".dv-csel-wrap { display:inline-flex; align-items:stretch; position:relative; box-shadow:inset 0 0 0 1px var(--background-modifier-border); border-radius:4px; background:var(--background-primary); flex-shrink:0; min-width:80px; cursor:pointer; user-select:none; transition:box-shadow .12s,background .12s; }",
  ".dv-csel-wrap:hover { background:var(--background-modifier-hover); }",
  ".dv-csel-wrap--open, .dv-csel-wrap:focus { box-shadow:inset 0 0 0 1px var(--interactive-accent); outline:none; }",
  ".dv-csel-trigger { display:flex; align-items:center; justify-content:space-between; padding:4px 7px; gap:4px; width:100%; box-sizing:border-box; font-size:11px; color:var(--text-normal); min-height:26px; }",
  ".dv-csel-text { flex:1; overflow:hidden; text-overflow:ellipsis; white-space:nowrap; line-height:1.4; }",
  ".dv-csel-arrow { font-size:9px; color:var(--text-muted); flex-shrink:0; line-height:1; padding-bottom:1px; }",
  ".dv-dropdown-panel { position:fixed; background:var(--background-primary); border:1px solid var(--background-modifier-border); border-radius:4px; box-shadow:0 4px 16px rgba(0,0,0,.14); z-index:99999; max-height:260px; overflow-y:auto; overflow-x:hidden; box-sizing:border-box; }",
  ".dv-dropdown-option { padding:5px 10px; font-size:11px; color:var(--text-normal); cursor:pointer; white-space:nowrap; line-height:1.5; }",
  ".dv-dropdown-option:hover { background:var(--background-modifier-hover); }",
  ".dv-dropdown-option--active { background:var(--interactive-accent); color:var(--text-on-accent); }"
].join(" ");

dv.container.createEl("style", { text: css });

const root      = dv.container.createDiv("ib");
const allRaw    = Array.from(obsidian.getIconIds()).sort();
const lucideIds = allRaw.filter(function(id){ return id.startsWith("lucide-"); });
const otherIds  = allRaw.filter(function(id){ return !id.startsWith("lucide-"); });

let query     = "";
let category  = "all";
let size      = "m";
let darkBg    = true;
let showLabel = false;
let copyFmt   = "short";
let sortMode  = "az";
let recent    = [];
let copyCount = {};

const _ibPanels = [];

function createCustomSelect(parentEl, options, initValue, onChange, label) {
    const outer = parentEl.createEl("div", { cls: "dv-sw-wrap" });
    if (label) outer.createEl("div", { cls: "dv-ml", text: label });

    const wrap = outer.createEl("div", { cls: "dv-csel-wrap" });
    wrap.setAttribute("tabindex", "0");

    const trigger  = wrap.createEl("div", { cls: "dv-csel-trigger" });
    const trigText = trigger.createEl("span", { cls: "dv-csel-text" });
    trigger.createEl("span", { cls: "dv-csel-arrow", text: "▾" });

    const panel = document.body.createEl("div", { cls: "dv-dropdown-panel" });
    panel.style.display = "none";
    _ibPanels.push(panel);

    let currentValue = initValue;
    let isOpen = false;

    const optEls = options.map(([val, text]) => {
        const item = panel.createEl("div", { cls: "dv-dropdown-option", text });
        item.dataset.value = String(val);
        item.addEventListener("mousedown", e => e.preventDefault());
        item.addEventListener("click", () => {
            currentValue = val;
            refreshDisplay();
            closePanel();
            onChange(val);
        });
        return item;
    });

    function refreshDisplay() {
        const opt = options.find(o => String(o[0]) === String(currentValue));
        trigText.textContent = opt ? opt[1] : String(currentValue);
        optEls.forEach(el =>
            el.classList.toggle("dv-dropdown-option--active", el.dataset.value === String(currentValue))
        );
    }

    function positionPanel() {
        const rect = wrap.getBoundingClientRect();
        panel.style.visibility = "hidden";
        panel.style.display = "block";
        const panelW = Math.max(panel.scrollWidth, rect.width, 80);
        const panelH = Math.min(panel.scrollHeight, 260);
        panel.style.minWidth = panelW + "px";
        let left = rect.left;
        if (left + panelW > window.innerWidth - 6) left = Math.max(6, window.innerWidth - panelW - 6);
        panel.style.left = left + "px";
        const spaceBelow = window.innerHeight - rect.bottom - 4;
        const spaceAbove = rect.top - 4;
        if (spaceBelow >= panelH || spaceBelow >= spaceAbove) {
            panel.style.top    = (rect.bottom + 2) + "px";
            panel.style.bottom = "auto";
        } else {
            panel.style.top    = "auto";
            panel.style.bottom = (window.innerHeight - rect.top + 2) + "px";
        }
        panel.style.visibility = "";
    }

    function openPanel() {
        if (isOpen) return;
        isOpen = true;
        refreshDisplay();
        positionPanel();
        wrap.classList.add("dv-csel-wrap--open");
    }

    function closePanel() {
        if (!isOpen) return;
        isOpen = false;
        panel.style.display = "none";
        wrap.classList.remove("dv-csel-wrap--open");
    }

    trigger.addEventListener("click", () => { isOpen ? closePanel() : openPanel(); });
    document.addEventListener("click", e => {
        if (isOpen && !wrap.contains(e.target) && !panel.contains(e.target)) closePanel();
    });
    document.addEventListener("scroll", e => {
        if (isOpen && !panel.contains(e.target)) closePanel();
    }, true);
    wrap.addEventListener("keydown", e => {
        e.stopPropagation();
        if (e.key === "Enter" || e.key === " ") { e.preventDefault(); isOpen ? closePanel() : openPanel(); }
        if (e.key === "Escape") { closePanel(); wrap.blur(); }
        if (e.key === "ArrowDown") {
            e.preventDefault();
            const idx = options.findIndex(o => String(o[0]) === String(currentValue));
            if (idx < options.length - 1) { currentValue = options[idx + 1][0]; refreshDisplay(); onChange(currentValue); }
        }
        if (e.key === "ArrowUp") {
            e.preventDefault();
            const idx = options.findIndex(o => String(o[0]) === String(currentValue));
            if (idx > 0) { currentValue = options[idx - 1][0]; refreshDisplay(); onChange(currentValue); }
        }
    });

    refreshDisplay();
    return {
        el: outer,
        getValue: () => currentValue,
        setValue: v => { currentValue = v; refreshDisplay(); }
    };
}

const _ibCleanupObs = new MutationObserver(() => {
    if (!document.contains(root)) {
        _ibPanels.forEach(p => { try { p.remove(); } catch {} });
        _ibCleanupObs.disconnect();
    }
});
if (dv.container.parentNode) {
    _ibCleanupObs.observe(dv.container.parentNode, { childList: true });
}

const bar = root.createDiv("ib-bar");

const row1 = bar.createDiv("ib-row1");

const pfx = row1.createSpan("ib-ico-prefix");
obsidian.setIcon(pfx, "search");

const inp = row1.createEl("input", {
    cls: "ib-search",
    attr: { type:"text", placeholder:"搜索图标名称…", spellcheck:"false" }
});

const clrBtn = row1.createEl("button", { cls:"ib-clear", text:"✕" });
row1.createEl("span", { cls:"ib-kbd", text:"ESC 清空" });
row1.createDiv("ib-vsep");
const statsEl = row1.createDiv("ib-stats");

const row2  = bar.createDiv("ib-row2");
const tabs  = row2.createDiv("ib-tabs");

function mkTab(key, label) {
    const t = tabs.createEl("button", {
        cls: "ib-tab" + (key === category ? " on" : ""),
        text: label
    });
    t.onclick = function() {
        category = key;
        tabs.querySelectorAll(".ib-tab").forEach(function(b){ b.removeClass("on"); });
        t.addClass("on");
        renderGrid();
    };
}
mkTab("all",    "全部 " + allRaw.length);
mkTab("lucide", "Lucide " + lucideIds.length);
mkTab("other",  "其他 " + otherIds.length);

const row3 = bar.createDiv("ib-row3");

const sgGrp = row3.createDiv("ib-cg");
sgGrp.createSpan({ cls:"ib-cl", text:"尺寸" });
["s","m","l"].forEach(function(s_) {
    const map = { s:"小", m:"中", l:"大" };
    const b = sgGrp.createEl("button", { cls:"ib-btn"+(s_===size?" on":""), text:map[s_] });
    b.onclick = function() {
        size = s_;
        sgGrp.querySelectorAll(".ib-btn").forEach(function(x){ x.removeClass("on"); });
        b.addClass("on");
        renderGrid();
    };
});

row3.createDiv("ib-divider");

const lblGrp = row3.createDiv("ib-cg");
lblGrp.createSpan({ cls:"ib-cl", text:"名称标签" });
const lblBtn = lblGrp.createEl("button", { cls:"ib-btn", text:"隐藏" });
lblBtn.onclick = function() {
    showLabel = !showLabel;
    lblBtn.textContent = showLabel ? "显示" : "隐藏";
    lblBtn.toggleClass("on", showLabel);
    renderGrid();
};

row3.createDiv("ib-divider");

const darkGrp = row3.createDiv("ib-cg");
darkGrp.createSpan({ cls:"ib-cl", text:"背景" });
const darkBtn = darkGrp.createEl("button", { cls:"ib-btn on", text:"深色" });
darkBtn.onclick = function() {
    darkBg = !darkBg;
    darkBtn.textContent = darkBg ? "深色" : "浅色";
    darkBtn.toggleClass("on", darkBg);
    grid.toggleClass("dark", darkBg);
};

row3.createDiv("ib-divider");

createCustomSelect(row3,
    [["az", "A → Z"], ["za", "Z → A"], ["count", "复制次数↓"]],
    "az",
    v => { sortMode = v; renderGrid(); },
    "排序"
);

row3.createDiv("ib-divider");

createCustomSelect(row3,
    [["full", "完整 ID"], ["short", "短名（去前缀）"], ["call", "setIcon() 调用"]],
    "short",
    v => { copyFmt = v; },
    "复制为"
);

row3.createDiv("ib-divider");

const randGrp = row3.createDiv("ib-cg");
const randBtn = randGrp.createEl("button", { cls:"ib-btn ib-rand", text:"🎲 随机" });
randBtn.onclick = function() {
    const list = allRaw;
    const id = list[Math.floor(Math.random() * list.length)];
    inp.value = id;
    query = id;
    clrBtn.addClass("on");
    renderGrid();
    setTimeout(function() {
        const found = grid.querySelector(".ib-item");
        if (found) {
            found.addClass("highlight");
            setTimeout(function(){ found.removeClass("highlight"); }, 1200);
        }
    }, 50);
};

const grid = root.createDiv("ib-grid");

const recPanel = root.createDiv("ib-recent");
recPanel.style.display = "none";

const recHead  = recPanel.createDiv("ib-rec-head");
const recTitle = recHead.createDiv("ib-rec-title");
const recIco   = recTitle.createSpan();
obsidian.setIcon(recIco, "clock");
recTitle.createSpan({ text:"最近复制" });

const recClearBtn = recHead.createEl("button", { cls:"ib-rec-clear", text:"清空" });
recClearBtn.onclick = function() { recent = []; renderRecent(); };

const recList = recPanel.createDiv("ib-rec-list");

const toast = document.body.createDiv("ib-toast");
let toastTmr = null;

function showToast(id, text) {
    toast.empty();
    const icoSpan = toast.createSpan("ib-toast-icon");
    obsidian.setIcon(icoSpan, id);
    toast.appendText("已复制 ");
    toast.createEl("b", { text: text });
    toast.addClass("on");
    clearTimeout(toastTmr);
    toastTmr = setTimeout(function(){ toast.removeClass("on"); }, 2000);
}

function fmtText(id) {
    if (copyFmt === "short" && id.startsWith("lucide-")) return id.slice(7);
    if (copyFmt === "call")  return 'setIcon(el, "' + id + '")';
    return id;
}

function doCopy(id, el) {
    const text = fmtText(id);
    navigator.clipboard.writeText(text).then(function() {
        showToast(id, text);
        el.addClass("copied");
        setTimeout(function(){ el.removeClass("copied"); }, 460);
        copyCount[id] = (copyCount[id] || 0) + 1;
        var badge = el.querySelector(".ib-badge");
        if (badge) {
            badge.textContent = String(copyCount[id]);
            el.addClass("has-badge");
        }
        recent = [id].concat(recent.filter(function(x){ return x !== id; })).slice(0, 12);
        renderRecent();
    });
}

function renderRecent() {
    recList.empty();
    if (recent.length === 0) { recPanel.style.display = "none"; return; }
    recPanel.style.display = "";
    recent.forEach(function(id) {
        const tag = recList.createDiv("ib-tag");
        const ico = tag.createSpan();
        obsidian.setIcon(ico, id);
        tag.createSpan({ text: fmtText(id) });
        tag.onclick = function() {
            const text = fmtText(id);
            navigator.clipboard.writeText(text);
            showToast(id, text);
        };
        obsidian.setTooltip(tag, "点击再次复制：" + fmtText(id), { delay: 150 });
    });
}

function renderGrid() {
    grid.empty();

    const szCfg = { s:{w:32,gap:3}, m:{w:52,gap:4}, l:{w:76,gap:6} };
    const sz = szCfg[size];
    grid.style.display             = "grid";
    grid.style.gridTemplateColumns = "repeat(auto-fill, " + sz.w + "px)";
    grid.style.justifyContent      = "space-evenly";
    grid.style.columnGap           = "0";
    grid.style.rowGap              = sz.gap + "px";
    grid.style.paddingTop          = sz.gap + "px";
    grid.style.paddingBottom       = sz.gap + "px";
    grid.style.paddingLeft         = "0";
    grid.style.paddingRight        = "0";

    let src;
    if      (category === "lucide") src = lucideIds.slice();
    else if (category === "other")  src = otherIds.slice();
    else                            src = allRaw.slice();

    const q = query.trim().toLowerCase();
    let filtered = q ? src.filter(function(id){ return id.toLowerCase().includes(q); }) : src;

    if (sortMode === "za") {
        filtered = filtered.slice().reverse();
    } else if (sortMode === "count") {
        filtered = filtered.slice().sort(function(a, b) {
            return (copyCount[b] || 0) - (copyCount[a] || 0);
        });
    }

    statsEl.empty();
    if (q) {
        statsEl.appendText("找到 ");
        statsEl.createEl("b", { text: String(filtered.length) });
        statsEl.appendText(" / " + src.length);
    } else {
        statsEl.createEl("b", { text: String(filtered.length) });
        statsEl.appendText(" 个图标");
    }

    if (filtered.length === 0) {
        const emp = grid.createDiv("ib-empty");
        const eico = emp.createDiv();
        obsidian.setIcon(eico, "search-x");
        emp.createEl("p", { text: "没有找到包含 [" + query + "] 的图标" });
        return;
    }

    const szCls  = size;
    const lblCls = showLabel ? " lbl" : "";
    const recentSet = new Set(recent);

    filtered.forEach(function(id) {
        const item = grid.createDiv("ib-item " + szCls + lblCls);
        if (recentSet.has(id)) item.addClass("highlight");
        const iSpan = item.createSpan();
        obsidian.setIcon(iSpan, id);
        const displayName = (id.startsWith("lucide-")) ? id.slice(7) : id;
        item.createSpan({ cls:"ib-lbl", text: displayName });
        const badge = item.createSpan({ cls:"ib-badge" });
        if (copyCount[id]) {
            badge.textContent = String(copyCount[id]);
            item.addClass("has-badge");
        }
        obsidian.setTooltip(item, id + (copyCount[id] ? "  （已复制 " + copyCount[id] + " 次）" : ""), { delay: 60 });
        item.onclick = function() { doCopy(id, item); };
    });

    if (darkBg) grid.addClass("dark");
}

inp.addEventListener("keydown", function(e) {
    if (e.key === "Escape") {
        inp.value = "";
        query = "";
        clrBtn.removeClass("on");
        renderGrid();
    }
});

inp.addEventListener("input", function() {
    query = inp.value;
    clrBtn.toggleClass("on", query.length > 0);
    renderGrid();
});

clrBtn.onclick = function() {
    inp.value = "";
    query = "";
    clrBtn.removeClass("on");
    inp.focus();
    renderGrid();
};

renderGrid();
```
`````

---

> ⚠️ **完整代码获取方法**：
> 1. 为提升阅读体验，将 `Obsidian图标库浏览器完整代码` 放在本文最后的附录当中。
> 2. 关注本微信公众号：**Obsidianist**，回复关键词：**图标库浏览器**，后台即会自动回复完整代码 Markdown 文件下载地址，直接将该 Markdown 文件复制到 Obsidian 当中即可使用。
