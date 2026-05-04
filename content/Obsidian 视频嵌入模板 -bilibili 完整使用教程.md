---
tags:
  - Obsidian/视频/bilibili
---

# Obsidian 视频嵌入模板 -bilibili 完整使用教程

本教程适用于 **插入 - 视频嵌入模板 -bilibili**——一款基于 Templater 插件的 Obsidian 模板文件，可帮助你在笔记中一键嵌入哔哩哔哩（B 站）视频播放器，并附带完整的时间戳管理系统，支持添加、跳转、删除时间戳，以及在视频播放过程中精准记录进度节点。

---

## 目录

1. [什么是插入 - 视频嵌入模板 -bilibili](#1.%20什么是插入%20-%20视频嵌入模板%20-bilibili)
2. [适用场景与核心价值](#2.%20适用场景与核心价值)
3. [前提条件与必要插件](#3.%20前提条件与必要插件)
4. [模板文件安装方式](#4.%20模板文件安装方式)
5. [使用流程：从触发到嵌入](#5.%20使用流程：从触发到嵌入)
6. [剪贴板自动识别的工作原理](#6.%20剪贴板自动识别的工作原理)
7. [输出结果：笔记结构说明](#7.%20输出结果：笔记结构说明)
8. [时间戳管理系统详解](#8.%20时间戳管理系统详解)
9. [播放器交互功能说明](#9.%20播放器交互功能说明)
10. [常见问题排查](#10.%20常见问题排查)
11. [进阶用法与个性化调整](#11.%20进阶用法与个性化调整)
12. [与其他插件的联动方式](#12.%20与其他插件的联动方式)
13. [适用版本与平台说明](#13.%20适用版本与平台说明)

---

## 1. 什么是插入 - 视频嵌入模板 -bilibili

在 Obsidian 中记录视频笔记，始终有一个令人烦恼的断层：视频播放在浏览器，笔记写在 Obsidian，两者无法互通，既不能在笔记中直接看视频，也没有办法将某一个时间节点与笔记内容精确关联起来。

**插入 - 视频嵌入模板 -bilibili** 正是为消除这一断层而生。它是一个基于 Templater 插件的模板文件，触发后会完成以下一系列工作：

1. 自动读取剪贴板，识别其中的 B 站视频链接，并通过 B 站 API 预填视频标题
2. 弹出一个样式简洁的输入对话框，让你确认视频标题和链接
3. 在当前笔记中生成一个标准化的页面结构，包含视频标题、内嵌播放器和时间戳列表区
4. 播放器通过 DataviewJS 渲染为全功能的交互组件，支持点击时间戳精准跳转、添加当前播放进度为时间戳、删除和复制时间戳等操作

整个过程在笔记内部闭环完成，无需来回切换浏览器与 Obsidian，是构建视频笔记工作流的高效起点。

---

## 2. 适用场景与核心价值

### 2.1 典型应用场景

**场景一：技术课程学习笔记** 观看 B 站编程、设计、语言学习等教程时，一边播放视频，一边在笔记中记录重点。通过时间戳功能，将笔记内容与视频中的具体讲解片段精确绑定，方便日后复习时一键跳回原处。

**场景二：读书会与演讲回放** 录制的演讲、讲座或读书会视频上传到 B 站后，可以在 Obsidian 中为每一个关键论点、引用或亮点打上时间戳，形成带有导航功能的视频索引笔记。

**场景三：纪录片与科普视频摘录** 观看纪录片或科普内容时，在感兴趣的片段处记录时间戳，并在时间戳附近写下自己的思考，形成可检索的知识条目。

**场景四：视频内容审核与制作** 视频创作者在审看自己的 B 站作品时，可将需要修改的时间点逐一标注为时间戳，在笔记中积累修改意见，再统一处理。

### 2.2 核心价值

**视频与笔记真正合一**：视频播放器直接渲染在笔记页面内，无需切换窗口，阅读笔记与观看视频可以同步进行。

**时间戳即导航**：时间戳不仅是文字记录，点击即可控制播放器跳转到对应秒数，与浏览器书签相比更加灵活，与外部时间戳工具相比则与笔记内容紧密结合。

**自动化链接提取**：粘贴任意格式的 B 站链接（包含追踪参数、分享短链等），模板自动提取干净的 BV 号或 av 号，并通过 B 站 API 获取视频标题，减少手动输入。

**时间戳持久化存储**：时间戳以标准 Markdown 链接格式写入笔记文件，不依赖插件私有数据，可被 Dataview 等插件查询，也可在任何 Markdown 编辑器中读取。

---

## 3. 前提条件与必要插件

### 3.1 Obsidian 版本要求

建议使用 **Obsidian 1.4.0 及以上版本**，以确保 DataviewJS 渲染和 Templater 的异步模板功能均能正常工作。

### 3.2 必须安装的插件

本模板依赖以下两款社区插件，**缺一不可**。

---

**插件一：Templater**

Templater 是 Obsidian 中功能最强大的模板引擎插件，由开发者 SilentVoid13 创建并维护。

- **GitHub 仓库**：<https://github.com/SilentVoid13/Templater>
- **官方文档**：<https://silentvoid13.github.io/Templater>

与 Obsidian 内置的 " 模板 " 核心插件不同，Templater 支持动态模板语法，允许在模板中执行 JavaScript 代码（包括异步操作）、读取系统剪贴板、发起 HTTP 请求、操作 Vault 文件等。本模板使用的是 Templater 的 **异步脚本块**（`<%* … %>`），所有交互逻辑都在这个块中运行。

本模板还使用了 Templater 提供的内置 `request` 函数来调用 B 站 API。该函数通过 Node.js 主进程发起请求，可以绕过浏览器的 CORS 限制，因此**能够成功访问** B 站 API——这是普通 `fetch()` 在 Obsidian 中无法做到的。

> ⚠️ **注意**：`request` 函数是 Templater 的桌面端特性，仅在 **桌面端 Obsidian** 中可用。在移动端，该函数不存在，模板已做兼容处理，自动跳过 API 调用，其余功能仍可正常使用。详见第 13 节的平台说明。

---

**插件二：Dataview**

Dataview 插件由 Michael Brenan（blacksmithgu）开发，提供强大的笔记查询能力，同时也支持以 `dataviewjs` 代码块运行完整的 JavaScript，并将其输出渲染为交互式 UI 组件。

- **GitHub 仓库**：<https://github.com/blacksmithgu/obsidian-dataview>

本模板生成的视频播放器和时间戳管理界面，全部以 `dataviewjs` 代码块的形式嵌入笔记。只有在 Dataview 插件启用且 DataviewJS 功能开启的情况下，这些代码块才能正确渲染为可交互的播放器组件。

> ⚠️ **Dataview 设置检查**：安装 Dataview 后，请进入其设置页面，确认 **"Enable JavaScript Queries"**（启用 JavaScript 查询）选项处于**开启**状态。默认情况下此选项已开启，但如果你曾出于安全考虑关闭过，需要重新打开，否则 `dataviewjs` 块将拒绝执行，播放器将无法显示。

### 3.3 所需权限与网络条件

**剪贴板访问权限**：模板启动时会尝试读取剪贴板内容，浏览器沙箱环境可能要求用户手动授权。在 Obsidian 桌面端（Electron 环境）中，该权限通常自动授予，无需额外操作。

**网络连接（可选）**：调用 B 站 API 自动获取视频标题时需要网络连接。如果网络不可用或请求失败，模板会静默跳过这一步，你需要在弹窗中手动输入标题，其余功能不受影响。

---

## 4. 模板文件安装方式

### 4.1 第一步：确认 Templater 已正确配置

1. 打开**设置 → 第三方插件**，确认 Templater 已安装并处于启用状态
2. 点击 Templater 右侧的**齿轮图标**进入其设置页面
3. 找到 **"Template folder location"**（模板文件夹位置）选项，确认已填写一个存放模板文件的文件夹路径

> 💡 **说明（按需调整）**：Templater 的模板文件夹路径是你自行指定的，常见设置为 `Templates`、`_Templates` 或 `00-Templates` 等。若尚未配置，在此处填入你希望用于存放模板文件的文件夹名称即可，Templater 会自动创建该文件夹。

### 4.2 第二步：放置模板文件

将模板文件 `插入-视频嵌入模板-bilibili.md` 放入上一步配置的模板文件夹中。

> 📎 **获取完整代码**
>
> 1. 为了提升阅读体验，完整代码已放置于本文末尾**附录**部分，可直接前往复制使用。
> 2. 关注微信公众号：**Obsidianist**，回复关键词：**视频嵌入模板-bilibili**，后台即会自动回复完整代码文件下载地址，下载后直接放入 Templater 模板文件夹，无需任何额外配置，即可使用。

> ⚠️ **注意（特定设置）**：模板文件的**文件名可以自由修改**，文件名将直接影响你在命令面板中搜索时显示的命令名称（Templater 会注册形如 `Templater: Insert template - 插入-视频嵌入模板-bilibili` 的命令）。若你修改了文件名，后续触发时请使用新文件名对应的命令名称搜索。

### 4.3 第三步：确认 Dataview 已启用 DataviewJS

1. 进入**设置 → 第三方插件**，确认 Dataview 已安装并启用
2. 点击 Dataview 右侧的**齿轮图标**进入设置
3. 找到 **"Enable JavaScript Queries"** 选项，确认其处于**开启**（蓝色）状态

---

## 5. 使用流程：从触发到嵌入

### 5.1 触发方式

**方式 A——命令面板（通用）**：按 `Ctrl+P`（Windows/Linux）或 `Cmd+P`（macOS）打开命令面板，输入 `bilibili` 或模板文件名的关键字，找到并选择 `Templater: Insert template - 插入-视频嵌入模板-bilibili`，回车确认。

**方式 B——绑定快捷键（推荐）**：在**设置 → 快捷键**中搜索 `插入-视频嵌入模板-bilibili`，为其绑定一个专属快捷键（如 `Ctrl+Shift+B`），后续一键触发。

**方式 C——文件夹右键菜单**：在 Templater 设置中开启 **"Enable Folder Templates"** 或 **"Trigger Templater on new file creation"** 后，也可以通过右键新建文件并自动应用模板的方式触发。

> 💡 **触发前的准备**：在触发模板前，建议先**将目标 B 站视频链接复制到剪贴板**（在浏览器中打开视频页面后直接复制地址栏链接即可）。模板会在启动时自动读取并解析剪贴板内容，完成链接提取和标题获取，省去手动填写的步骤。当然，若剪贴板中没有有效链接，也可以在弹窗中手动输入，不会影响使用。

### 5.2 弹窗操作详解

触发模板后，Obsidian 界面中央会出现一个浮层对话框，标题为 **"📺 添加哔哩哔哩视频 "**，包含以下两个输入字段：

**📝 视频标题**：用于填写视频的标题，将作为嵌入后笔记页面的一级标题（`# 视频标题`）。如果剪贴板中的链接成功触发了 API 调用，此字段会自动预填视频标题；若预填内容有误或为空，可手动修改或输入。

**🔗 哔哩哔哩网址**：用于填写 B 站视频链接，支持以下格式：

- 标准 BV 号链接：`https://www.bilibili.com/video/BV1234567890`
- 带追踪参数的分享链接：`https://www.bilibili.com/video/BV1234567890?share_source=copy_web`
- av 号链接：`https://www.bilibili.com/video/av12345678`
- 含时间参数的精准跳转链接：`https://www.bilibili.com/video/BV1234567890?t=120`

以上格式模板均可正确解析，额外的参数会被自动剥离，只保留干净的视频 ID。

**对话框交互规则**：

填写完毕后，点击 **" 确认插入 "** 按钮（或在任意输入框中按 Enter）提交；点击 **" 取消 "** 或按 Esc 或点击背景遮罩，均会关闭对话框并取消操作。

弹窗聚焦规则：若视频标题已自动预填，光标会自动定位到网址输入框；若标题为空，光标定位到标题输入框。这样设计是为了减少不必要的点击次数。

### 5.3 输入验证

点击 " 确认插入 " 后，模板会依次校验以下条件：

- 标题字段不能为空
- 链接字段不能为空
- 链接必须包含 `bilibili.com/video` 路径段
- 链接中必须能解析出有效的 BV 号或 av 号

任何一项校验失败，都会在 Obsidian 右上角弹出相应的错误通知（如 `❌ 请输入视频标题`、`❌ 不是有效的哔哩哔哩视频链接` 等），并**不执行插入操作**。你可以重新触发模板，按照提示修正后再试。

---

## 6. 剪贴板自动识别的工作原理

理解这一机制有助于你更顺畅地使用模板，也方便在出现异常时快速判断原因。

模板在启动时（弹窗出现之前），会立即尝试读取剪贴板内容并执行以下步骤：

**第一步：提取视频 ID**——使用正则表达式分别匹配 BV 号（`/BV[a-zA-Z0-9]+/`）和 av 号（`/av\d+/`），优先识别 BV 号。

**第二步：构造标准链接**——将提取到的视频 ID 拼接为标准格式链接（`https://www.bilibili.com/video/BVxx`），作为网址字段的预填值。

**第三步：调用 B 站 API 获取标题**——通过 Templater 的 `request` 函数向 B 站公开接口（`https://api.bilibili.com/x/web-interface/view?bvid=xxx`）发起 GET 请求，解析返回的 JSON 中的 `data.title` 字段，作为标题字段的预填值。

上述三步中，任何一步出现错误（剪贴板为空、无有效链接、API 请求失败、网络不通等），模板均会**静默跳过**，不会报错，只是对应字段保持为空，需要你手动填写。

此外，网址输入框还有一个**聚焦触发**的二次读取逻辑：当你**点击**网址输入框时（且该字段当前为空），模板会再次读取剪贴板。这样，如果你在弹窗已经打开后才复制了 B 站链接，只需点击网址框，链接和标题就会自动填入，而无需关闭弹窗重新触发。

---

## 7. 输出结果：笔记结构说明

确认插入后，模板会在当前笔记的**光标位置**插入以下结构：

```markdown
# 视频标题

\`\`\`dataviewjs
（完整的 DataviewJS 播放器代码）
\`\`\`

## Timestamps
```

具体说明如下：

**`# 视频标题`**：由你在弹窗中填写的标题生成，作为该视频笔记的一级标题。

> ⚠️ **注意（特定设置）**：此处使用一级标题（`#`）是模板的默认设计。如果你计划将视频嵌入块插入到已有笔记的某个章节中（而非新建专属笔记页面），一级标题可能与已有结构冲突。此时建议在插入后手动调整标题层级，或参考第 11 节的进阶用法修改模板的标题输出。

**`\`\`\`dataviewjs … \`\`\``**：核心播放器代码块。Dataview 插件会将其渲染为一个包含 iframe 播放器和控制栏的完整交互组件，详见第 8、9 节。

**`## Timestamps`**：时间戳列表的宿主区域。添加时间戳时，模板会将时间戳写入当前笔记文件，DataviewJS 则实时读取该文件中的时间戳行并渲染列表。`## Timestamps` 标题本身只是一个视觉分隔标记，时间戳实际上是普通的 Markdown 行，可以写在笔记文件的任意位置（只要与视频 ID 对应即可）。

> 💡 **说明（按需调整）**：`## Timestamps` 的标题文本本身对时间戳的识别没有影响——时间戳是根据行格式（`- [时间标签](链接含视频ID)`）来识别的，而非根据其所在标题。因此你可以将该标题改为中文（如 `## 时间戳`）或其他任何名称，不会影响功能。

---

## 8. 时间戳管理系统详解

时间戳是本模板功能设计中最具特色的部分，以下逐一说明各项操作。

### 8.1 时间戳的存储格式

时间戳以标准 Markdown 链接的形式存储在笔记文件中，格式如下：

```markdown
- [0:15](https://www.bilibili.com/video/BV1234567890?t=15)
- [1:03](https://www.bilibili.com/video/BV1234567890?t=63)
- [12:34](https://www.bilibili.com/video/BV1234567890?t=754)
```

其中，`0:15` 为时间标签（由模板自动生成），`?t=15` 参数为对应的秒数。这种格式是纯 Markdown，不依赖任何插件私有语法，在任何 Markdown 编辑器中都可正常阅读，点击链接也会直接在浏览器中以精准跳转方式打开 B 站视频。

### 8.2 添加时间戳

播放器控制栏中有一个 **"📍 添加时间戳 "** 按钮，点击后的行为取决于当前是否有已知的播放时间：

**情况 A：已知当前播放时间**——如果你此前已通过右键精准空降流程（见下文）记录了播放进度，或通过时间戳跳转过某个时间点，播放器内部会保存一个 " 已知时间 "。点击添加按钮时，直接以该时间生成时间戳，无需额外操作。

**情况 B：播放时间未知**——由于 B 站播放器通过 iframe 嵌入，受浏览器跨域限制，Obsidian 无法直接读取 iframe 内部的播放进度。此时点击添加按钮，会弹出一个引导弹窗，提示执行以下步骤：

1. 右键点击上方视频画面
2. 在右键菜单中选择「复制视频地址（精准空降）」
3. 返回此引导弹窗，点击「📋 粘贴确认」

这一设计利用了 B 站自身提供的 " 精准空降 " 功能——该选项会将含有当前时间参数（`?t=秒数`）的链接复制到剪贴板，模板读取剪贴板后提取时间参数，从而获得精确的播放进度。

> 💡 B 站的「复制视频地址（精准空降）」选项在 PC 版 Chrome/Edge 浏览器中右键视频画面可以看到。需要注意的是，该选项的文字在不同版本的 B 站网页中可能有细微差异。

时间戳写入位置的优先级：若当前笔记处于编辑模式且光标位于笔记中，时间戳会插入到光标所在位置；若笔记处于阅读模式或光标位置不明确，时间戳会追加到笔记文件末尾。

**重复时间戳保护**：若相同秒数的时间戳已存在，添加操作会取消并显示提示 `⚠️ 该时间点已存在时间戳`，避免产生重复记录。

### 8.3 时间戳列表与跳转

播放器下方的 **"⏱ 时间戳列表 "** 区域会实时显示当前笔记中属于该视频的所有时间戳，按时间升序排列。

点击任意时间戳按钮，播放器会立即跳转到对应时间点并开始播放。模板内部通过修改 iframe 的 `src` URL（在播放器地址后追加 `&t=秒数` 参数）实现定位，这是目前跨域条件下控制 B 站 iframe 播放位置最可靠的方式。

**激活高亮**：时间戳列表中，当前已跳转到（或最近一次已知播放位置对应）的时间戳按钮会以高亮样式显示，方便你在多个时间戳中快速定位当前进度所处区间。

### 8.4 删除时间戳

将鼠标悬停在任意时间戳按钮上，按钮右侧会滑出一个 **"×"** 删除图标（桌面端才有此悬浮效果）。点击 "×" 后，模板会读取笔记文件内容，精确删除对应时间戳行，并刷新列表。删除操作不可撤销，请谨慎操作（若误删，可立即按 `Ctrl+Z` 在 Obsidian 编辑器中撤销文件修改）。

### 8.5 复制全部时间戳

控制栏中的 **"📋 复制全部 "** 按钮会将当前视频的所有时间戳以链接格式复制到剪贴板，每条时间戳占一行，格式为 `[0:15](链接)`。复制后可粘贴到其他笔记、文档或消息中，分享给他人时对方也可直接点击链接跳转到对应时间点。

### 8.6 时间显示与手动输入

控制栏中有一个小型时间显示器（等宽字体，格式为 `0:00`），显示当前已知的播放时间。当时间未知时，显示为 `--:--`。

该显示器同时支持**手动输入时间**：点击后进入编辑模式（此时显示器边框会高亮），输入目标时间（支持 `秒`、`分:秒`、`时:分:秒` 三种格式），按 Enter 确认后播放器会立即跳转到该时间点；按 Esc 取消编辑，恢复原始显示。

---

## 9. 播放器交互功能说明

### 9.1 播放器渲染

视频播放器通过 iframe 渲染，嵌入的是 B 站官方提供的 `player.bilibili.com` 播放页面。

> ⚠️ **注意（特定设置）**：模板中使用的播放器 URL 包含以下参数，这些参数均为模板作者预设的推荐值，读者可根据需要进行调整：

| 参数 | 当前值 | 含义 | 替换建议 |
|------|--------|------|----------|
| `p` | `1` | 视频分 P 序号 | 如需嵌入多 P 视频的指定分集，修改为对应数字 |
| `as_wide` | `1` | 宽屏播放器 | 改为 `0` 可使用普通比例播放器 |
| `high_quality` | `1` | 自动最高画质 | 改为 `0` 可降低默认画质 |
| `danmaku` | `0` | 关闭弹幕 | 改为 `1` 开启弹幕 |
| `autoplay` | `0` | 禁用自动播放 | 改为 `1` 开启自动播放（不推荐） |

若需修改这些参数，请在附录代码中找到 `embedUrl` 的生成行并调整对应的参数值。

### 9.2 播放进度追踪限制

由于 B 站播放器与 Obsidian 运行在不同的安全域中，浏览器的跨域安全策略（CORS / same-origin policy）阻止了 Obsidian 直接读取 iframe 内部的 JavaScript 状态（包括当前播放时间）。

模板通过以下机制进行部分补偿：

**iframe 失焦检测**：当用户将鼠标点入 iframe 区域（即视频画面）时，Obsidian 窗口会失去焦点。模板检测到这一 `blur` 事件后，会清空已知的播放时间（将时间显示器重置为 `--:--`），提示你当前进度已不再可知，需要使用 " 精准空降 " 流程重新获取。

**跳转时间保存**：通过时间戳跳转或手动输入时间跳转后，目标时间会被保存为当前已知时间，后续添加时间戳时可直接使用，无需重新执行精准空降流程。

**跨笔记状态保持**：已知的播放时间存储在 `window` 全局对象中（以视频 ID 为键），这意味着在同一 Obsidian 会话内，即使你切换到其他笔记再切换回来，该视频的已知播放时间仍然保留，不会因视图刷新而丢失。

---

## 10. 常见问题排查

### 10.1 dataviewjs 代码块未渲染，显示为原始代码

**原因一**：Dataview 插件未安装或未启用。

**解决方法**：进入**设置 → 第三方插件**，确认 Dataview 已安装并开启。

**原因二**：Dataview 的 JavaScript 查询功能被禁用。

**解决方法**：进入 Dataview 插件设置，开启 **"Enable JavaScript Queries"** 选项。

---

### 10.2 视频标题和链接未自动预填

**原因一**：剪贴板中没有有效的 B 站视频链接。

**解决方法**：在浏览器地址栏复制当前 B 站视频页面的完整 URL，再触发模板，或在弹窗中手动填写。

**原因二**：B 站 API 请求失败（网络不通、API 临时限流、返回了错误格式）。

**解决方法**：链接字段通常仍会正常预填（链接提取不依赖 API），只有标题字段需要手动输入。网络恢复后再次触发模板即可自动获取标题。

**原因三**：在移动端使用，`request` 函数不可用。

**说明**：这是预期行为，非错误。移动端 API 调用被静默跳过，手动填写标题即可。

---

### 10.3 播放器显示但视频无法播放，提示需要登录

**原因**：部分 B 站视频设有大陆地区限定、会员专属或年龄验证等限制，iframe 嵌入时无法通过 B 站的登录验证。

**解决方法**：此限制源于 B 站平台策略，模板层面无法绕过。对于此类视频，建议在笔记中保留视频链接，仍可通过点击链接在浏览器中登录后观看，时间戳链接同样有效。

---

### 10.4 点击 " 添加时间戳 " 后弹出引导弹窗，但粘贴确认时提示 " 未找到时间参数 "

**原因**：右键菜单中复制的不是「复制视频地址（精准空降）」，而是「复制视频地址」（不含时间参数）。

**解决方法**：右键视频**画面区域**（非控制栏），确认选择的是含有 " 精准 " 或 " 空降 " 字样的选项。不同浏览器、不同版本的 B 站网页，右键菜单选项文字可能略有不同，但通常只有一个带时间参数的选项。

---

### 10.5 时间戳列表为空，但我确认已添加过时间戳

**原因**：时间戳被写入笔记文件的某个位置，但可能格式不完全符合模板的解析规则。

**解决方法**：切换到笔记的**编辑视图**（或使用 " 源码模式 "），手动查找类似 `- [0:15](https://www.bilibili.com/video/BV…)` 格式的行。常见问题包括：

- 链接末尾缺少右括号 `)`
- URL 中 `?t=` 参数格式有误（如写成 `&t=` 而整体链接不是正确 URL）
- 时间戳行前有多余空格或特殊字符，导致不以 `- [` 开头

若确认格式无误但列表仍为空，可尝试点击控制栏中的 **"🔄 刷新 "** 按钮手动刷新列表。

---

### 10.6 在移动端打开含有该模板输出的笔记，播放器显示正常但无法与其交互

**说明**：时间戳管理中的 " 精准空降 " 流程依赖于右键点击视频画面，在移动端无法实现。但通过时间戳列表点击跳转、手动输入时间的功能仍然可用。移动端的整体使用体验会比桌面端有所降低，详见第 13 节。

---

## 11. 进阶用法与个性化调整

### 11.1 修改视频播放器的宽高比

模板中播放器区域使用了 `padding-top: 56.25%` 实现 16:9 的自适应宽高比（这是标准的 CSS 百分比 padding 技巧）。如果你的视频是 4:3（如一些旧式录像）或竖屏格式，可以在附录代码的 CSS 部分找到 `.yr` 类并修改 `padding-top` 值：

- 16:9（横屏标准）：`56.25%`
- 4:3（旧式横屏）：`75%`
- 9:16（竖屏）：`177.78%`

### 11.2 修改弹窗标题文字

若你在库中维护多个视频来源的嵌入模板（如 YouTube、Vimeo 等），可以将弹窗标题行的文字从 `📺 添加哔哩哔哩视频` 修改为更具辨识度的名称，方便区分不同模板触发后的弹窗。

### 11.3 将标题层级改为二级标题

如果你习惯在已有笔记的某个章节下插入视频模块，而不是为每个视频单独创建一个笔记，可以将模板末尾 `tR` 赋值行的 `# ${tt}` 改为 `## ${tt}` 或 `### ${tt}`，避免破坏已有的标题层级结构。

> 💡 **说明（按需调整）**：修改标题层级只影响插入内容中的标题，不影响时间戳管理功能的任何行为。

### 11.4 在时间戳中附加文字说明

当前时间戳的标签格式为纯粹的时间（如 `0:15`）。如果你希望为每个时间戳附加说明文字（如 `0:15 | 定义介绍`），可以在插入时间戳后手动编辑笔记中的时间戳行，将标签部分（`- [这里](链接)`）修改为带说明的格式。

模板在解析时间戳时，提取的是 `](` 之前的全部内容作为标签，因此修改标签文字不会影响跳转功能，也不会影响时间戳列表的渲染。

### 11.5 创建一个 " 视频笔记 " 专属模板

如果你经常为 B 站视频创建独立笔记，可以创建一个更完整的 Obsidian 笔记模板，在其 Frontmatter 中预设 `source:`、`tags:`、`created:` 等字段，并在正文中调用本模板。通过 Templater 的 `tp.file.include()` 功能可以将两个模板串联使用，实现一次触发同时生成 Frontmatter 结构和视频嵌入块。

---

## 12. 与其他插件的联动方式

### 12.1 与 Dataview 插件联动（查询时间戳）

由于时间戳以标准 Markdown 链接形式存储在笔记文件中，你可以在任意笔记中使用 Dataview 查询语句，构建跨笔记的时间戳索引。例如，以下 DQL 查询列出指定文件夹下所有视频笔记：

```dataview
TABLE file.mtime AS "修改时间"
FROM "视频笔记"
SORT file.mtime DESC
```

> 💡 **说明（按需调整）**：上方查询中的 `"视频笔记"` 为示例文件夹名称，请替换为你实际存放视频笔记的文件夹路径。

### 12.2 与 QuickAdd 插件联动

可以使用 QuickAdd 的 Template 类型 Choice，为本模板绑定一个全局触发命令，并将其加入 QuickAdd 的命令菜单，统一管理所有快速操作入口。这样在 QuickAdd 命令菜单中选择视频嵌入，Templater 模板即可被调用。

### 12.3 与 Canvas 联动

将包含视频嵌入块的笔记嵌入 Obsidian Canvas 的笔记卡片中，可以在 Canvas 看板上直接预览和播放 B 站视频，适合构建以视频内容为核心的知识地图或项目看板。

### 12.4 与 Bases 或 Dataview 表格联动

如果你的视频笔记 Frontmatter 中包含结构化字段（如 `视频链接:`、`视频标题:`、`来源:`），可以配合 Obsidian Bases（或 Dataview TABLE 查询）构建视频资源库，以表格形式汇总所有已记录的视频，并通过链接跳转到对应的嵌入笔记。

---

## 13. 适用版本与平台说明

| 环境 | 要求 / 说明 |
|------|------------|
| Obsidian | 建议 1.4.0 及以上版本 |
| Templater 插件 | 建议使用最新版，模板使用了异步 `<%* %>` 语法，需要 Templater 支持异步执行 |
| Dataview 插件 | 建议使用最新版，需开启 "Enable JavaScript Queries" 选项 |
| Windows / macOS / Linux | ✅ 完整支持，所有功能均可正常使用 |
| iOS / Android 移动端 | ⚠️ 部分支持：视频播放和时间戳列表跳转可用；B 站 API 自动获取标题不可用（`request` 函数为桌面专属）；精准空降获取播放进度不可用（依赖右键菜单）；移动端播放器交互体验有限 |
| 网络连接 | 视频播放需要网络连接（访问 B 站播放器）；B 站 API 标题获取需要网络连接；时间戳管理（读写、跳转）在本地完成，不需要网络 |

> 💡 **关于移动端的补充说明**：虽然部分功能在移动端受限，但在桌面端录制好时间戳的笔记，在移动端仍可正常阅读和通过点击时间戳链接跳转（链接会在浏览器中打开 B 站视频并定位到对应时间）。移动端的主要限制在于**创建**阶段，一旦模板已经插入完毕，阅读和基本使用均不受影响。

---

## 附录

### 插入 - 视频嵌入模板 -bilibili 完整模板代码

**用途说明**：以下为完整的 Templater 模板文件内容。将其粘贴为一个新的 `.md` 文件，命名为 `插入-视频嵌入模板-bilibili.md`，放入 Templater 插件配置的模板文件夹中即可使用。模板执行时弹出输入对话框，接收视频标题与链接后，在当前笔记的光标位置插入包含视频标题、DataviewJS 播放器和时间戳列表区的标准化结构。

> ⚠️ **使用方法**：将下方代码块中的全部内容（从 `<%*` 到最后的 `%>` ）完整复制，粘贴到一个新建的 Markdown 文件中，确保文件内容仅包含模板代码，不含任何其他文字，保存后放入 Templater 的模板文件夹。

`````
<%*
let _initUrl = '', _initTitle = '';
const _biliReq = typeof request !== 'undefined' ? request : null;
try {
  const _clip = await navigator.clipboard.readText();
  const _bvM = /\/(BV[a-zA-Z0-9]+)/.exec(_clip);
  const _avM = /\/av(\d+)/.exec(_clip);
  const _vid = _bvM ? _bvM[1] : (_avM ? 'av' + _avM[1] : null);
  if (_vid) {
    _initUrl = 'https://www.bilibili.com/video/' + _vid;
    try {
      const _q = _vid.startsWith('BV') ? `bvid=${_vid}` : `aid=${_vid.slice(2)}`;
      if (_biliReq) {
        const _res = await _biliReq({ url: `https://api.bilibili.com/x/web-interface/view?${_q}`, method: 'GET' });
        const _j = JSON.parse(_res);
        _initTitle = _j.data?.title || '';
      }
    } catch(_) { _initTitle = ''; }
  }
} catch(_) {}

const result = await new Promise(resolve => {
  let settled = false;
  const finish = v => { if (settled) return; settled = true; document.removeEventListener('keydown', onEsc); ov.remove(); resolve(v); };
  const onEsc = e => { if (e.key === 'Escape') finish(null); };
  document.addEventListener('keydown', onEsc);

  const ov = document.createElement('div');
  ov.style.cssText = 'position:fixed;inset:0;background:rgba(0,0,0,0.55);z-index:99999;display:flex;align-items:center;justify-content:center;';
  ov.addEventListener('click', e => { if (e.target === ov) finish(null); });

  const dlg = document.createElement('div');
  dlg.style.cssText = 'background:var(--background-primary);border-radius:12px;padding:24px;width:420px;max-width:90vw;box-shadow:0 20px 60px rgba(0,0,0,0.5);';
  ov.appendChild(dlg);

  const hd = document.createElement('div');
  hd.textContent = '📺 添加哔哩哔哩视频';
  hd.style.cssText = 'font-size:16px;font-weight:700;color:var(--text-normal);margin-bottom:20px;';
  dlg.appendChild(hd);

  const makeField = (label, placeholder, value = '') => {
    const row = document.createElement('div');
    row.style.marginBottom = '14px';
    const lbl = document.createElement('div');
    lbl.textContent = label;
    lbl.style.cssText = 'font-size:11px;font-weight:700;color:var(--text-muted);text-transform:uppercase;letter-spacing:.5px;margin-bottom:5px;';
    const inp = document.createElement('input');
    inp.type = 'text'; inp.placeholder = placeholder; inp.value = value;
    inp.style.cssText = 'width:100%;padding:8px 10px;border-radius:6px;border:1.5px solid var(--background-modifier-border);background:var(--background-secondary);color:var(--text-normal);font-size:14px;box-sizing:border-box;outline:none;';
    inp.addEventListener('focus', () => inp.style.borderColor = 'var(--interactive-accent)');
    inp.addEventListener('blur',  () => inp.style.borderColor = 'var(--background-modifier-border)');
    row.append(lbl, inp); dlg.appendChild(row);
    return inp;
  };

  const titleInp = makeField('📝 视频标题', '请输入视频标题', _initTitle);
  const urlInp   = makeField('🔗 哔哩哔哩网址', 'https://www.bilibili.com/video/BV...', _initUrl);

  urlInp.addEventListener('focus', async () => {
    if (urlInp.value) return;
    try {
      const clip = await navigator.clipboard.readText();
      const bvM2 = /\/(BV[a-zA-Z0-9]+)/.exec(clip);
      const avM2 = /\/av(\d+)/.exec(clip);
      const vid2 = bvM2 ? bvM2[1] : (avM2 ? 'av' + avM2[1] : null);
      if (!vid2) return;
      urlInp.value = 'https://www.bilibili.com/video/' + vid2;
      if (!titleInp.value && _biliReq) {
        try {
          const q2 = vid2.startsWith('BV') ? `bvid=${vid2}` : `aid=${vid2.slice(2)}`;
          const res2 = await _biliReq({ url: `https://api.bilibili.com/x/web-interface/view?${q2}`, method: 'GET' });
          const j2 = JSON.parse(res2);
          if (j2.data?.title) titleInp.value = j2.data.title;
        } catch(_) {}
      }
    } catch(_) {}
  });

  const btnRow = document.createElement('div');
  btnRow.style.cssText = 'display:flex;gap:8px;justify-content:flex-end;';

  const cancelBtn = document.createElement('button');
  cancelBtn.textContent = '取消';
  cancelBtn.style.cssText = 'padding:6px 14px;border-radius:6px;border:1.5px solid var(--background-modifier-border);background:transparent;color:var(--text-muted);font-size:13px;cursor:pointer;';

  const okBtn = document.createElement('button');
  okBtn.textContent = '确认插入';
  okBtn.style.cssText = 'padding:6px 14px;border:none;border-radius:6px;background:var(--interactive-accent);color:var(--text-on-accent);font-size:13px;font-weight:600;cursor:pointer;';

  okBtn.addEventListener('click', () => finish({ title: titleInp.value.trim(), url: urlInp.value.trim() }));
  cancelBtn.addEventListener('click', () => finish(null));
  [titleInp, urlInp].forEach(el => el.addEventListener('keydown', e => { if (e.key === 'Enter') okBtn.click(); }));

  btnRow.append(cancelBtn, okBtn);
  dlg.appendChild(btnRow);
  document.body.appendChild(ov);
  setTimeout(() => (titleInp.value ? urlInp : titleInp).focus(), 60);
});

if (!result)       { new Notice('❌ 已取消'); return; }
if (!result.title) { new Notice('❌ 请输入视频标题'); return; }
if (!result.url)   { new Notice('❌ 请输入哔哩哔哩网址'); return; }
if (!/bilibili\.com\/video/.test(result.url)) { new Notice('❌ 不是有效的哔哩哔哩视频链接'); return; }

const bvM = /\/(BV[a-zA-Z0-9]+)/.exec(result.url);
const avM = /\/av(\d+)/.exec(result.url);
const vid = bvM ? bvM[1] : (avM ? 'av' + avM[1] : null);
if (!vid) { new Notice('❌ 无法解析视频 ID，请检查链接格式'); return; }

const q        = vid.startsWith('BV') ? `bvid=${vid}` : `aid=${vid.slice(2)}`;
const embedUrl = `https://player.bilibili.com/player.html?${q}&p=1&as_wide=1&high_quality=1&danmaku=0&autoplay=0`;
const linkPfx  = `https://www.bilibili.com/video/${vid}?t=`;
const tt  = result.title;
const BT3 = '```';

const dvLines =
`const VID="${vid}",BASE_SRC="${embedUrl}",LINK_PFX="${linkPfx}";
const BTK="bT_"+VID,BK="bB_"+VID;
const FILE=app.vault.getAbstractFileByPath(dv.current().file.path),NL=String.fromCharCode(10);
if(window[BK]){window.removeEventListener("blur",window[BK]);window[BK]=null;}
let bKnown=window[BTK]??null,currentTime=bKnown??0;
let tsList=[],infoTimer=null,timeEditing=false,timeEsc=false;

dv.container.createEl("style").textContent=".yw{display:flex;flex-direction:column;gap:12px;max-width:760px;padding:4px 0}.yr{position:relative;width:100%;padding-top:56.25%;background:#000;border-radius:10px;overflow:hidden}.yr iframe{position:absolute;top:0;left:0;width:100%;height:100%;border:none}.yc{display:flex;gap:8px;align-items:center;flex-wrap:wrap;margin-top:2px}.yb{padding:6px 14px;border-radius:6px;border:none;cursor:pointer;font-size:13px;font-weight:600}.yp{background:#e53935;color:#fff}.ys{background:var(--background-modifier-border);color:var(--text-normal)}.ytd{font-family:monospace;font-size:12px;color:var(--text-muted);background:var(--background-secondary);padding:2px 8px;border-radius:4px;border:1px solid transparent;min-width:6em;text-align:center;cursor:text;outline:none;transition:border-color .15s,color .15s}.ytd:focus{border-color:var(--interactive-accent);color:var(--text-normal)}.yi{font-size:12px;color:var(--text-muted)}.yh{font-size:13px;font-weight:700;color:var(--text-normal)}.yl{display:flex;flex-wrap:wrap;gap:6px;padding:10px;background:var(--background-secondary);border-radius:8px;min-height:38px;align-items:center}.ytbw{display:inline-flex;align-items:stretch;border-radius:5px;overflow:hidden}.ytb{padding:4px 11px;border:none;cursor:pointer;font-size:13px;font-family:monospace;background:var(--interactive-accent);color:var(--text-on-accent)}.ytb-active{box-shadow:inset 0 0 0 2px rgba(255,255,255,.45)}.ytx{width:0;padding:0;border:none;cursor:pointer;font-size:11px;background:var(--interactive-accent);color:var(--text-on-accent);opacity:0;overflow:hidden;transition:width .15s,opacity .15s,background .1s}.ytbw:hover .ytx{width:1.4em;padding:0 3px;opacity:.6}.ytbw:hover .ytx:hover{opacity:1;background:#c62828}.ye{font-size:12px;color:var(--text-muted)}";

const fmtTime=s=>{s=Math.floor(s);const h=Math.floor(s/3600),m=Math.floor((s%3600)/60),sc=s%60;return(h?h+":":"")+(m<10?"0":"")+m+":"+(sc<10?"0":"")+sc;};
const parseTime=raw=>{const p=raw.trim().split(":"),n=p.map(v=>parseInt(v,10));if(n.some(v=>isNaN(v)||v<0))return null;if(p.length===1)return n[0];if(p.length===2&&n[1]<60)return n[0]*60+n[1];if(p.length===3&&n[1]<60&&n[2]<60)return n[0]*3600+n[1]*60+n[2];return null;};
const displayTime=()=>bKnown!==null?fmtTime(bKnown):"--:--";
const updateActive=()=>{let a=-1;for(let i=0;i<tsList.length;i++){if(tsList[i].sec<=currentTime)a=i;else break;}tsList.forEach((ts,i)=>ts.btn.classList.toggle("ytb-active",i===a));};
const showInfo=msg=>{info.setText(msg);clearTimeout(infoTimer);infoTimer=setTimeout(()=>info.setText(""),2500);};
const setBTime=t=>{bKnown=t;window[BTK]=t;currentTime=t;};
const clearBTime=()=>{bKnown=null;window[BTK]=null;currentTime=0;if(!timeEditing)timeDisplay.value="--:--";};

const wrap=dv.container.createEl("div",{cls:"yw"});
const ratio=wrap.createEl("div",{cls:"yr"});
const iframeEl=ratio.createEl("iframe",{attr:{src:BASE_SRC,allow:"accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture",allowfullscreen:"",loading:"lazy"}});
const ctrl=wrap.createEl("div",{cls:"yc"});
const addBtn=ctrl.createEl("button",{text:"📍 添加时间戳",cls:"yb yp"});
const cpBtn=ctrl.createEl("button",{text:"📋 复制全部",cls:"yb ys"});
const refreshBtn=ctrl.createEl("button",{text:"🔄",cls:"yb ys",attr:{title:"刷新时间戳列表"}});
const timeDisplay=ctrl.createEl("input",{cls:"ytd",attr:{type:"text",value:"--:--",title:"手动输入时间后 Enter 跳转 · Esc 取消\\n支持：秒数 / MM:SS / H:MM:SS"}});
const info=ctrl.createEl("span",{cls:"yi"});
const headEl=wrap.createEl("div",{cls:"yh"});
const box=wrap.createEl("div",{cls:"yl"});

const seekTo=sec=>{setBTime(sec);if(!timeEditing)timeDisplay.value=fmtTime(sec);updateActive();const u=new URL(BASE_SRC);u.searchParams.set("t",sec);iframeEl.src=u.href;};

const blurH=()=>{setTimeout(()=>{if(document.activeElement===iframeEl)clearBTime();},50);};
window.addEventListener("blur",blurH);window[BK]=blurH;

iframeEl.addEventListener("load",()=>{
  try{if(!new URL(iframeEl.src).searchParams.has("t")&&bKnown!==null){const u=new URL(BASE_SRC);u.searchParams.set("t",bKnown);iframeEl.src=u.href;}}catch(_){}
});

timeDisplay.addEventListener("focus",()=>{timeEditing=true;if(timeDisplay.value==="--:--")timeDisplay.value="";timeDisplay.select();});
timeDisplay.addEventListener("blur",()=>{
  timeEditing=false;
  if(timeEsc){timeEsc=false;timeDisplay.value=displayTime();return;}
  const sec=parseTime(timeDisplay.value);
  if(sec!==null)seekTo(sec);else timeDisplay.value=displayTime();
});
timeDisplay.addEventListener("keydown",e=>{if(e.key==="Enter"){e.preventDefault();timeDisplay.blur();}if(e.key==="Escape"){timeEsc=true;timeDisplay.blur();}});

const getBilibiliTime=()=>new Promise(resolve=>{
  try{iframeEl.contentWindow.postMessage(JSON.stringify({event:"web_command",func:"pause"}),"*");}catch(_){}
  let settled=false;
  const done=v=>{if(settled)return;settled=true;document.removeEventListener("keydown",onEsc);ov.remove();resolve(v);};
  const onEsc=e=>{if(e.key==="Escape")done(null);};
  document.addEventListener("keydown",onEsc);
  const mk=(tag,css,txt)=>{const el=document.createElement(tag);if(css)el.style.cssText=css;if(txt)el.textContent=txt;return el;};
  const ov=mk("div","position:fixed;inset:0;background:rgba(0,0,0,.5);z-index:99999;display:flex;align-items:center;justify-content:center;");
  ov.addEventListener("click",e=>{if(e.target===ov)done(null);});
  const dlg=mk("div","background:var(--background-primary);border-radius:12px;padding:24px 26px;width:360px;max-width:90vw;box-shadow:0 20px 60px rgba(0,0,0,.5);");
  ov.appendChild(dlg);
  dlg.appendChild(mk("div","font-size:15px;font-weight:700;color:var(--text-normal);margin-bottom:14px;","📍 获取 B站当前时间"));
  const steps=mk("div","margin-bottom:16px;");
  ["① 右键点击上方视频画面","② 选择「复制视频地址(精准空降)」","③ 返回此处，点击「粘贴确认」"].forEach(s=>steps.appendChild(mk("div","font-size:13px;color:var(--text-muted);line-height:2;",s)));
  dlg.appendChild(steps);
  const errEl=mk("div","font-size:12px;color:var(--color-red,#f44336);min-height:1.2em;margin-bottom:10px;");
  dlg.appendChild(errEl);
  const btnRow=mk("div","display:flex;gap:8px;justify-content:flex-end;");
  const cancelBtn=mk("button","padding:6px 14px;border-radius:6px;border:1.5px solid var(--background-modifier-border);background:transparent;color:var(--text-muted);font-size:13px;cursor:pointer;","取消");
  cancelBtn.onclick=()=>done(null);
  const okBtn=mk("button","padding:6px 14px;border:none;border-radius:6px;background:var(--interactive-accent);color:var(--text-on-accent);font-size:13px;font-weight:600;cursor:pointer;","📋 粘贴确认");
  okBtn.onclick=async()=>{
    errEl.textContent="";
    try{
      const clip=(await navigator.clipboard.readText()).trim();
      let t=null;try{t=new URL(clip).searchParams.get("t");}catch(_){}
      if(t===null){errEl.textContent="⚠️ 未找到时间参数，请确认已选择「精准空降」";return;}
      const sec=Math.floor(parseFloat(t));
      if(!isFinite(sec)||sec<0){errEl.textContent="⚠️ 时间解析失败，请重试";return;}
      done(sec);
    }catch(_){errEl.textContent="⚠️ 读取剪贴板失败，请重试";}
  };
  btnRow.append(cancelBtn,okBtn);dlg.appendChild(btnRow);
  document.body.appendChild(ov);setTimeout(()=>okBtn.focus(),60);
});

const getTS=async()=>{
  if(!FILE)return[];
  const out=[];
  for(const ln of(await app.vault.read(FILE)).split(NL)){
    if(!ln.startsWith("- ["))continue;
    const ci=ln.indexOf("](");if(ci<4||!ln.includes(VID,ci))continue;
    const tm=/[?&]t=(\\d+)[)]/.exec(ln.slice(ci));if(tm)out.push({label:ln.slice(3,ci),sec:+tm[1]});
  }
  return out.sort((a,b)=>a.sec-b.sec);
};

const deleteTS=async sec=>{
  if(!FILE)return;
  const c=await app.vault.read(FILE);
  const n=c.split(NL).filter(ln=>!(ln.startsWith("- [")&&ln.includes(VID)&&ln.trimEnd().endsWith("t="+sec+")"))).join(NL);
  if(n!==c)await app.vault.modify(FILE,n);
};

const renderTS=async()=>{
  box.empty();tsList=[];
  const list=await getTS();
  headEl.textContent=list.length?"⏱ 时间戳列表（"+list.length+" 个 · 点击跳转）":"⏱ 时间戳列表";
  if(!list.length){box.createEl("span",{text:"暂无时间戳，点击「📍 添加时间戳」开始记录",cls:"ye"});return;}
  list.forEach(ts=>{
    const g=box.createEl("span",{cls:"ytbw"});
    const b=g.createEl("button",{text:"⏱ "+ts.label,cls:"ytb",attr:{title:"跳转到 "+ts.label}});
    b.onclick=()=>seekTo(ts.sec);
    tsList.push({sec:ts.sec,btn:b});
    const x=g.createEl("button",{text:"×",cls:"ytx",attr:{title:"删除此时间戳"}});
    x.onclick=async()=>{if(x.disabled)return;x.disabled=true;try{await deleteTS(ts.sec);await renderTS();}finally{x.disabled=false;}};
  });
  updateActive();
};

addBtn.onclick=async()=>{
  if(addBtn.disabled||!FILE)return;addBtn.disabled=true;
  try{
    let sec;
    if(bKnown!==null){
      sec=bKnown;
    }else{
      sec=await getBilibiliTime();
      if(sec===null)return;
      setBTime(sec);timeDisplay.value=fmtTime(sec);updateActive();
    }
    const existing=await getTS();
    if(existing.some(t=>t.sec===sec)){showInfo("⚠️ 该时间点已存在时间戳");return;}
    const lbl=fmtTime(sec),line="- ["+lbl+"]("+LINK_PFX+sec+")";
    const ae=app.workspace.activeEditor;
    if(ae?.editor){ae.editor.replaceRange(line+NL,ae.editor.getCursor());showInfo("✅ 已在光标处插入 "+lbl);}
    else{await app.vault.modify(FILE,(await app.vault.read(FILE))+NL+line);showInfo("✅ 已追加 "+lbl);}
    await renderTS();
  }finally{addBtn.disabled=false;}
};

cpBtn.onclick=async()=>{
  const list=await getTS();
  if(!list.length){showInfo("⚠️ 暂无时间戳");return;}
  await navigator.clipboard.writeText(list.map(t=>"["+t.label+"]("+LINK_PFX+t.sec+")").join(NL));
  showInfo("📋 已复制 "+list.length+" 条时间戳");
};

refreshBtn.onclick=()=>renderTS();
await renderTS();`;

tR = `# ${tt}

${BT3}dataviewjs
${dvLines}
${BT3}

## Timestamps
`;
%>

`````

> 📎 **获取完整代码**
>
> 1. 以上即为完整代码，可直接复制使用。
> 2. 关注微信公众号：**Obsidianist**，回复关键词：**视频嵌入模板-bilibili**，后台即会自动回复完整代码文件下载地址，下载后直接放入 Templater 模板文件夹，无需任何额外配置，即可使用。
