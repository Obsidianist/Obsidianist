---
tags:
  - Obsidian/第三方插件/Templater/任务列表
---

# Obsidian 任务列表模板完整使用教程

本教程适用于 **任务列表模板**——一款基于 Templater 插件的 Obsidian 模板文件，可帮助你通过可视化选择界面，在笔记中快速插入带有语义标记的任务项，涵盖从普通待办到星标、位置、引用、紧急等二十余种任务类型，彻底告别手动敲符号的繁琐。

---

## 目录

1. [什么是任务列表模板](#1.%20什么是任务列表模板)
2. [适用场景与核心价值](#2.%20适用场景与核心价值)
3. [前提条件与必要插件](#3.%20前提条件与必要插件)
4. [模板文件安装方式](#4.%20模板文件安装方式)
5. [使用流程：从触发到插入](#5.%20使用流程：从触发到插入)
6. [任务类型完整列表说明](#6.%20任务类型完整列表说明)
7. [弹窗交互细节](#7.%20弹窗交互细节)
8. [输出格式说明](#8.%20输出格式说明)
9. [常见问题排查](#9.%20常见问题排查)
10. [进阶用法与个性化调整](#10.%20进阶用法与个性化调整)
11. [与其他插件的联动方式](#11.%20与其他插件的联动方式)
12. [适用版本与平台说明](#12.%20适用版本与平台说明)

---

## 1. 什么是任务列表模板

Obsidian 的任务标记语法并不复杂：一个 `- [ ]` 就是未勾选任务，一个 `- [x]` 就是已完成。但当你的工作流开始引入更丰富的任务语义时——哪些是重要事项，哪些已取消，哪些进行中，哪些是想法或书签——逐一手动输入 `- [!]`、`- [/]`、`- [I]` 这类符号就变得相当低效，更别提需要时刻记忆每个符号对应的含义。

**插入 - 任务列表模板** 正是为解决这个问题而设计的。它是一个基于 Templater 插件的模板文件，触发后会弹出一个原生风格的对话框，完成以下工作：

1. 以可视化下拉列表呈现全部任务类型，每一项都附带实时渲染的任务复选框和双语标签，让你一眼就能看出每种类型插入后的实际效果
2. 提供多行文本输入框，支持粘贴或输入任意内容作为任务文字，也可以留空
3. 点击确认（或按 `Ctrl+Enter`）后，立即在笔记光标处插入格式正确的任务项

整个过程不需要记忆任何符号，也不需要离开当前笔记，从触发到完成插入通常只需三到五秒。

---

## 2. 适用场景与核心价值

### 2.1 典型应用场景

**场景一：GTD 任务管理工作流** 在日记、项目笔记或每周回顾中快速插入不同状态的任务项。使用「重新安排（>）」标记延期事项，使用「进行中（/）」标记当前推进中的任务，使用「已取消（-）」代替直接删除以保留决策记录，配合 Tasks 或 Dataview 插件实现跨笔记任务聚合。

**场景二：读书与研究笔记** 在摘录和笔记中使用「引用（"）」标记原文摘抄，「想法（I）」标记由阅读触发的新灵感，「关键（k）」标记核心论点，「书签（b）」标记需要进一步查阅的内容，让原本平铺直叙的笔记拥有清晰的语义层次。

**场景三：会议记录与决策日志** 在会议笔记中使用「重要（!）」标记会议决策，「问题（?）」标记待确认事项，「优点（p）」与「缺点（c）」对比记录某一方案的利弊，「胜利（w）」标记项目里程碑或好消息。

**场景四：个人财务与资源追踪** 使用「金额（S）」标记与费用相关的任务项（如报销、账单、预算），「位置（l）」标记有地理信息的事项，「向上（u）」和「向下（d）」标记某一指标或状态的变化趋势。

### 2.2 核心价值

**零记忆成本**：不再需要背诵 `- [!]` 是重要还是 `- [*]` 是星标，每次触发模板都能从可视化列表中直接选择，且每一项都有中英文双语标签辅助理解。

**所见即所得**：下拉列表中每种任务类型都通过真实的 Obsidian 复选框组件渲染，你在选择时看到的样式就是插入后在笔记中呈现的样式（前提是已安装相应的 CSS 主题或插件，详见第 3 节）。

**支持多行内容**：模板提供的是文本域（textarea）而非单行输入框，支持粘贴多段文字、列表或代码片段作为任务内容，内容会保留原有的换行与格式。

**与标准语法完全兼容**：输出结果是纯粹的 Markdown 任务列表语法，不引入任何私有格式，可被 Tasks、Dataview、Kanban 等插件正常识别和查询。

---

## 3. 前提条件与必要插件

### 3.1 Obsidian 版本要求

建议使用 **Obsidian 1.4.0 及以上版本**，以确保 Templater 的异步模板执行与 Modal API 能够正常工作。

### 3.2 必须安装的插件

本模板仅依赖一款社区插件。

---

**插件：Templater**

Templater 是 Obsidian 中功能最强大的模板引擎插件，由开发者 SilentVoid13 创建并维护。

- **GitHub 仓库**：<https://github.com/SilentVoid13/Templater>
- **官方文档**：<https://silentvoid13.github.io/Templater>

本模板使用的是 Templater 的 **异步脚本块**（`<%* … %>`），所有交互逻辑均在这个块中运行。模板通过 `tp.obsidian.Modal` 调用 Obsidian 原生的模态弹窗 API 构建交互界面，这是 Templater 所暴露的底层 Obsidian API 能力，无需额外配置。

---

### 3.3 可选但推荐的插件（用于渲染任务样式）

模板输出的任务项本身是标准 Markdown 语法，在任何情况下都可以正常写入和读取。但若希望 `- [!]`、`- [/]`、`- [*]` 等扩展任务类型在 Obsidian 阅读视图中以自定义图标或颜色渲染（而非统一显示为已勾选的复选框），还需要额外的渲染支持。

以下是常见的两种方案：

**方案一：Obsidian Tasks 插件**

Tasks 插件原生支持部分扩展任务类型（如 `- [/]` 进行中、`- [-]` 已取消等），并在任务上附加截止日期、重复规则等元数据解析能力。

- **GitHub 仓库**：<https://github.com/obsidian-tasks-group/obsidian-tasks>

**方案二：自定义 CSS 片段（Checkbox Styling）**

通过 CSS 片段为每个 `data-task` 属性值定义独立的样式（图标、颜色等）。多款流行主题（如 Things、AnuPpuccin、Minimal）内置了对扩展任务类型的 CSS 支持，也可以自行编写或从社区获取 CSS 片段放入 Obsidian 的 `.obsidian/snippets/` 目录启用。

> 💡 **说明**：若不安装上述任何插件或 CSS 片段，扩展任务类型仍然可以正常插入和读取，只是在阅读视图中所有已勾选状态（`[x]`、`[!]`、`[/]` 等）可能统一显示为标准已完成样式，视觉上无法区分。模板的弹窗界面在选择时会实时渲染复选框样式，若当前库已有相关 CSS 支持，弹窗内的预览也会同步呈现相应样式。

---

## 4. 模板文件安装方式

### 4.1 第一步：确认 Templater 已正确配置

1. 打开**设置 → 第三方插件**，确认 Templater 已安装并处于启用状态
2. 点击 Templater 右侧的**齿轮图标**进入其设置页面
3. 找到 **"Template folder location"**（模板文件夹位置）选项，确认已填写一个存放模板文件的文件夹路径

> 💡 **说明（按需调整）**：Templater 的模板文件夹路径由你自行指定，常见设置为 `Templates`、`_Templates` 或 `00-Templates` 等。若尚未配置，在此处填入希望用于存放模板文件的文件夹名称即可；Templater 会在该文件夹不存在时自动创建。

### 4.2 第二步：放置模板文件

将模板文件 `插入-任务列表模板.md` 放入上一步配置的模板文件夹中。

> 📎 **获取完整代码**
>
> 1. 为了提升阅读体验，完整代码已放置于本文末尾**附录**部分，可直接前往复制使用。
> 2. 关注微信公众号：**Obsidianist**，回复关键词：**任务列表模板**，后台即会自动回复完整代码文件下载地址，下载后直接放入 Templater 模板文件夹，无需任何额外配置，即可使用。

> ⚠️ **注意（特定设置）**：模板文件的**文件名可以自由修改**，Templater 会将文件名注册为对应命令的名称（格式为 `Templater: Insert template - 文件名`）。若修改了文件名，在命令面板中触发时请使用新的文件名关键字搜索。

---

## 5. 使用流程：从触发到插入

### 5.1 触发方式

**方式 A——命令面板（通用）**：按 `Ctrl+P`（Windows/Linux）或 `Cmd+P`（macOS）打开命令面板，输入「任务列表」或模板文件名的关键字，找到并选择 `Templater: Insert template - 插入-任务列表模板`，回车确认。

**方式 B——绑定快捷键（推荐）**：在**设置 → 快捷键**中搜索 `插入-任务列表模板`，为其绑定一个专属快捷键（如 `Ctrl+Shift+T`），后续一键触发，无需每次打开命令面板。

> 💡 **触发前的准备**：在触发模板前，请确认当前笔记处于**编辑模式**，且光标已定位到希望插入任务项的位置。模板会在光标处写入任务内容，若光标不在预期位置，可在模板插入后手动移动。

### 5.2 弹窗操作流程

触发模板后，Obsidian 界面中央会出现一个原生风格的模态对话框，标题为 **" 插入任务项（Ctrl+Enter 确认）"**，包含以下几个交互区域：

**任务类型选择器**：位于对话框上方，是一个自定义的下拉触发器。初始状态下显示提示文字「请点击选择任务类型…」和一个向下箭头。点击后展开下拉列表，列出全部二十三种任务类型；每一项均以实际渲染的复选框 + 双语标签的形式呈现，让你直观地看到每种任务类型在笔记中的显示效果。点击任意一项即可完成选择，列表自动收起，触发器区域随即展示所选任务类型的预览。**在提交前必须选择一种任务类型**，否则确认按钮触发后，选择器边框会短暂变红以提示未选择。

**任务内容输入框**：位于类型选择器下方，是一个可调整高度的多行文本域，占位提示为「在此处粘贴或输入多行内容…」。可在此输入任务的具体描述，也可以留空——留空时，模板会在任务前缀后保留一个空格，方便插入后直接在光标处继续输入内容。

**按钮区域**：对话框底部有「确认插入」和「取消」两个按钮。点击**确认插入**（或在内容输入框中按 `Ctrl+Enter`）完成插入；点击**取消**或按 `Esc` 关闭对话框并取消操作。

> 💡 **操作小技巧**：对话框打开后，焦点会自动落在内容输入框上，因此常见操作流程可以是：打开弹窗 → 鼠标点击选择任务类型 → 直接在输入框中键入内容（无需再次点击输入框）→ 按 `Ctrl+Enter` 确认，全程基本无需额外点击。

---

## 6. 任务类型完整列表说明

模板内置了以下二十三种任务类型，覆盖了日常任务管理中最常用的语义标记场景：

| 插入语法 | 英文标签 | 中文标签 | 典型使用场景 |
|---------|---------|---------|-----------|
| `- [ ]` | Unchecked | 未勾选 | 普通待办事项，尚未开始 |
| `- [x]` | Checked | 已勾选 | 已完成的任务 |
| `- [>]` | Rescheduled | 已重新安排 | 推迟到其他日期的任务 |
| `- [<]` | Scheduled | 已安排 | 已规划到特定日期的任务 |
| `- [!]` | Important | 重要 | 高优先级或必须关注的事项 |
| `- [-]` | Cancelled | 已取消 | 决定不再执行的任务（保留记录） |
| `- [/]` | In Progress | 进行中 | 当前正在推进的任务 |
| `- [?]` | Question | 问题 | 待确认的疑问或需要调查的问题 |
| `- [*]` | Star | 星标 | 值得特别关注或收藏的内容 |
| `- [n]` | Note | 备注 | 附加说明或补充注解 |
| `- [l]` | Location | 位置 | 与地点或地理信息相关的任务 |
| `- [i]` | Information | 信息 | 需要记录的参考信息或事实 |
| `- [I]` | Idea | 想法 | 灵感、创意或待评估的新思路 |
| `- [S]` | Amount | 金额 | 与费用、预算或财务相关的事项 |
| `- [p]` | Pro | 优点 | 某方案或决策的正面理由 |
| `- [c]` | Con | 缺点 | 某方案或决策的负面顾虑 |
| `- [b]` | Bookmark | 书签 | 需要进一步查阅的链接或来源 |
| `- ["]` | Quote | 引用 | 原文摘抄或值得保留的引述 |
| `- [u]` | Up | 向上 | 趋势上升、状态改善或正向变化 |
| `- [d]` | Down | 向下 | 趋势下降、状态变差或负向变化 |
| `- [w]` | Win | 胜利/成功 | 达成的里程碑、好消息或成果 |
| `- [k]` | Key | 关键 | 核心要点、关键决策或关键人物 |
| `- [f]` | Fire | 紧急/火 | 需要立即处理的紧急事项 |

> ⚠️ **注意（特定设置）**：上表中列出的二十三种任务类型及其对应的符号，均为模板代码中预设的 `todoMap` 配置。这份列表**并非 Obsidian 或 Tasks 插件的官方规范**，而是模板作者根据常用工作流整理的一套任务语义体系。
>
> 若你的工作流中需要增减任务类型，或希望使用不同的符号对应关系，可以直接修改附录代码中的 `todoMap` 数组，每一项的结构为：
>
> ```js
> { task: '符号', label: '标签文字', insert: '- [符号]', checked: true/false }
> ```
>
> 其中 `checked` 控制复选框在预览中是否以「已勾选」状态渲染，`insert` 为实际插入笔记的前缀。未勾选任务（`- [ ]`）应将 `checked` 设为 `false`，其余类型通常设为 `true`。

---

## 7. 弹窗交互细节

### 7.1 下拉选择器的行为逻辑

任务类型选择器采用自定义的折叠/展开下拉组件，而非浏览器原生的 `<select>` 元素，这样做的目的是让每个选项都能以完整的复选框样式渲染——原生下拉框无法在选项内显示自定义 HTML。

具体交互规则如下：点击触发器区域切换展开/折叠状态；展开时列表最大高度为 320px，超出部分可滚动；选中某项后列表立即关闭，触发器内替换为所选项的复选框预览；再次点击触发器可重新展开并更换选择。

### 7.2 输入验证

点击「确认插入」时，模板只会检查一项条件：是否已选择了任务类型。若未选择，选择器边框会短暂变为红色，同时列表自动展开，提示你完成选择后再提交。内容输入框则没有任何强制要求，可以为空。

### 7.3 多行内容的处理方式

内容输入框是一个标准的 `<textarea>`，支持换行和粘贴多段文字。模板在处理内容时，会对全部输入进行 `trim()` 操作（去除首尾多余空白），然后整体拼接到任务前缀之后。

需要注意的是：多行内容会被作为单个字符串插入到任务项的文字部分，而非生成多条任务项。如果你粘贴了三行内容，插入结果会是一条任务项，其文字部分包含三行。若需要生成多条独立的任务项，触发多次模板是更适合的方式。

### 7.4 Ctrl+Enter 快捷键

`Ctrl+Enter` 快捷键仅在焦点处于**内容输入框**时生效，其他情况下（如焦点在选择器上）不会触发确认。这是有意设计的，避免在浏览下拉列表时误触发提交。

---

## 8. 输出格式说明

确认插入后，模板会在当前笔记**光标位置**写入以下格式的内容：

**有内容时**：

```markdown
- [x] 任务内容文字
```

**无内容时（输入框留空）**：

```markdown
- [x] 
```

末尾会跟随一个换行符（`\n`），确保插入后光标位置在任务项的下一行，方便继续追加内容。

> 💡 **说明**：以上示例中的 `[x]` 仅为占位示意，实际插入的符号取决于你在弹窗中所选的任务类型（如选择「重要」则插入 `- [!]`，选择「进行中」则插入 `- [/]`，以此类推）。

---

## 9. 常见问题排查

### 9.1 触发模板后没有弹出任何对话框

**原因一**：Templater 插件未启用，或模板文件未放置在正确的模板文件夹中。

**解决方法**：确认 Templater 已在**设置 → 第三方插件**中启用，且模板文件位于 Templater 设置中指定的模板文件夹内。

**原因二**：在命令面板搜索到的条目不是该模板对应的 Templater 命令，而是 Obsidian 内置「模板」插件的命令（两者外观类似，但功能不同）。

**解决方法**：确认选择的命令前缀为 `Templater:` 而非 `Templates:`，两者是不同的命令体系，后者不支持脚本执行，无法渲染模板中的 `<%* %>` 块。

---

### 9.2 弹窗中的复选框样式全部显示为普通已勾选样式，没有图标区分

**原因**：当前 Obsidian 库没有为扩展任务类型（`data-task` 属性不为空或 `x`）定义自定义 CSS 样式。弹窗内的复选框使用的是与笔记阅读视图相同的 CSS 渲染环境，若库中没有相关 CSS，所有已勾选状态的复选框看起来都一样。

**解决方法**：安装支持扩展任务类型的主题（如 Things、AnuPpuccin）或启用相关 CSS 片段。未安装 CSS 支持不影响模板的任何功能，仅影响复选框图标的视觉区分度。

---

### 9.3 选择好任务类型并填写内容后点击确认，笔记中没有出现插入内容

**原因一**：当前笔记处于**阅读视图**而非编辑视图。Templater 的插入操作依赖编辑器光标，在阅读视图中没有可用的光标位置。

**解决方法**：切换到**编辑视图**后重新触发模板。

**原因二**：光标不在任何已打开的笔记中（如焦点在侧边栏或其他面板上）。

**解决方法**：先点击一次笔记编辑区域，确认光标定位在目标位置后再触发模板。

---

### 9.4 插入的任务项出现在了错误的位置

**原因**：触发模板时光标所在位置与预期不符。模板的插入位置完全取决于触发时编辑器中光标的实际位置。

**解决方法**：触发模板前，先用鼠标点击（或方向键移动）确认光标位于正确行。若偶尔插入位置有误，可立即按 `Ctrl+Z` 撤销，重新定位光标后再次触发。

---

### 9.5 弹窗关闭后，Obsidian 提示「已取消」或类似通知

**原因**：按了 `Esc`、点击了「取消」按钮，或点击了弹窗以外的遮罩区域——这三种操作都会触发取消逻辑，模板不会插入任何内容，属于预期行为。

**说明**：此时 Obsidian 不会显示任何通知（模板取消时直接输出空字符串 `tR += ''`），笔记内容不会有任何变化，重新触发模板即可。

---

## 10. 进阶用法与个性化调整

### 10.1 增减任务类型

模板代码中的 `todoMap` 数组是唯一需要修改的地方。每一条记录对应弹窗下拉列表中的一行，格式为：

```js
{ task: '符号', label: '显示标签', insert: '- [符号]', checked: true }
```

新增一种类型只需在数组末尾（或任意位置）追加一条记录；删除某类型则移除对应记录；调整顺序则拖动对应行在数组中的位置即可，下拉列表的渲染顺序与数组顺序一致。

> 💡 **说明（按需调整）**：`label` 字段的文字会直接显示在弹窗的下拉列表和触发器预览中，可以自由修改为你更熟悉的名称（如将 `Win - 胜利/成功` 改为 `完成目标`）。修改标签不会影响实际插入到笔记中的内容，插入内容仅由 `insert` 字段决定。

### 10.2 修改弹窗标题文字

弹窗标题在代码中通过以下行设置：

```js
modal.titleEl.setText('插入任务项（Ctrl+Enter 确认）');
```

可以将括号内的提示文字（`Ctrl+Enter 确认`）替换为你更熟悉的操作说明，或将整个标题改为更简短的名称。

### 10.3 调整内容输入框的默认高度

内容输入框默认显示 8 行高度（`rows: '8'`）。如果你通常只需要输入一两行的短任务内容，可以将 `rows` 的值改小（如改为 `'3'`）；如果经常需要粘贴大段内容，可以改大。

```js
const contentInput = el.createEl('textarea', {
  attr: { placeholder: '...', rows: '8', class: 'anp-textarea' }
});
```

### 10.4 为弹窗绑定快捷键以进一步提速

将模板对应的 Templater 命令绑定到快捷键（见第 4 节第一步），再配合 `Ctrl+Enter` 快捷确认，整个插入流程可以做到完全无需鼠标：触发快捷键 → 键盘导航下拉列表选择类型（鼠标点击仍是最快方式）→ 输入内容 → `Ctrl+Enter` 确认。

### 10.5 创建针对特定场景的精简版本

如果你在某类笔记中只会用到其中几种任务类型（例如会议记录只需要「重要」「问题」「已完成」三种），可以将 `todoMap` 精简为仅包含这几项，另存为一个新的模板文件（如 `插入-会议任务项.md`），与原模板并存，按场景选择触发。这样可以减少每次打开下拉列表时需要浏览的选项数量。

---

## 11. 与其他插件的联动方式

### 11.1 与 Obsidian Tasks 插件联动

Tasks 插件能够识别并解析笔记中的任务项，支持根据状态、截止日期、优先级等条件跨笔记查询。本模板插入的任务语法（`- [ ]`、`- [x]`、`- [/]` 等）与 Tasks 插件的任务格式完全兼容，使用本模板插入的任务同样可以被 Tasks 的查询块捕获和管理。

例如，用以下查询块汇总所有「进行中」的任务：

```tasks
not done
status.type is IN_PROGRESS
```

> 💡 **说明（按需调整）**：上方查询中的筛选条件是 Tasks 插件的查询语法，具体支持的状态类型取决于你在 Tasks 设置中配置的「Task Statuses」。如果 Tasks 未将 `[/]` 识别为 `IN_PROGRESS` 状态，需要在 Tasks 插件设置中手动添加对应的状态映射。

### 11.2 与 Dataview 插件联动

Dataview 可以通过 `task` 数据类型查询笔记中的任务项，但标准 Dataview 查询对任务状态的识别较为有限（通常仅区分完成/未完成）。若需要基于扩展任务类型进行细粒度筛选，可以使用 DataviewJS 通过正则表达式匹配行内容：

```js
const pages = dv.pages('"你的笔记文件夹"');
for (const page of pages) {
  const file = await app.vault.read(app.vault.getAbstractFileByPath(page.file.path));
  const importantTasks = file.split('\n').filter(ln => ln.startsWith('- [!]'));
  // 处理 importantTasks...
}
```

> 💡 **说明（按需调整）**：上方代码中的 `'"你的笔记文件夹"'` 为示例路径，请替换为你实际存放笔记的文件夹名称。

### 11.3 与 Kanban 插件联动

Obsidian Kanban 插件支持在看板卡片中嵌入任务列表，并能根据任务完成状态自动移动卡片。本模板插入的标准任务格式在 Kanban 卡片中同样可以正常渲染和交互，适合将任务管理与看板视图结合使用。

### 11.4 与 QuickAdd 插件联动

可以使用 QuickAdd 的 Template 类型 Choice 为本模板绑定一个全局快速操作入口，并将其加入 QuickAdd 命令菜单。这样可以将任务插入与其他常用的快速操作统一管理，通过 QuickAdd 的单一入口触发。

---

## 12. 适用版本与平台说明

| 环境 | 要求 / 说明 |
|------|------------|
| Obsidian | 建议 1.4.0 及以上版本 |
| Templater 插件 | 建议使用最新版，模板使用异步 `<%* %>` 语法及 `tp.obsidian.Modal` API |
| Windows / macOS / Linux | ✅ 完整支持，所有功能均可正常使用 |
| iOS / Android 移动端 | ⚠️ 部分支持：模态弹窗可正常显示和操作；任务类型选择和内容输入功能可用；移动端键盘操作体验可能与桌面端有细微差异，但整体功能不受影响 |

> 💡 **关于移动端的补充说明**：本模板不依赖任何桌面专属 API（如 `request` 函数或剪贴板自动读取），所有功能均基于 Obsidian 的跨平台 Modal API 实现，因此在移动端的兼容性较好。主要差异在于触控操作的交互细节：下拉列表在触屏设备上点击选择的体验与鼠标操作基本一致；`Ctrl+Enter` 快捷键在移动端软键盘下可能无法正常触发，此时点击「确认插入」按钮完成提交即可。

---

## 附录

### 插入 - 任务列表模板完整模板代码

**用途说明**：以下为完整的 Templater 模板文件内容。将其粘贴为一个新的 `.md` 文件，命名为 `插入-任务列表模板.md`，放入 Templater 插件配置的模板文件夹中即可使用。模板执行时弹出选择对话框，接收任务类型与任务内容后，在当前笔记的光标位置插入格式正确的任务项。

> ⚠️ **使用方法**：将下方代码块中的全部内容（从 `<%*` 到最后的 `-%>` ）完整复制，粘贴到一个新建的 Markdown 文件中，确保文件内容仅包含模板代码，不含任何其他文字，保存后放入 Templater 的模板文件夹。

````
<%*
const todoMap = [
  { task: ' ', label: 'Unchecked - 未勾选',       insert: '- [ ]', checked: false },
  { task: 'x', label: 'Checked - 已勾选',         insert: '- [x]', checked: true  },
  { task: '>', label: 'Rescheduled - 已重新安排',  insert: '- [>]', checked: true  },
  { task: '<', label: 'Scheduled - 已安排',        insert: '- [<]', checked: true  },
  { task: '!', label: 'Important - 重要',          insert: '- [!]', checked: true  },
  { task: '-', label: 'Cancelled - 已取消',        insert: '- [-]', checked: true  },
  { task: '/', label: 'In Progress - 进行中',      insert: '- [/]', checked: true  },
  { task: '?', label: 'Question - 问题',           insert: '- [?]', checked: true  },
  { task: '*', label: 'Star - 星标',               insert: '- [*]', checked: true  },
  { task: 'n', label: 'Note - 备注',               insert: '- [n]', checked: true  },
  { task: 'l', label: 'Location - 位置',           insert: '- [l]', checked: true  },
  { task: 'i', label: 'Information - 信息',        insert: '- [i]', checked: true  },
  { task: 'I', label: 'Idea - 想法',               insert: '- [I]', checked: true  },
  { task: 'S', label: 'Amount - 金额',             insert: '- [S]', checked: true  },
  { task: 'p', label: 'Pro - 优点',                insert: '- [p]', checked: true  },
  { task: 'c', label: 'Con - 缺点',                insert: '- [c]', checked: true  },
  { task: 'b', label: 'Bookmark - 书签',           insert: '- [b]', checked: true  },
  { task: '"', label: 'Quote - 引用',              insert: '- ["]', checked: true  },
  { task: 'u', label: 'Up - 向上',                 insert: '- [u]', checked: true  },
  { task: 'd', label: 'Down - 向下',               insert: '- [d]', checked: true  },
  { task: 'w', label: 'Win - 胜利/成功',            insert: '- [w]', checked: true  },
  { task: 'k', label: 'Key - 关键',                insert: '- [k]', checked: true  },
  { task: 'f', label: 'Fire - 紧急/火',             insert: '- [f]', checked: true  },
];

function makeTaskRow(parentEl, def, onClick) {
  const wrapper = parentEl.createEl('div', { cls: 'anp-row-wrap' });
  const ul = wrapper.createEl('ul', { cls: 'contains-task-list anp-task-ul' });
  const li = ul.createEl('li', {
    cls: 'task-list-item anp-task-li',
    attr: { 'data-task': def.task }
  });
  const cb = li.createEl('input', {
    cls: 'task-list-item-checkbox',
    attr: { type: 'checkbox' }
  });
  if (def.checked) cb.setAttribute('checked', '');
  cb.disabled = true;
  li.createEl('span', { cls: 'anp-task-label', text: def.label });
  if (onClick) {
    wrapper.style.cursor = 'pointer';
    wrapper.addEventListener('click', () => onClick(def));
  }
  return wrapper;
}

const result = await new Promise((resolve) => {
  const modal = new tp.obsidian.Modal(app);
  modal.titleEl.setText('插入任务项（Ctrl+Enter 确认）');

  modal.contentEl.createEl('style').textContent = `
    .anp-task-ul,
    .anp-task-ul.contains-task-list {
      display: block !important;
      list-style: none !important;
      padding: 0 0 0 28px !important;
      margin: 0 !important;
    }
    .anp-task-li,
    .anp-task-li.task-list-item {
      display: flex !important;
      align-items: center !important;
      position: relative !important;
      height: auto !important;
      min-height: 28px !important;
      max-height: none !important;
      overflow: visible !important;
      visibility: visible !important;
      opacity: 1 !important;
      padding: 3px 8px 3px 4px !important;
      margin: 0 !important;
      border-radius: 4px !important;
      gap: 8px !important;
    }
    .anp-task-li .task-list-item-checkbox {
      flex-shrink: 0 !important;
      pointer-events: none !important;
    }
    .anp-task-label {
      font-size: 13px !important;
      display: block !important;
      visibility: visible !important;
    }
    .anp-row-wrap {
      border-radius: 5px;
      transition: background 0.1s ease;
    }
    .anp-row-wrap:hover {
      background: var(--background-modifier-hover);
    }
    .anp-trigger {
      display: flex;
      align-items: center;
      padding: 4px 10px 4px 4px;
      border: 1px solid var(--background-modifier-border);
      border-radius: 6px;
      cursor: pointer;
      background: var(--background-secondary);
      transition: border-color 0.15s ease;
      user-select: none;
      min-height: 38px;
      box-sizing: border-box;
      gap: 4px;
    }
    .anp-trigger:hover    { border-color: var(--interactive-accent); }
    .anp-trigger.anp-open {
      border-color: var(--interactive-accent);
      border-bottom-left-radius: 0;
      border-bottom-right-radius: 0;
    }
    .anp-trigger-placeholder {
      color: var(--text-muted);
      font-size: 13px;
      flex: 1;
      padding-left: 8px;
    }
    .anp-trigger-arrow {
      color: var(--text-muted);
      font-size: 12px;
      flex-shrink: 0;
      transition: transform 0.15s ease;
    }
    .anp-trigger.anp-open .anp-trigger-arrow { transform: rotate(180deg); }
    .anp-trigger .anp-row-wrap {
      flex: 1;
      pointer-events: none;
    }
    .anp-trigger .anp-task-li {
      padding: 1px 4px 1px 4px !important;
      min-height: 24px !important;
    }
    .anp-list-wrap {
      display: none;
      border: 1px solid var(--interactive-accent);
      border-top: none;
      border-bottom-left-radius: 6px;
      border-bottom-right-radius: 6px;
      background: var(--background-primary);
      overflow-y: auto;
      max-height: 320px;
      margin-bottom: 14px;
      overflow-x: visible;
    }
    .anp-list-wrap.anp-open { display: block; }
    .anp-list-inner {
      display: flex;
      flex-direction: column;
      padding: 4px;
      gap: 1px;
      padding-left: 4px;
    }
    .anp-label {
      font-weight: bold;
      font-size: 13px;
      margin-bottom: 6px;
    }
    .anp-input {
      width: 100%; box-sizing: border-box; font-size: 13px; padding: 6px;
      margin-bottom: 4px; border-radius: 4px;
      border: 1px solid var(--background-modifier-border);
      background: var(--background-primary); color: var(--text-normal);
    }
    .anp-textarea {
      width: 100%; box-sizing: border-box; font-size: 13px; padding: 8px;
      margin-bottom: 4px; border-radius: 4px; resize: vertical; font-family: inherit;
      border: 1px solid var(--background-modifier-border);
      background: var(--background-primary); color: var(--text-normal);
    }
    .anp-btns { margin-top: 12px; text-align: right; }
    .anp-confirm-btn {
      background: var(--interactive-accent) !important;
      border: 1px solid var(--interactive-accent) !important;
      color: var(--text-on-accent) !important;
      padding: 5px 14px !important;
      border-radius: 6px !important;
      font-size: 13px !important;
      cursor: pointer !important;
      transition: filter 0.1s ease !important;
      box-shadow: none !important;
    }
    .anp-confirm-btn:hover { filter: brightness(1.1) !important; }
  `;

  const el = modal.contentEl;
  let selectedDef = null;
  let isOpen = false;

  el.createEl('div', { cls: 'anp-label', text: '任务类型' });

  const trigger     = el.createEl('div', { cls: 'anp-trigger' });
  const placeholder = trigger.createEl('span', {
    cls: 'anp-trigger-placeholder',
    text: '请点击选择任务类型…'
  });
  const arrow = trigger.createEl('span', { cls: 'anp-trigger-arrow', text: '▾' });

  const listWrap  = el.createEl('div', { cls: 'anp-list-wrap' });
  const listInner = listWrap.createEl('div', { cls: 'anp-list-inner' });

  for (const def of todoMap) {
    makeTaskRow(listInner, def, (chosen) => {
      selectedDef = chosen;
      isOpen = false;
      listWrap.removeClass('anp-open');
      trigger.removeClass('anp-open');
      placeholder.style.display = 'none';
      trigger.querySelectorAll('.anp-row-wrap').forEach(n => n.remove());
      const prev = makeTaskRow(trigger, chosen, null);
      trigger.insertBefore(prev, arrow);
    });
  }

  trigger.addEventListener('click', () => {
    isOpen = !isOpen;
    listWrap.toggleClass('anp-open', isOpen);
    trigger.toggleClass('anp-open', isOpen);
  });

  el.createEl('div', { cls: 'anp-label', text: '任务内容（可留空）：' });
  const contentInput = el.createEl('textarea', {
    attr: { placeholder: '在此处粘贴或输入多行内容…', rows: '8', class: 'anp-textarea' }
  });

  const btnRow     = el.createEl('div', { cls: 'anp-btns' });
  const confirmBtn = btnRow.createEl('button', { text: '确认插入', cls: 'anp-confirm-btn' });
  const cancelBtn  = btnRow.createEl('button', { text: '取消' });
  cancelBtn.style.marginLeft = '8px';

  const confirm = () => {
    if (!selectedDef) {
      trigger.style.borderColor = 'var(--color-red)';
      setTimeout(() => { trigger.style.borderColor = ''; }, 1200);
      if (!isOpen) { isOpen = true; listWrap.addClass('anp-open'); trigger.addClass('anp-open'); }
      return;
    }
    resolve({ def: selectedDef, content: contentInput.value });
    modal.close();
  };
  const cancel = () => { resolve(null); modal.close(); };

  confirmBtn.addEventListener('click', confirm);
  cancelBtn.addEventListener('click',  cancel);
  contentInput.addEventListener('keydown', e => {
    if (e.key === 'Enter' && e.ctrlKey) confirm();
  });

  modal.onClose = () => resolve(null);
  modal.open();
  setTimeout(() => contentInput.focus(), 50);
});

if (!result) {
  tR += '';
} else {
  const trimmed = (result.content && result.content.trim()) ? result.content.trim() : '';
  const line = trimmed.length > 0
    ? `${result.def.insert} ${trimmed}`
    : `${result.def.insert} `;
  tR += line + '\n';
}
-%>
````

> 📎 **获取完整代码**
>
> 1. 以上即为完整代码，可直接复制使用。
> 2. 关注微信公众号：**Obsidianist**，回复关键词：**任务列表模板**，后台即会自动回复完整代码文件下载地址，下载后直接放入 Templater 模板文件夹，无需任何额外配置，即可使用。
