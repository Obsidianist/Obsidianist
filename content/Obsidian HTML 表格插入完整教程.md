---
tags:
  - Obsidian/表格
---

# Obsidian HTML 表格插入完整教程

Obsidian 原生的 Markdown 表格语法简洁，但一旦涉及合并单元格、复杂行列结构，它就彻底失效了——Markdown 表格不支持合并单元格。遇到这类需求，绕不开的解决路径是直接在笔记中插入 HTML 表格代码。

然而，手写 HTML 表格既繁琐又容易出错，样式也难以统一。[TablesGenerator](https://www.tablesgenerator.com/html_tables) 是一个免费的在线表格编辑器，能够以可视化方式编辑任意结构的表格，并导出标准的 HTML 代码。将它的输出粘贴进 Obsidian，就能在笔记中渲染出支持合并单元格的富结构表格。

本文介绍两套方案：

**方案一（基础版）**：仅依赖 TablesGenerator 网站，将生成的 HTML 代码直接粘贴进 Obsidian，无需安装任何额外插件或 CSS。适合偶尔有表格需求、不想折腾配置的用户。

**方案二（完整版）**：在方案一的基础上，额外添加一个 CSS 代码片段和一个 Templater 模板。CSS 负责让表格在 Obsidian 中呈现统一、精致的视觉效果；模板负责将 TablesGenerator 输出的乱码类名一键格式化为标准类名，并自动清除多余的 `<style>` 标签。适合频繁使用表格、追求整洁代码和一致样式的用户。

---

## 目录

1. [为什么不用表格插件](Obsidian%20HTML%20表格插入完整教程.md#1.%20为什么不用表格插件)
2. [TablesGenerator 使用要点](Obsidian%20HTML%20表格插入完整教程.md#2.%20TablesGenerator%20使用要点)
3. [方案一：直接插入 HTML 表格](Obsidian%20HTML%20表格插入完整教程.md#3.%20方案一：直接插入%20HTML%20表格)
4. [方案二前提：所需插件与文件](Obsidian%20HTML%20表格插入完整教程.md#4.%20方案二前提：所需插件与文件)
5. [安装 CSS 代码片段](Obsidian%20HTML%20表格插入完整教程.md#5.%20安装%20CSS%20代码片段)
6. [安装 Templater 模板](Obsidian%20HTML%20表格插入完整教程.md#6.%20安装%20Templater%20模板)
7. [方案二完整操作流程](Obsidian%20HTML%20表格插入完整教程.md#7.%20方案二完整操作流程)
8. [格式化模板弹窗详解](Obsidian%20HTML%20表格插入完整教程.md#8.%20格式化模板弹窗详解)
9. [CSS 样式功能说明](Obsidian%20HTML%20表格插入完整教程.md#9.%20CSS%20样式功能说明)
10. [常见问题与注意事项](Obsidian%20HTML%20表格插入完整教程.md#10.%20常见问题与注意事项)
11. [进阶用法](Obsidian%20HTML%20表格插入完整教程.md#11.%20进阶用法)

---

## 1. 为什么不用表格插件

处理 Obsidian 表格问题时，很多人的第一反应是去社区插件市场搜一个。这个想法完全合理，毕竟插件市场里确实不乏针对表格场景的解决方案，其中不少下载量相当可观。但在真正动手安装之前，值得先想清楚一个问题：这些插件究竟解决了什么问题，又带来了什么代价？

### 1.1 主流表格插件一览

以下是目前下载量排名靠前的表格相关插件，涵盖了从辅助编辑到功能增强的各类方向：

**Advanced Tables**（总下载量约 273 万，历史最高）：最受欢迎的表格类插件，通过 Tab/Enter 导航让 Markdown 表格的编辑体验接近电子表格，支持列对齐、简单公式和 CSV 导出。核心能力是让 Markdown 表格写起来更顺手，但底层仍然是 Markdown 语法，合并单元格依然无解。

**Table Generator**（2024 年下载量排名第 5）：以类似 Typora 的卡片式界面快速生成 Markdown 表格，解决的是表格创建的便利性问题，功能上仍受限于 Markdown 表格本身的天花板。

**Database Folder（DB Folder）**：以文件夹为数据库，在 Obsidian 中实现类 Notion 的数据库视图，适合管理大量结构化笔记，数据以 YAML frontmatter 形式存储在各笔记文件中。功能强大，学习曲线陡峭，更像数据库工具，而非普通表格。

**Notion-Like Tables（DataLoom 的继任者）**：高度仿照 Notion 的数据库表格，支持多种视图和属性类型，数据存储在独立的 JSON 文件中，与普通 Markdown 笔记分离。

**Table Extended**：通过引入 MultiMarkdown 表格语法，在不使用 HTML 的前提下支持合并单元格、多表头等结构。方向与本文最接近，但依赖非标准语法，且需持续依赖插件维护来保证渲染。

**Enhanced Tables**：为标准 Markdown 表格叠加排序、筛选、分页、自定义列类型等功能，以 YAML 配置块控制行为，适合需要轻量级交互的表格场景。

**CSV Table**：将 CSV 文件渲染为表格视图，支持筛选和排序，数据本体在外部 CSV 文件而非笔记内容中。

**Excel to Markdown Table**：将从 Excel、Google Sheets、Apple Numbers 等工具复制的数据一键粘贴为 Markdown 表格，解决的是数据导入效率问题。

**Sortable**：为阅读视图下的 Markdown 表格表头添加点击排序功能，轻量但功能单一。

**Sheets Extended**：在 Advanced Tables 基础上扩展单元格合并、垂直表头、行列样式等功能，是少数真正尝试解决 Markdown 合并单元格问题的插件之一。

### 1.2 插件方案的共同局限

逐一看完这个列表，可以发现一个规律：越是想要在 Obsidian 中实现复杂表格结构，就越需要依赖更重、更专有的方案。而这些方案，都不同程度地带来以下几类问题。

**合并单元格是绕不过的硬伤。** Advanced Tables、Table Generator、Sortable、Enhanced Tables、CSV Table 等插件都建立在标准 Markdown 表格语法之上，而 Markdown 表格从规范层面就不支持 `rowspan` 和 `colspan`。这不是插件开发者能绕过的限制，而是底层格式的根本缺陷。Table Extended 和 Sheets Extended 虽然做了尝试，但都引入了非标准语法，脱离对应插件后，这些表格在任何其他 Markdown 环境下都会渲染为乱码。

**数据存储方式影响可迁移性。** Notion-Like Tables 将数据存在独立的 JSON 文件里，DB Folder 依赖 frontmatter 和特定的文件夹结构，CSV Table 的数据在外部文件中。一旦插件停止维护或你决定更换工具，这些数据的迁移成本远高于普通的 Markdown 笔记。即便是相对克制的 Enhanced Tables，也需要 YAML 配置块才能让表格正常显示，脱离插件后配置块变成普通代码块，功能全部失效。

**插件存在停更风险，且无从预判。** Obsidian 的社区插件由第三方开发者维护，没有任何停更通知机制。历史上确实出现过广受欢迎的插件突然停止维护的案例——甚至 Advanced Tables 在 Obsidian 官方推出原生表格编辑功能（1.5.x）后，社区也曾公开讨论该插件是否还有必要继续使用。一旦你在大量笔记中深度依赖某个插件的特殊语法或存储格式，停更就意味着潜在的维护负担或数据迁移风险。

**插件叠加加重了库的维护负担。** 每一个社区插件都需要在 Obsidian 版本更新时保持兼容性，也可能与其他插件产生冲突。表格插件普遍会接管编辑器的 Tab/Enter 键行为，这在多插件共存时尤其容易出现意外。

### 1.3 HTML 表格方案的核心优势

相比之下，本文介绍的 HTML + TablesGenerator + 可选 CSS 与 Templater 方案，在以下几个维度具备插件方案难以匹敌的优势。

**原生支持合并单元格，这是最根本的差异。** HTML 表格通过 `rowspan` 和 `colspan` 属性原生支持任意的单元格合并，Obsidian 的 WebView 直接渲染标准 HTML，无需任何插件介入。这不是一个权宜之计，而是 Web 技术几十年来经过充分验证的标准能力。

**格式完全通用，可迁移性极强。** HTML 是所有现代 Markdown 渲染器的超集——无论是 Obsidian、Logseq、Typora、Notion，还是 GitHub README、个人博客、静态网站，HTML 表格都能直接渲染。将来某天你决定迁移到其他工具，笔记里的 HTML 表格不需要做任何修改，原样可用。而那些依赖特殊语法或专有格式的插件方案，一旦离开 Obsidian 就可能面目全非。

**数据完全自主，存储在仓库内部。** HTML 表格是笔记正文的一部分，就是普通的文本，和其他 Markdown 内容没有任何区别。这意味着它天然兼容所有基于文件的同步方案——iCloud、Syncthing、OneDrive、Git——多设备之间同步与备份的逻辑和同步普通笔记完全一样，不需要任何额外配置。

**永久可用，不依赖任何第三方维护。** 本方案在核心层面（HTML 表格本身）不依赖任何社区插件。方案二引入的 Templater 只是辅助格式化工具，且 Templater 是 Obsidian 生态中维护最稳定、更新最活跃的插件之一；CSS 代码片段是 Obsidian 的内置功能，不依赖插件市场。表格的渲染能力由 Obsidian 的 WebView 内核保证，这是官方基础设施，稳定性远高于任何社区插件。

**学习成本一次性，知识终身有效。** HTML 表格语法是 Web 开发的基础知识，学会之后不会因为任何软件更新而失效。TablesGenerator 的操作几分钟就能上手，且具备可视化界面，不需要手写任何 HTML 代码。相比之下，各种插件各有一套独立的操作逻辑，换一个插件就需要重新学习。

**样式统一，全库一键控制。** 方案二的 CSS 代码片段对整个库生效，所有带 `tg` 类名的表格都会自动应用统一样式，并且完全跟随 Obsidian 的主题 CSS 变量，在深色和浅色模式下无需任何额外配置。与之相比，插件方案通常各有一套自己的样式体系，跨插件实现统一的视觉风格往往比较困难。

**对库的侵入性极低。** 方案一完全零插件，方案二只新增一个 CSS 代码片段和一个 Templater 模板文件，仅此而已。Templater 本身也是许多 Obsidian 用户的基础设施级插件，如果你已经在使用它，方案二对库的影响几乎可以忽略不计。

当然，这个方案也不是万能的。如果你需要的是动态数据库、跨笔记查询、实时筛选排序，那么 Dataview、DB Folder 等插件是更合适的工具，它们解决的是完全不同的问题。但如果你的需求是**在笔记内部插入一张结构清晰、样式统一、可包含合并单元格的表格**，HTML 方案在各个维度都优于任何现有插件。

---

## 2. TablesGenerator 使用要点

无论选择哪套方案，以下几点在使用 TablesGenerator 时都需要注意。

**一定要选择 HTML 标签页。** TablesGenerator 支持多种格式（LaTeX、HTML、Markdown 等），务必点击顶部的 **"HTML"** 标签页进行操作，而非默认的 LaTeX 或其他格式。

**字体选择 Arial。** 在 TablesGenerator 的字体设置中选择 **Arial**，避免引入其他字体声明干扰 Obsidian 的字体渲染。

**不要勾选 "Do not generate CSS"。** 这个选项如果被勾选，输出的 HTML 将没有任何样式类，方案二的格式化模板将无法正常工作。保持此选项的**默认未勾选状态**。

**推荐勾选以下两项**（位于代码导出区下方）：

- **`Make table responsive`**：让表格在窄屏下可以横向滚动，防止内容溢出。
- **`First row is a table header`**：将第一行标记为 `<thead>`，作为表头。若不勾选此项，某些情况下可能导致单元格合并出现显示错误。

**导入已有表格**：如果你想在 TablesGenerator 中修改一张已有的表格，可以点击菜单 **File → Paste table data**，在弹出的对话框中粘贴已有的 HTML、Markdown、LaTeX 或其他格式的表格代码，TablesGenerator 会自动识别并导入。

---

## 3. 方案一：直接插入 HTML 表格

如果你只是偶尔需要插入一张结构复杂的表格，最简单的方法是：

**第一步：在 TablesGenerator 编辑表格。** 打开 [TablesGenerator HTML 标签页](https://www.tablesgenerator.com/html_tables)，在可视化界面中编辑你的表格——合并单元格、添加内容、调整行列数量等操作均可在此完成。

**第二步：导出 HTML 代码。** 点击编辑区下方的 **"Generate"** 按钮，再点击 **"Copy to clipboard"**，将生成的 HTML 代码复制到剪贴板。若复制按钮无响应，直接手动选中代码框中的全部内容复制即可。

**第三步：粘贴到 Obsidian 笔记。** 在 Obsidian 中打开目标笔记，将光标定位到希望插入表格的位置，直接粘贴（`Ctrl/Cmd+V`）。

> ⚠️ **注意**
> Obsidian 在**实时预览**（Live Preview）或**阅读视图**下才会渲染 HTML 表格。若粘贴后看到的是原始 HTML 代码，请切换到阅读视图，或确认当前模式为实时预览而非源码模式（Source Mode）。

**方案一的局限性**：TablesGenerator 生成的代码包含一个 `<style>` 标签，其中定义了若干形如 `.tg-abcd1234` 的随机类名，以及配套的内联样式。这些样式是一次性的，类名是随机生成的，每次导出都不同。大量笔记累积下来，这些冗余的 `<style>` 块会让代码难以维护，视觉样式也无法跨笔记统一。

如果你对这些问题不在意，方案一完全够用。如果希望代码整洁、样式统一，请继续阅读方案二。

---

## 4. 方案二前提：所需插件与文件

方案二需要在 Obsidian 中完成以下准备工作：

**Obsidian 版本**：建议使用 1.0 及以上版本。

**Templater 插件**：这是运行格式化模板的核心依赖。若尚未安装，在 Obsidian 的「社区插件」页面搜索「Templater」安装（作者 SilentVoid13），安装后在插件设置中确认「Enable Templater」已开启。

**Templater 模板文件夹**：Templater 需要一个专用文件夹存放模板文件。若尚未配置，进入「设置 → Templater → Template folder location」，填写你用于存放模板的文件夹路径。

**两个文件**：

- `table-tg-html.css`：CSS 代码片段，负责表格的视觉样式。详见第五节。
- `格式化-表格代码格式化模板.md`：Templater 模板，负责一键格式化 TablesGenerator 的输出。详见第六节。

两个文件的完整代码均放置于本文末尾附录，可按需前往复制。

---

## 5. 安装 CSS 代码片段

CSS 代码片段是 Obsidian 的内置功能，无需额外插件即可使用。

**第一步：找到代码片段文件夹。** 进入「设置 → 外观 → CSS 代码片段」，点击右侧的文件夹图标，打开 CSS 代码片段所在的文件夹（通常位于你的库文件夹下的 `.obsidian/snippets/`）。

**第二步：放置 CSS 文件。** 将 `table-tg-html.css` 文件复制到上述文件夹中。完整 CSS 代码见本文末尾**附录 A**。

> 📎 **获取完整代码**
>
> 1. 为了提升阅读体验，完整代码已放置于本文末尾**附录**部分，可直接前往复制使用。
> 2. 关注微信公众号：**Obsidianist**，回复关键词：**HTML 表格**，后台即会自动回复完整代码文件下载地址，下载后放入库根目录 `.obsidian/snippets` 文件夹，在「外观」设置中启用对应片段即可。

**第三步：启用代码片段。** 回到 Obsidian 的「设置 → 外观 → CSS 代码片段」，点击右侧的刷新图标让 Obsidian 识别新文件，然后找到 `table-tg-html` 并将其右侧的开关拨到开启状态。

> 💡 **说明（按需调整）**
> CSS 文件名 `table-tg-html` 是本方案的示例命名，可以根据个人习惯重命名，功能不受影响。Obsidian 会识别 `.obsidian/snippets/` 文件夹内所有以 `.css` 结尾的文件。

---

## 6. 安装 Templater 模板

**第一步：放置模板文件。** 将 `格式化-表格代码格式化模板.md` 放入你在 Templater 设置中指定的模板文件夹内。完整模板代码见本文末尾**附录 B**。

> 📎 **获取完整代码**
>
> 1. 为了提升阅读体验，完整代码已放置于本文末尾**附录**部分，可直接前往复制使用。
> 2. 关注微信公众号：**Obsidianist**，回复关键词：**HTML 表格**，后台即会自动回复完整代码文件下载地址，下载后直接放入 Templater 模板文件夹，无需任何额外配置，即可使用。

> 💡 **说明（按需调整）**
> 模板文件夹路径因库而异，请将文件放入你自己在 Templater 设置中指定的文件夹，而非固定路径。文件名也可以按照个人习惯修改，Templater 会自动识别模板文件夹内所有的 `.md` 文件作为可用模板。
>
> | 项目 | 示例值 | 替换建议 |
> |------|--------|---------|
> | 模板文件夹 | `Templates/` | 替换为你在 Templater 中实际配置的模板文件夹路径 |
> | 模板文件名 | `格式化-表格代码格式化模板.md` | 可重命名为任意你习惯的名称 |

**第二步（可选）：绑定快捷键。** 进入「设置 → 快捷键」，搜索「Templater: Insert Template」，绑定你偏好的组合键（例如 `Alt+T`）。绑定快捷键后，触发模板时无需经过命令面板，操作更流畅。

---

## 7. 方案二完整操作流程

配置完成后，完整的使用流程如下：

**第一步：在 TablesGenerator 编辑并导出表格。** 参考第二节的要点，在 TablesGenerator 的 HTML 标签页中编辑好表格，点击 **"Generate"** → **"Copy to clipboard"**，复制生成的 HTML 代码。

**第二步：粘贴到 Obsidian 笔记。** 在 Obsidian 中新建或打开一个笔记，将刚才复制的 HTML 代码粘贴进去。此时笔记中会出现从 `<style` 开始到 `</table>` 结束的完整 HTML 代码块。

**第三步：选中全部 HTML 代码。** 用鼠标或键盘选中刚才粘贴的所有内容，从 `<style` 开头一直选到 `</table>` 结尾，确保选中范围完整。

> ⚠️ **注意**
> 格式化模板会读取所选文本的内容进行处理。若没有选中任何内容就触发模板，模板会弹出提示「请先选中 Tables Generator 生成的 HTML 代码，再插入此模板！」并中止执行。

**第四步：触发格式化模板。** 通过以下任一方式触发模板：

- 点击左侧边栏的 Templater 图标，在弹出的模板列表中选择「格式化 - 表格代码格式化模板」
- 使用你绑定的快捷键，在模板列表中选择该模板
- 打开命令面板（`Ctrl/Cmd+P`），输入「Templater」，选择「Insert Template」，再选择该模板

**第五步：在弹窗中完成设置。** 模板触发后会弹出一个设置对话框，可以选择是否添加表格标题、是否启用响应式滚动、固定表头等功能（详见下节说明）。确认设置后，点击「确认（Ctrl+Enter）」，或直接按 `Ctrl+Enter`。

**第六步：查看格式化结果。** 模板执行完毕后，原来的 HTML 代码会被替换为格式化后的版本：`<style>` 标签消失，随机类名（如 `tg-abcd1234`）替换为规范的语义类名（如 `tg-center-top`），代码整洁可读。

**第七步：将表格代码放置到目标位置。** 如果你是先把代码粘贴到临时笔记中再格式化的，将格式化后的代码复制，粘贴到最终希望插入的笔记位置即可。

---

## 8. 格式化模板弹窗详解

触发模板后弹出的设置对话框包含以下几个部分：

### 8.1 自动检测提示

弹窗顶部会显示一行提示，说明模板从选中的代码中自动检测到哪些特性。例如，若 TablesGenerator 勾选了「Make table responsive」，模板会检测到并在提示中显示「响应式」，同时在下方的对应选项中预先设置为「启用」。

这个自动检测机制省去了手动判断的麻烦，但你也可以在下方手动调整任意选项，自动检测结果只是预设值，不是锁定值。

### 8.2 表格标题输入框

位于弹窗上方。在此输入文字，模板会在 `<table>` 标签内部的顶部插入一个 `<caption>` 元素，作为表格的可见标题——通常以加粗、居中的样式显示在表格上方。

支持多行粘贴。若留空，则不插入 `<caption>`。

### 8.3 功能选项

以网格布局排列，每项均提供「禁用」和「启用」两个选项：

**水平居中 `.tg-center`**：为 `<table>` 标签添加 `tg-center` 类，令表格在页面中水平居中显示（通过 `margin: auto` 实现），适用于内容较少、表格宽度不足以撑满整行的场景。

**响应式滚动 `.tg-wrapper`**：在 `<table>` 外层包裹一个 `<div class="tg-wrapper">`，当表格宽度超出笔记区域时，允许横向滚动而不会挤压或溢出。内容较多的宽表格建议启用此项。

**可排序表头 `.tg-sort`**：为 `<table>` 添加 `tg-sort` 类，表头单元格会显示可点击的排序箭头图标。

> ⚠️ **注意**
> `.tg-sort` 仅提供视觉提示（排序箭头），实际的点击排序功能需要配合社区插件 **Sortable** 实现。若未安装该插件，点击表头不会有排序效果。模板在检测到排序功能时会弹出一条 Notice 提醒安装 Sortable 插件。

**固定表头 `.tg-sticky`**：为表格添加 `tg-sticky` 类，表头行在表格内滚动时保持固定在顶部。适用于行数较多的长表格。固定表头的默认最大高度为 400px，超出部分可滚动。

> 💡 **说明**
> 若同时启用「固定表头」和「响应式滚动」，模板会使用 `tg-wrapper tg-sticky-wrapper` 复合类名，并弹出 Notice 提示：若需调整表格最大高度，可在 CSS 代码片段中添加自定义变量：
>
> ```css
> :root { --tg-sticky-max-height: 600px; }
> ```

**键盘导航 tabindex（WCAG）**：为每个 `<td>` 和 `<th>` 添加 `tabindex="0"` 属性，使表格单元格可通过键盘的 Tab 键依次聚焦，符合 WCAG 无障碍访问规范。默认关闭，有无障碍需求时可启用。

### 8.4 确认与取消

点击「确认（Ctrl+Enter）」或按 `Ctrl+Enter` 执行格式化；点击「取消（Esc）」或按 `Esc` 关闭弹窗并保留原始代码不做任何修改。

---

## 9. CSS 样式功能说明

`table-tg-html.css` 代码片段启用后，会为所有带有 `tg` 类名的 HTML 表格提供统一的视觉样式，完全适配 Obsidian 的主题变量（CSS Variables），在深色模式和浅色模式下均能正常显示。

样式体系的核心是一套标准化的单元格对齐类名，格式为 `tg-{水平对齐}-{垂直对齐}`，共九个：

| 类名 | 水平对齐 | 垂直对齐 |
|------|---------|---------|
| `tg-left-top` | 左 | 上 |
| `tg-center-top` | 居中 | 上 |
| `tg-right-top` | 右 | 上 |
| `tg-left-middle` | 左 | 中 |
| `tg-center-middle` | 居中 | 中 |
| `tg-right-middle` | 右 | 中 |
| `tg-left-bottom` | 左 | 下 |
| `tg-center-bottom` | 居中 | 下 |
| `tg-right-bottom` | 右 | 下 |

格式化模板在处理代码时，会读取 `<style>` 块中每个随机类名所定义的 `text-align` 和 `vertical-align` 值，自动将其映射到上述九个标准类名之一。因此，无论 TablesGenerator 每次生成什么样的随机类名，最终在 Obsidian 中看到的类名都是规范、一致、可读的。

此外，CSS 还包含以下样式规则：

- **表格整体**：边框折叠，宽度 100%，边框颜色跟随 Obsidian 主题变量 `--background-modifier-border`
- **单元格**：统一内边距（10px 8px），字体跟随 Obsidian 文本字体变量 `--font-text`，支持长文本自动换行
- **表头（`<thead>`）**：加粗，下方有双线分隔，文字不换行
- **行标题（`scope="row"` 的 `<th>`）**：半加粗，左对齐，区别于列标题的样式
- **表尾（`<tfoot>`）**：上方双线分隔，文字斜体，颜色略浅
- **表格标题（`<caption>`）**：居中加粗，显示在表格上方
- **打印支持**：包含 `@media print` 规则，确保打印时表头在每页顶部重复出现，单元格不被分页截断

---

## 10. 常见问题与注意事项

**粘贴后表格没有渲染，显示的是原始 HTML 代码**

Obsidian 的源码模式（Source Mode）不渲染 HTML，切换到实时预览或阅读视图即可。

**格式化后表格样式丢失，单元格全部左对齐**

最常见的原因是 CSS 代码片段未正确安装或未启用。检查「设置 → 外观 → CSS 代码片段」，确认 `table-tg-html` 已开启。也可以尝试点击刷新图标让 Obsidian 重新加载代码片段。

**合并单元格（跨行/跨列）在 Obsidian 中显示错乱**

在 TablesGenerator 中确认已勾选 **`First row is a table header`**。该选项会正确生成 `<thead>` 和 `<tbody>` 结构，Obsidian 的 HTML 渲染器依赖这个结构来正确处理单元格合并。

**格式化模板弹窗没有出现，直接插入了原始 Templater 代码**

这意味着 Templater 的 JavaScript 执行功能没有正常运行。进入「设置 → Templater」，确认「Enable Templater」已开启。若首次使用 Templater，Obsidian 可能弹出安全提示，需手动确认允许执行脚本。

**启用 `.tg-sort` 后点击表头没有排序效果**

`.tg-sort` 仅提供排序箭头的视觉样式，实际排序功能需要安装社区插件 **Sortable**：在「设置 → 社区插件 → 浏览」中搜索「Sortable」，安装并启用后，带有 `tg-sort` 类的表格表头即可点击排序。

**固定表头的高度不合适**

默认高度限制为 400px，可在 CSS 代码片段（或库根目录下的任意 CSS 文件）中添加以下内容调整：

```css
:root { --tg-sticky-max-height: 600px; }
```

将 `600px` 替换为你希望的高度值即可。

**在移动端（iOS/Android）能否正常使用**

HTML 表格的渲染依赖 Obsidian 的 WebView，移动端同样支持。但 TablesGenerator 是网页工具，在手机上操作可视化编辑器体验较差。建议在桌面端完成表格编辑和格式化，同步到移动端后即可正常查看。

---

## 11. 进阶用法

### 11.1 修改已有表格

若需要修改一张已经插入 Obsidian 的表格，可将笔记中的 HTML 代码复制，回到 TablesGenerator 网站，点击 **File → Paste table data**，在对话框中粘贴代码，TablesGenerator 即可识别并还原为可编辑状态。修改完成后，重新导出、格式化，替换笔记中的旧代码即可。

### 11.2 手动微调类名

格式化后的 HTML 代码可读性良好，若某个单元格的对齐方式与预期不符，可以直接在 Obsidian 源码模式中手动修改 `class` 属性中的类名，例如将 `tg-left-top` 改为 `tg-center-middle`，切换到预览模式即可立即看到效果。

### 11.3 与 Callout 结合

格式化后的 HTML 表格可以嵌套在 Obsidian 的 Callout 块内，实现带有提示框样式的表格展示。Callout 块中的 HTML 会正常渲染，但需要注意每行内容仍须以 `>` 开头，建议先将 HTML 表格放置在笔记中，确认渲染正常后再手动包裹 Callout 语法。

### 11.4 自定义 CSS 类

如果你对某类表格有固定的样式需求（例如特定背景色的表格、特殊字号的标题列），可以在 `table-tg-html.css` 的末尾追加自定义规则，并在 HTML 代码中手动为对应的 `<table>` 或 `<td>` 添加自定义类名。CSS 代码片段对 Obsidian 全库生效，所有笔记中的同名类都会应用相同样式。

---

## 附录

### 附录 A：`table-tg-html.css`

**用途**：Obsidian CSS 代码片段，为所有带有 `tg` 类的 HTML 表格提供统一样式，完全适配 Obsidian 主题的 CSS 变量，支持深浅色模式、响应式滚动、固定表头、打印优化等功能。

**安装位置**：库文件夹下的 `.obsidian/snippets/` 目录，安装后在「设置 → 外观 → CSS 代码片段」中启用。

```css
.markdown-preview-view .el-table {
    margin-top: 0 !important;
    margin-bottom: var(--p-spacing, 1rem) !important;
}

.markdown-preview-view .tg,
.markdown-source-view .tg {
    border-collapse: collapse;
    border-spacing: 0;
    width: 100%;
    table-layout: auto;
    margin-top: 0 !important;
    margin-bottom: 0 !important;
}

.markdown-preview-view .tg td,
.markdown-preview-view .tg th,
.markdown-source-view .tg td,
.markdown-source-view .tg th {
    border-style: solid;
    border-width: 1px;
    border-color: var(--background-modifier-border);
    padding: 10px 8px;
    overflow: hidden;
    word-break: normal;
    overflow-wrap: break-word;
    font-family: var(--font-text);
    font-size: var(--font-text-size, 1em);
    line-height: var(--line-height-normal, 1.5);
    background-color: transparent;
    transition: background-color 0.15s ease;
}

.markdown-preview-view .tg caption,
.markdown-source-view .tg caption {
    caption-side: top;
    text-align: center;
    font-weight: bold;
    font-size: 0.95em;
    padding: 0 4px 10px;
    color: var(--text-normal);
    letter-spacing: 0.02em;
    white-space: normal;
    max-width: 100%;
}

.markdown-preview-view .tg thead th,
.markdown-source-view .tg thead th {
    font-weight: bold;
    border-bottom: 2px solid var(--background-modifier-border);
    white-space: nowrap;
}

.markdown-preview-view .tg th[scope="row"],
.markdown-preview-view .tg th[scope="rowgroup"],
.markdown-source-view .tg th[scope="row"],
.markdown-source-view .tg th[scope="rowgroup"] {
    font-weight: 600;
    font-style: normal;
    text-align: left;
    white-space: normal;
}

.markdown-preview-view .tg tbody td,
.markdown-preview-view .tg tbody th,
.markdown-source-view .tg tbody td,
.markdown-source-view .tg tbody th {
    vertical-align: top;
}

.markdown-preview-view .tg tfoot td,
.markdown-preview-view .tg tfoot th,
.markdown-source-view .tg tfoot td,
.markdown-source-view .tg tfoot th {
    border-top: 2px solid var(--background-modifier-border);
    font-style: italic;
    color: var(--text-muted);
}

.markdown-preview-view .tg td:focus-visible,
.markdown-preview-view .tg th:focus-visible,
.markdown-source-view .tg td:focus-visible,
.markdown-source-view .tg th:focus-visible {
    outline: 2px solid var(--interactive-accent);
    outline-offset: -2px;
    background-color: color-mix(in srgb, var(--interactive-accent) 8%, transparent) !important;
}

@media print {
    .tg thead { display: table-header-group; }
    .tg tbody { display: table-row-group;    }
    .tg tfoot { display: table-footer-group; }

    .tg tr {
        break-inside: avoid;
        page-break-inside: avoid;
    }

    .tg.tg-sort thead th::after { display: none !important; }
    .tg.tg-sort thead th { cursor: default; padding-right: 8px; }

    .tg.tg-sticky thead th {
        position: static !important;
        box-shadow: none !important;
        background-color: transparent !important;
        border-top-width: 1px !important;
        border-bottom-width: 2px !important;
    }
}

.markdown-preview-view .tg .tg-left-top,
.markdown-source-view  .tg .tg-left-top   { text-align: left   !important; vertical-align: top    !important; }
.markdown-preview-view .tg .tg-center-top,
.markdown-source-view  .tg .tg-center-top { text-align: center !important; vertical-align: top    !important; }
.markdown-preview-view .tg .tg-right-top,
.markdown-source-view  .tg .tg-right-top  { text-align: right  !important; vertical-align: top    !important; }

.markdown-preview-view .tg .tg-left-middle,
.markdown-source-view  .tg .tg-left-middle   { text-align: left   !important; vertical-align: middle !important; }
.markdown-preview-view .tg .tg-center-middle,
.markdown-source-view  .tg .tg-center-middle { text-align: center !important; vertical-align: middle !important; }
.markdown-preview-view .tg .tg-right-middle,
.markdown-source-view  .tg .tg-right-middle  { text-align: right  !important; vertical-align: middle !important; }

.markdown-preview-view .tg .tg-left-bottom,
.markdown-source-view  .tg .tg-left-bottom   { text-align: left   !important; vertical-align: bottom !important; }
.markdown-preview-view .tg .tg-center-bottom,
.markdown-source-view  .tg .tg-center-bottom { text-align: center !important; vertical-align: bottom !important; }
.markdown-preview-view .tg .tg-right-bottom,
.markdown-source-view  .tg .tg-right-bottom  { text-align: right  !important; vertical-align: bottom !important; }

.markdown-preview-view .tg-wrapper,
.markdown-source-view .tg-wrapper {
    overflow-x: auto;
    width: 100%;
    padding-bottom: 2px;
    margin-top: 0 !important;
    margin-bottom: 0 !important;
    overscroll-behavior-x: contain;
    scrollbar-width: thin;
    scrollbar-color: var(--background-modifier-border) transparent;
}

.markdown-preview-view .tg-wrapper .tg,
.markdown-source-view .tg-wrapper .tg {
    margin: 0;
}

.markdown-preview-view .tg-wrapper::-webkit-scrollbar,
.markdown-source-view .tg-wrapper::-webkit-scrollbar { height: 4px; }

.markdown-preview-view .tg-wrapper::-webkit-scrollbar-track,
.markdown-source-view .tg-wrapper::-webkit-scrollbar-track { background: transparent; }

.markdown-preview-view .tg-wrapper::-webkit-scrollbar-thumb,
.markdown-source-view .tg-wrapper::-webkit-scrollbar-thumb {
    background-color: var(--background-modifier-border);
    border-radius: 2px;
}

.markdown-preview-view .tg.tg-center,
.markdown-source-view .tg.tg-center {
    margin-left: auto;
    margin-right: auto;
}

.markdown-preview-view .tg.tg-sticky thead th,
.markdown-source-view .tg.tg-sticky thead th {
    position: sticky;
    top: 0;
    z-index: 10;
    background-color: var(--background-primary, #fff) !important;
    border-top-width: 0;
    border-bottom-width: 0;
    box-shadow:
        inset 0  1px 0 var(--background-modifier-border),
        inset 0 -2px 0 var(--background-modifier-border);
}

.markdown-preview-view .tg-sticky-wrapper,
.markdown-source-view  .tg-sticky-wrapper {
    overflow-y: auto;
    max-height: var(--tg-sticky-max-height, 400px);
    padding-bottom: 0;
}

.markdown-preview-view .tg-wrapper:has(.tg-sticky),
.markdown-source-view  .tg-wrapper:has(.tg-sticky) {
    overflow-y: auto;
    max-height: var(--tg-sticky-max-height, 400px);
    padding-bottom: 0;
}

.markdown-preview-view .tg.tg-sort thead th,
.markdown-source-view .tg.tg-sort thead th {
    cursor: pointer;
    user-select: none;
    padding-right: 22px;
    position: relative;
    min-height: 44px;
    touch-action: pan-y;
    -webkit-tap-highlight-color: transparent;
}

.markdown-preview-view .tg.tg-sort thead th::after,
.markdown-source-view .tg.tg-sort thead th::after {
    content: '⇅';
    position: absolute;
    right: 5px;
    top: 50%;
    transform: translateY(-50%);
    font-size: 0.85em;
    opacity: 0.35;
    pointer-events: none;
    line-height: 1;
}

.markdown-preview-view .tg.tg-sort thead th[data-sort-dir="asc"]::after,
.markdown-source-view .tg.tg-sort thead th[data-sort-dir="asc"]::after {
    content: '↑';
    opacity: 1;
    color: var(--interactive-accent);
}

.markdown-preview-view .tg.tg-sort thead th[data-sort-dir="desc"]::after,
.markdown-source-view .tg.tg-sort thead th[data-sort-dir="desc"]::after {
    content: '↓';
    opacity: 1;
    color: var(--interactive-accent);
}

.markdown-preview-view .tg.tg-sort thead th:hover,
.markdown-source-view .tg.tg-sort thead th:hover {
    background-color: var(--background-modifier-hover) !important;
}

.markdown-preview-view .tg.tg-sort thead th:active,
.markdown-source-view .tg.tg-sort thead th:active {
    background-color: var(--background-modifier-active-hover) !important;
}
```

> 📎 **获取完整代码**
>
> 1. 以上即为完整代码，可直接复制使用。
> 2. 关注微信公众号：**Obsidianist**，回复关键词：**HTML表格**，后台即会自动回复完整代码文件下载地址，下载后放入库根目录 `.obsidian/snippets` 文件夹，在「外观」设置中启用对应片段即可。

---

### 附录 B：`格式化-表格代码格式化模板.md`

**用途**：Templater 模板文件，触发后弹出设置对话框，读取用户在编辑器中选中的 TablesGenerator HTML 代码，将随机类名映射为标准类名（如 `tg-center-top`），清除 `<style>` 标签，并根据弹窗中的设置决定是否添加表格标题、启用响应式滚动、固定表头、可排序表头或键盘导航支持。

**安装位置**：Templater 插件设置中指定的模板文件夹。

````
<%*
let selection = tp.file.selection();
if (!selection || selection.trim() === "") {
    new Notice("❌ 请先选中 Tables Generator 生成的 HTML 代码，再插入此模板！");
    return;
}

const detectedCenter = (
    /margin\s*:\s*(?:0\s+auto|auto)/i.test(selection) ||
    /margin-(?:left|right)\s*:\s*auto/i.test(selection) ||
    /<div[^>]*text-align\s*:\s*center[^>]*>/i.test(selection)
);

const detectedResponsive = /overflow-x\s*:\s*auto/i.test(selection);

const detectedSort = /<script\b/i.test(selection);

const styleBlockForDetection = (selection.match(/<style\b[\s\S]*?<\/style>/i) || [""])[0];
const detectedSticky = /position\s*:\s*sticky/i.test(styleBlockForDetection);

const dialogResult = await new Promise((resolve) => {
  let resolved = false;
  const modal = new tp.obsidian.Modal(app);
  modal.titleEl.setText("表格设置（Ctrl+Enter 确认）");

  modal.contentEl.createEl("style").textContent = `
    .tg-detect-tip {
      font-size: 12px; color: var(--text-muted);
      padding: 4px 10px; border-radius: 4px; margin-bottom: 8px;
      border-left: 3px solid var(--interactive-accent);
      background: var(--background-secondary);
    }
    .tg-sec-title {
      font-size: 11px; font-weight: 600; color: var(--text-muted);
      text-transform: uppercase; letter-spacing: 0.6px; margin-bottom: 5px;
    }
    .tg-textarea {
      width: 100%; box-sizing: border-box; font-size: 13px;
      padding: 6px 8px; border-radius: 4px; resize: vertical;
      font-family: inherit; margin-bottom: 2px;
      border: 1px solid var(--background-modifier-border);
      background: var(--background-primary); color: var(--text-normal);
    }
    .tg-textarea:focus {
      border-color: var(--interactive-accent);
      box-shadow: 0 0 0 2px rgba(var(--interactive-accent-rgb), 0.15);
      outline: none;
    }
    .tg-divider { border: none; border-top: 1px solid var(--background-modifier-border); margin: 6px 0; }
    .tg-grid {
      display: grid; grid-template-columns: 1fr 1fr;
      gap: 4px 10px; margin-bottom: 4px;
    }
    .tg-row { display: flex; flex-direction: column; gap: 2px; }
    .tg-label { font-size: 12px; color: var(--text-muted); white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
    .tg-select {
      width: 100%; padding: 3px 6px; border-radius: 4px;
      border: 1px solid var(--background-modifier-border);
      background: var(--background-secondary); color: var(--text-normal);
      font-size: 12px; cursor: pointer; outline: none;
    }
    .tg-select:focus { border-color: var(--interactive-accent); }
    .tg-btn-row { display: flex; justify-content: flex-end; gap: 8px; margin-top: 8px; }
    .tg-confirm-btn {
      background: var(--interactive-accent) !important;
      border: 1px solid var(--interactive-accent) !important;
      color: var(--text-on-accent) !important;
      padding: 4px 16px !important; border-radius: 5px !important;
      font-size: 13px !important; cursor: pointer !important;
      transition: filter 0.1s ease !important;
    }
    .tg-confirm-btn:hover { filter: brightness(1.1) !important; }
    .tg-cancel-btn {
      background: var(--background-secondary) !important;
      border: 1px solid var(--background-modifier-border) !important;
      color: var(--text-normal) !important;
      padding: 4px 12px !important; border-radius: 5px !important;
      font-size: 13px !important; cursor: pointer !important;
    }
    .tg-cancel-btn:hover { background: var(--background-modifier-hover) !important; }
  `;

  const el = modal.contentEl;

  const hits = [];
  if (detectedCenter)     hits.push("水平居中");
  if (detectedResponsive) hits.push("响应式");
  if (detectedSort)       hits.push("排序");
  if (detectedSticky)     hits.push("固定表头");
  el.createEl("div", {
    cls: "tg-detect-tip",
    text: hits.length > 0
      ? "✦ 自动检测到：" + hits.join(" · ") + "（已预选，可在下方调整）"
      : "✦ 未检测到特殊功能，所有选项默认关闭，可按需启用。"
  });

  el.createEl("div", { cls: "tg-sec-title", text: "① 表格标题 <caption>（可多行粘贴）" });
  const captionTextarea = el.createEl("textarea", {
    cls: "tg-textarea",
    attr: { rows: "2", placeholder: "输入文字作为可见标题，或留空不插入 <caption>…" }
  });

  el.createEl("hr", { cls: "tg-divider" });

  el.createEl("div", { cls: "tg-sec-title", text: "② 功能选项（已根据自动检测预选，可调整）" });

  function makeSelect(parent, label, defaultEnabled) {
    const row = parent.createEl("div", { cls: "tg-row" });
    row.createEl("div", { text: label, cls: "tg-label" });
    const sel = row.createEl("select", { cls: "tg-select" });
    for (const opt of ["禁用", "启用"]) {
      const o = sel.createEl("option"); o.value = opt; o.textContent = opt;
      if ((opt === "启用") === defaultEnabled) o.selected = true;
    }
    return { isEnabled: () => sel.value === "启用" };
  }

  const grid = el.createEl("div", { cls: "tg-grid" });

  const centerToggle    = makeSelect(grid, "水平居中 .tg-center",         detectedCenter);
  const responsiveToggle = makeSelect(grid, "响应式滚动 .tg-wrapper",     detectedResponsive);
  const sortToggle      = makeSelect(grid, "可排序表头 .tg-sort",         detectedSort);
  const stickyToggle    = makeSelect(grid, "固定表头 .tg-sticky",         detectedSticky);
  const tabindexToggle  = makeSelect(grid, "③ 键盘导航 tabindex (WCAG)", false);

  const btnRow     = el.createEl("div", { cls: "tg-btn-row" });
  const confirmBtn = btnRow.createEl("button", { text: "确认（Ctrl+Enter）", cls: "tg-confirm-btn" });
  const cancelBtn  = btnRow.createEl("button", { text: "取消（Esc）",        cls: "tg-cancel-btn"  });

  const confirm = () => {
    if (resolved) return;
    resolved = true;
    resolve({
      caption:    captionTextarea.value,
      center:     centerToggle.isEnabled(),
      responsive: responsiveToggle.isEnabled(),
      sort:       sortToggle.isEnabled(),
      sticky:     stickyToggle.isEnabled(),
      tabindex:   tabindexToggle.isEnabled(),
    });
    modal.close();
  };
  const cancel = () => {
    if (resolved) return;
    resolved = true;
    resolve(null);
    modal.close();
  };

  confirmBtn.addEventListener("click", confirm);
  cancelBtn.addEventListener("click",  cancel);
  modal.contentEl.addEventListener("keydown", (e) => {
    if (e.key === "Enter" && e.ctrlKey) { e.preventDefault(); confirm(); }
    if (e.key === "Escape") { cancel(); }
  });

  modal.onClose = () => {
    if (!resolved) { resolved = true; resolve(null); }
  };
  modal.open();
  setTimeout(() => captionTextarea.focus(), 50);
});

if (!dialogResult) { tR += selection; return; }

const enableCenter     = dialogResult.center;
const enableResponsive = dialogResult.responsive;
const enableSort       = dialogResult.sort;
const enableSticky     = dialogResult.sticky;
const addTabindex      = dialogResult.tabindex;

const captionText = dialogResult.caption.trim()
    .replace(/&/g, "&amp;")
    .replace(/</g, "&lt;")
    .replace(/>/g, "&gt;")
    .replace(/"/g, "&quot;")
    .replace(/'/g, "&#39;");

const VALID_H = /^(left|center|right)$/;
const VALID_V = /^(top|middle|bottom)$/;

const EXTERNAL_PROPS = new Set([
    'border-collapse', 'border-spacing',
    'border-color', 'border-style', 'border-width',
    'border-top', 'border-bottom', 'border-left', 'border-right',
    'overflow', 'padding',
    'font-family', 'font-size', 'line-height',
    'word-break', 'overflow-wrap', 'white-space'
]);

const styleRegex = /\.tg\s+\.([\w-]+)\s*\{([^}]+)\}/g;
let styleMatch;
const classMappings       = {};
const classNonAlignStyles = {};

while ((styleMatch = styleRegex.exec(selection)) !== null) {
    const randomClass = styleMatch[1];
    const styleBody   = styleMatch[2];

    const hMatch = styleBody.match(/text-align\s*:\s*(left|center|right)/i);
    const hAlign = hMatch ? hMatch[1].toLowerCase() : "left";
    const vMatch = styleBody.match(/vertical-align\s*:\s*(top|middle|bottom)/i);
    const vAlign = vMatch ? vMatch[1].toLowerCase() : "top";

    if (VALID_H.test(hAlign) && VALID_V.test(vAlign)) {
        classMappings[randomClass] = `tg-${hAlign}-${vAlign}`;
    }

    const extras = [];
    for (const m of styleBody.matchAll(/([a-z-]+)\s*:\s*([^;{}]+)/gi)) {
        const key = m[1].trim().toLowerCase();
        const val = m[2].trim().replace(/;$/, '').trim();
        if (!key || !val) continue;
        if (key === 'text-align' || key === 'vertical-align') continue;
        if (EXTERNAL_PROPS.has(key)) continue;
        if (key === 'border-color' && /^inherit$/i.test(val)) continue;
        extras.push(`${key}:${val}`);
    }
    if (extras.length) classNonAlignStyles[randomClass] = extras.join(';');
}

let outputHtml = selection;

outputHtml = outputHtml.replace(/<style\b[\s\S]*?<\/style>/gi, "");
outputHtml = outputHtml.replace(/<script\b[\s\S]*?<\/script>/gi, "");
outputHtml = outputHtml.replace(
    /<div\b[^>]*overflow-x\s*:\s*auto[^>]*>\s*(<table\b[\s\S]*?<\/table>)\s*<\/div>/gi,
    "$1"
);

outputHtml = outputHtml.replace(
    /<table\b([^>]*)>/i,
    (match, attrs) => {
        let newAttrs = attrs;
        const featureClasses = [];
        if (enableCenter) featureClasses.push("tg-center");
        if (enableSticky) featureClasses.push("tg-sticky");
        if (enableSort)   featureClasses.push("tg-sort");
        if (featureClasses.length > 0) {
            if (/class\s*=\s*["']/i.test(newAttrs)) {
                newAttrs = newAttrs.replace(
                    /(class\s*=\s*["'])([^"']*?)(["'])/i,
                    (m, open, existing, close) => {
                        const parts = existing.trim()
                            ? [existing.trim(), ...featureClasses]
                            : featureClasses;
                        return `${open}${parts.join(" ")}${close}`;
                    }
                );
            } else {
                newAttrs = `class="tg ${featureClasses.join(" ")}"` +
                           (newAttrs.trim() ? ' ' + newAttrs.trim() : '');
            }
        }
        newAttrs = newAttrs.replace(
            /(?:^|\s)(?:border|cellspacing|cellpadding|align|bgcolor|frame|rules|summary)\s*=\s*(?:"[^"]*"|'[^']*'|\S+)/gi,
            ''
        );
        newAttrs = newAttrs.replace(/\s*aria-label(?:ledby)?\s*=\s*(?:"[^"]*"|'[^']*')/gi, "");
        newAttrs = newAttrs.replace(
            /(style\s*=\s*(["']))([\s\S]*?)\2/i,
            (m, open, q, styleVal) => {
                let s = styleVal;
                s = /width/i.test(s)
                    ? s.replace(/width\s*:\s*[\d.]+(?:px|em|rem|vw|%)?/gi, "width:100%")
                    : s.trimEnd().replace(/;?\s*$/, ";") + "width:100%";
                s = s.replace(/margin\s*:\s*(?:0\s+)?auto\s*;?/gi, "");
                s = s.replace(/margin-(?:left|right)\s*:\s*auto\s*;?/gi, "");
                s = s.replace(/;{2,}/g, ";").replace(/;\s*$/, "").trim();
                return s ? `${open}${s}${q}` : "";
            }
        );
        newAttrs = newAttrs.replace(/\s*style\s*=\s*(?:""|'')\s*/gi, " ").trim();
        if (!/style\s*=/i.test(newAttrs)) {
            newAttrs += ' style="width:100%"';
        }
        const normalizedAttrs = newAttrs.trim();
        return `<table${normalizedAttrs ? " " + normalizedAttrs : ""}>`;
    }
);

outputHtml = outputHtml.replace(
    /(<thead\b[\s\S]*?<\/thead>)/gi,
    (theadBlock) =>
        theadBlock.replace(/<th\b([^>]*)>/gi, (match, attrs) => {
            if (/\bscope=/i.test(attrs)) return match;
            const colspanMatch = attrs.match(/\bcolspan=["']?(\d+)["']?/i);
            const scopeVal = (colspanMatch && parseInt(colspanMatch[1], 10) > 1)
                ? "colgroup" : "col";
            return `<th scope="${scopeVal}"${attrs ? " " + attrs.trim() : ""}>`;
        })
);

outputHtml = outputHtml.replace(
    /(<tbody\b[\s\S]*?<\/tbody>)/gi,
    (tbodyBlock) =>
        tbodyBlock.replace(/<tr\b[\s\S]*?<\/tr>/gi, (rowBlock) => {
            let firstThDone = false;
            return rowBlock.replace(/<th\b([^>]*)>/gi, (match, attrs) => {
                if (!firstThDone && !/\bscope=/i.test(attrs)) {
                    firstThDone = true;
                    const rowspanMatch = attrs.match(/\browspan=["']?(\d+)["']?/i);
                    const scopeVal = (rowspanMatch && parseInt(rowspanMatch[1], 10) > 1)
                        ? "rowgroup" : "row";
                    return `<th scope="${scopeVal}"${attrs ? " " + attrs.trim() : ""}>`;
                }
                return match;
            });
        })
);

outputHtml = outputHtml.replace(
    /<(td|th)\b([^>]*)>/gi,
    (match, tag, attrsStr) => {
        let attrs = attrsStr;
        const extraStyles = [];
        attrs = attrs.replace(
            /(class\s*=\s*(["']))(.*?)\2/gi,
            (cm, open, q, classes) => {
                const newClasses = classes.trim().split(/\s+/).map(cls => {
                    if (classMappings[cls]) {
                        if (classNonAlignStyles[cls]) extraStyles.push(classNonAlignStyles[cls]);
                        return classMappings[cls];
                    }
                    return cls;
                });
                return `${open}${[...new Set(newClasses)].join(' ')}${q}`;
            }
        );
        let hadStyleAttr = false;
        attrs = attrs.replace(
            /(style\s*=\s*(["']))(.*?)\2/gi,
            (sa, open, q, styleVal) => {
                hadStyleAttr = true;
                let s = styleVal
                    .replace(/\btext-align\s*:[^;]*(;|$)/gi, '')
                    .replace(/\bvertical-align\s*:[^;]*(;|$)/gi, '')
                    .replace(/;{2,}/g, ';').replace(/;\s*$/, '').trim();
                if (extraStyles.length) {
                    s = s ? `${s};${extraStyles.join(';')}` : extraStyles.join(';');
                }
                return s ? `${open}${s}${q}` : '';
            }
        );
        attrs = attrs.replace(/\s*style\s*=\s*(?:""|'')/gi, '').trim();
        if (!hadStyleAttr && extraStyles.length) {
            attrs = (attrs ? attrs + ' ' : '') + `style="${extraStyles.join(';')}"`;
        }
        return `<${tag}${attrs ? ' ' + attrs.trim() : ''}>`;
    }
);

outputHtml = outputHtml.replace(
    /<col\b([^>]*)>/gi,
    (match, attrsStr) => {
        const cleaned = attrsStr
            .replace(/\s*style=(?:"[^"]*"|'[^']*')/gi, "")
            .replace(/\s*width=(?:"[^"]*"|'[^']*'|\S+)/gi, "")
            .trim();
        return `<col${cleaned ? " " + cleaned : ""}>`;
    }
);

outputHtml = outputHtml.replace(/<colgroup\b[^>]*>[\s\S]*?<\/colgroup>/gi, "");

outputHtml = outputHtml.replace(/<caption\b[^>]*>[\s\S]*?<\/caption>/gi, "");
if (captionText) {
    outputHtml = outputHtml.replace(
        /(<table\b[^>]*>)/i,
        `$1\n<caption>${captionText}</caption>`
    );
}

if (addTabindex) {
    outputHtml = outputHtml.replace(
        /<(td|th)\b([^>]*)>/gi,
        (match, tag, attrsStr) => {
            if (/\btabindex=/i.test(attrsStr)) return match;
            const trimmed = attrsStr.trim();
            return `<${tag}${trimmed ? " " + trimmed : ""} tabindex="0">`;
        }
    );
}

if (enableResponsive) {
    const wrapperCls = enableSticky ? "tg-wrapper tg-sticky-wrapper" : "tg-wrapper";
    outputHtml = outputHtml.replace(/(<table\b)/i, `<div class="${wrapperCls}">\n$1`);
    outputHtml = outputHtml.replace(/(<\/table>)/i, "$1\n</div>");
}

outputHtml = outputHtml.replace(/\n{3,}/g, "\n\n").trim();

if (enableSort) {
    new Notice(
        "ℹ️ 已添加 .tg-sort 排序视觉提示。\n" +
        "实际排序需安装插件 \"Sortable\"：\n" +
        "设置 → 第三方插件 → 浏览 → 搜索 Sortable → 安装并启用",
        8000
    );
}

if (enableSticky && enableResponsive) {
    new Notice(
        "ℹ️ 固定表头 + 响应式已同时启用。\n" +
        "表格高度默认限制为 400px。\n" +
        "如需调整，在 Obsidian CSS 代码片段中添加：\n" +
        ":root { --tg-sticky-max-height: 600px; }",
        8000
    );
}

tR += outputHtml;
%>
````

> 📎 **获取完整代码**
>
> 1. 以上即为完整代码，可直接复制使用。
> 2. 关注微信公众号：**Obsidianist**，回复关键词：**HTML表格**，后台即会自动回复完整代码文件下载地址，下载后直接放入 Templater 模板文件夹，无需任何额外配置，即可使用。
