---
tags:
  - Obsidian/第三方插件/Templater/标注
---

# Obsidian 标注模板完整使用教程

Obsidian 的标注块（Callout）是笔记排版中极为实用的组件——用来突出警告、标记重点、归纳摘要，甚至用作引用与示例。但每次手动键入 `> [!note] 标题` 这样的语法，再逐行添加 `>` 前缀，既繁琐又容易出错。

**插入 - 标注模板** 正是为解决这个问题而生。它基于 Templater 插件构建了一个图形化的选择弹窗：你只需点选标注类型、填写标题与内容，确认后模板便会自动生成符合 Obsidian 语法的标注块，并插入到光标所在位置。整个过程无需记忆任何语法，十秒内完成。

---

## 目录

1. [标注块基础：Obsidian Callout 是什么](Obsidian%20标注模板完整使用教程.md#1.%20标注块基础：Obsidian%20Callout%20是什么)
2. [前提条件](Obsidian%20标注模板完整使用教程.md#2.%20前提条件)
3. [模板文件的放置与配置](Obsidian%20标注模板完整使用教程.md#3.%20模板文件的放置与配置)
4. [使用流程：从触发到插入](Obsidian%20标注模板完整使用教程.md#4.%20使用流程：从触发到插入)
5. [弹窗界面详解](Obsidian%20标注模板完整使用教程.md#5.%20弹窗界面详解)
6. [所有支持的标注类型](Obsidian%20标注模板完整使用教程.md#6.%20所有支持的标注类型)
7. [折叠选项说明](Obsidian%20标注模板完整使用教程.md#7.%20折叠选项说明)
8. [输出格式解析](Obsidian%20标注模板完整使用教程.md#8.%20输出格式解析)
9. [快捷键与效率技巧](Obsidian%20标注模板完整使用教程.md#9.%20快捷键与效率技巧)
10. [常见问题与注意事项](Obsidian%20标注模板完整使用教程.md#10.%20常见问题与注意事项)
11. [进阶：修改与扩展标注类型](Obsidian%20标注模板完整使用教程.md#11.%20进阶：修改与扩展标注类型)

---

## 1. 标注块基础：Obsidian Callout 是什么

Obsidian 从 0.14 版本起内置了对标注块（Callout）的原生支持。标注块的基本语法如下：

```
> [!note] 这是标题
> 这是标注内容，可以换行。
> 每一行都以 `> ` 开头。
```

渲染后，它会以带有图标、彩色背景和标题栏的卡片形式呈现，视觉上非常醒目。不同的类型（`note`、`warning`、`tip` 等）对应不同的配色和图标，Obsidian 会根据 `[!类型]` 自动匹配样式。

标注块还支持**折叠**功能：在类型后紧跟 `+` 表示默认展开可折叠，`-` 表示默认折叠。例如：

```
> [!warning]- 注意（点击展开）
> 这段内容默认是折叠的。
```

手动写这些语法并不难，但多行内容的每行都要加 `> ` 前缀，在频繁使用时会成为明显的摩擦点。模板的价值正在于此。

---

## 2. 前提条件

在使用本模板前，请确认以下条件已满足：

**Obsidian 版本**：建议使用 1.0 及以上版本，以确保对 Callout 的完整支持。

**Templater 插件**：这是本模板运行的核心依赖。若尚未安装，可在 Obsidian 的「社区插件」页面搜索「Templater」安装，作者为 SilentVoid13。安装后需在插件设置中开启「Enable Templater」。

**Templater 的模板文件夹**：Templater 需要指定一个文件夹用于存放模板文件。若尚未配置，请进入「设置 → Templater → Template folder location」，填写你用于存放模板的文件夹路径（例如 `Templates` 或 `00-Templates`）。

无需额外安装其他插件，标注块是 Obsidian 的内置功能，不依赖 Dataview、Tasks 等第三方插件。

---

## 3. 模板文件的放置与配置

### 3.1 放置模板文件

将 `插入-标注模板.md` 文件放入你在 Templater 中配置的模板文件夹内。文件名可以按需修改，模板的功能不受文件名影响。

> 📎 **获取完整代码**
>
> 1. 为了提升阅读体验，完整代码已放置于本文末尾**附录**部分，可直接前往复制使用。
> 2. 关注微信公众号：**Obsidianist**，回复关键词：**标注模板**，后台即会自动回复完整代码文件下载地址，下载后直接放入 Templater 模板文件夹，无需任何额外配置，即可使用。

> 💡 **说明（按需调整）**
> 模板文件夹的路径因库而异，请将文件放入你自己在 Templater 设置中指定的文件夹，而非固定路径。以下为示例对比：
>
> | 项目 | 示例值 | 替换建议 |
> |------|--------|---------|
> | 模板文件夹 | `Templates/` | 替换为你在 Templater 中实际配置的模板文件夹路径 |
> | 模板文件名 | `插入-标注模板.md` | 可重命名为任意你习惯的名称 |

### 3.2 确认 Templater 设置

进入「设置 → Templater」，确认以下两项处于开启状态：

- **Enable Templater**：已开启
- **Trigger Templater on new file creation**（可选）：若需要在新建笔记时自动触发，可开启；本模板通常用于手动插入，此项不是必须的

### 3.3 绑定快捷键（推荐）

为了达到最流畅的使用体验，建议为「打开插入模板」动作绑定一个快捷键。进入「设置 → 快捷键」，搜索「Templater: Insert Template」或「Templater: Open Insert Template Modal」，设置你偏好的组合键（例如 `Alt+T`）。

你也可以直接在命令面板（`Ctrl/Cmd+P`）中搜索「Templater」来触发模板，无需单独绑定快捷键。

---

## 4. 使用流程：从触发到插入

整个使用过程分为以下几步：

**第一步：将光标定位到目标位置。** 打开你想要插入标注块的笔记，将光标放到希望插入的行。

**第二步：触发模板。** 通过以下任一方式打开模板：

- 使用你设置的快捷键，随后在 Templater 的模板列表中选择「插入 - 标注模板」
- 打开命令面板（`Ctrl/Cmd+P`），输入「Templater」，选择插入模板的选项，再选择本模板

**第三步：在弹窗中完成配置。** 弹窗打开后，依次选择标注类型、折叠选项，并填写标题与内容。

**第四步：确认插入。** 点击「确认插入」按钮，或按 `Ctrl+Enter` 快捷键，标注块将自动插入到光标位置。

---

## 5. 弹窗界面详解

模板触发后会弹出一个图形化对话框，包含以下几个部分：

### 5.1 标注类型选择器

位于弹窗顶部。点击下拉触发器，会展开一个包含 13 种标注类型的列表。每一项均以 Obsidian 原生的标注样式渲染，包含对应的彩色图标和类型名称，所见即所得。

点击列表中的任意一项即可选中，列表自动收起，触发器中会显示已选类型的预览效果。

> ⚠️ **注意**
> 标注类型为必填项。若未选择类型直接点击确认，触发器边框会变为红色提示，并自动展开列表，等待选择。

### 5.2 折叠选项

提供三个并排的按钮：

- **无**：不添加折叠控制，标注块正常展示，不可折叠
- **展开 +**：标注块默认展开，但用户可点击标题栏将其折叠（对应语法中的 `+`）
- **折叠 −**：标注块默认折叠，用户点击标题栏后展开（对应语法中的 `-`）

点击按钮后，按钮背景变为强调色表示已选中。此项为可选，若不点击任何按钮，默认输出不含折叠控制的标准标注块。

### 5.3 标题输入框

单行文本框，对应标注块的标题部分。可以留空——留空时，Obsidian 会根据标注类型自动使用默认标题（如 `Note`、`Warning` 等）。

弹窗打开时，光标会自动定位到标题输入框，可以直接开始输入。

### 5.4 内容输入框

多行文本域，用于填写标注块的正文内容。支持粘贴多行文字，每一行都会在输出时自动添加 `> ` 前缀，无需手动处理。

### 5.5 操作按钮

- **确认插入**：提交当前配置，生成并插入标注块
- **取消**：关闭弹窗，不插入任何内容
- **Ctrl+Enter**：在标题或内容输入框聚焦时，等同于点击「确认插入」

---

## 6. 所有支持的标注类型

模板内置了以下 13 种标注类型，覆盖了 Obsidian 官方支持的常用类型：

| 类型标识 | 显示名称 | 典型用途 |
|---------|---------|---------|
| `note` | Note | 普通笔记、补充说明 |
| `info` | Info | 信息提示、参考说明 |
| `todo` | Todo | 待办事项提示 |
| `tip` | Tip / Hint / Important | 技巧提示、重要提示 |
| `abstract` | Abstract / Summary / Tldr | 摘要、总结、概述 |
| `question` | Question / Help / Faq | 疑问、常见问题 |
| `quote` | Quote / Cite | 引用、引述 |
| `example` | Example | 示例说明 |
| `success` | Success / Check / Done | 成功状态、已完成 |
| `warning` | Warning / Caution / Attention | 警告、注意事项 |
| `failure` | Failure / Fail / Missing | 失败、错误、缺失 |
| `danger` | Danger / Error | 危险提示、严重错误 |
| `bug` | Bug | 已知问题、缺陷记录 |

> 💡 **说明**
> 部分类型支持多个别名（如 `tip` 等同于 `hint` 或 `important`），在 Obsidian 中使用这些别名会渲染为相同的样式。本模板统一使用主类型标识输出，行为完全一致。

---

## 7. 折叠选项说明

折叠是 Obsidian Callout 的内置功能，通过在类型标识后紧跟特定字符来控制：

**不折叠（默认）**：标注块完整展示，无折叠交互。输出格式：

```
> [!tip] 标题
> 内容
```

**展开 +**：标注块默认处于展开状态，但标题栏可点击，点击后内容折叠。输出格式：

```
> [!tip]+ 标题
> 内容
```

**折叠 −**：标注块默认折叠，仅显示标题栏，点击后展开内容。输出格式：

```
> [!tip]- 标题
> 内容
```

折叠功能在整理长篇笔记时尤为实用——将次要信息收纳到可折叠标注块中，既保留了内容，又不影响主体的阅读流畅性。

---

## 8. 输出格式解析

以选择 `warning` 类型、折叠选项为「折叠 −」、标题为「请注意」、内容为两行文字为例，模板最终输出的 Markdown 如下：

```
> [!warning]- 请注意
> 第一行内容
> 第二行内容
```

模板的处理逻辑是：将内容文本框中的每一行，都在行首自动添加 `> `，然后按顺序拼接。这意味着：

- 多行内容被正确处理，每行都有 `>` 前缀
- Windows 换行符（`\r\n`）被统一转换为 `\n`，兼容跨平台使用
- 内容留空时，最后一行同样输出 `> `（一个带空格的空内容行），保持语法完整性

> ⚠️ **注意**
> 模板输出的是纯 Markdown 文本，插入后在阅读视图下即可看到渲染效果。若在编辑视图下看到的仍是原始语法，可切换到阅读视图，或开启「实时预览」（Live Preview）模式查看渲染结果。

---

## 9. 快捷键与效率技巧

### Ctrl+Enter 确认

这是使用频率最高的操作。在标题或内容输入框内，`Ctrl+Enter` 等同于点击「确认插入」，全程可以不移动鼠标：输入标题 → `Tab` 切换到内容框 → 粘贴或输入内容 → `Ctrl+Enter` 完成插入。

### 内容留空的用法

若只需插入一个标注块的「壳」而暂不填写内容，可以在内容框留空，直接确认。这会插入一个仅含标题行和一个空内容行的标注块，之后可在笔记中直接补写内容。

### 与 Obsidian 快捷键组合

若将 Templater 的「插入模板」动作绑定了快捷键，可以构建一套连贯的键盘流程：

1. `快捷键` → 触发 Templater 模板列表
2. 输入模板名称关键字（如「标注」）快速过滤
3. `Enter` 选中模板，弹窗打开
4. 操作弹窗 → `Ctrl+Enter` 确认

整套流程可以在不触碰鼠标的情况下完成。

---

## 10. 常见问题与注意事项

**弹窗没有出现，直接插入了模板原始代码**

这通常意味着 Templater 的 JavaScript 执行功能未开启。进入「设置 → Templater」，确认「Enable Templater」已开启，并检查是否有权限相关的安全提示需要确认。

**标注类型的图标没有显示，只有文字**

弹窗中的图标使用的是 Obsidian 内置的 Lucide 图标库，通过 `setIcon` API 渲染。若图标未显示，可能是 Obsidian 版本较旧（建议升级至 1.0+），或当前主题对图标渲染有特殊处理。此问题不影响最终插入的标注块效果。

**选择了折叠选项但未生效**

确认你选择了折叠选项后，对应按钮的背景应变为强调色（通常是紫色或蓝色）。若按钮颜色没有变化，可能是点击未被识别，重新点击即可。

**内容中包含 Markdown 格式是否会被破坏**

不会。模板仅在每行行首添加 `> ` 前缀，不会对行内容进行任何修改。内容中的加粗、链接、代码等 Markdown 语法均会原样保留，在渲染时正常显示。

**能否在移动端（iOS / Android）使用**

Templater 的弹窗功能依赖 Obsidian 的插件 API，在移动端理论上可运行，但实际体验取决于移动端对弹窗交互的支持程度。部分复杂的 Modal 在移动端显示可能存在布局问题，建议以桌面端为主要使用场景。

> ⚠️ **注意**
> Obsidian 的 Callout 是编辑器内置功能，不依赖任何插件。但本模板依赖 Templater 插件。若在不安装 Templater 的情况下打开包含本模板文件的库，文件内容将以纯文本展示，不会执行任何操作。

---

## 11. 进阶：修改与扩展标注类型

模板在代码顶部以数组形式集中定义了所有标注类型，结构清晰，修改方便。

### 11.1 理解类型定义结构

每个标注类型的定义包含三个字段：

```js
{ type: 'note', label: 'Note', icon: 'pencil' }
```

- `type`：Obsidian 标注语法中 `[!类型]` 的类型标识，直接影响渲染样式
- `label`：弹窗列表中显示的名称，纯展示用，不影响输出
- `icon`：弹窗中显示的图标名称，使用 Lucide 图标库的图标 ID

### 11.2 添加自定义类型

若你的主题或 CSS 片段定义了自定义标注类型，可以直接向数组中追加：

```js
{ type: 'custom', label: '自定义类型', icon: 'star' }
```

Lucide 图标 ID 可在 [lucide.dev](https://lucide.dev) 上查找。

> ⚠️ **注意（特定设置）**
> 自定义标注类型的渲染样式取决于你的主题或自定义 CSS。若当前主题未定义该类型的样式，Obsidian 会回退到默认的灰色样式渲染，而不是报错。

### 11.3 调整显示顺序

数组中类型的顺序即弹窗列表中的显示顺序，直接调整数组元素位置即可改变排列。将最常用的类型移至顶部，可以减少每次操作的滚动距离。

### 11.4 删除不需要的类型

直接删除数组中对应的对象即可。例如若从不使用 `bug` 类型，删除 `{ type: 'bug', … }` 这一行后，该选项将不再出现在弹窗中。

---

## 附录

### 插入 - 标注模板完整模板代码

**用途**：在 Obsidian 笔记中通过图形化弹窗选择标注类型、折叠选项、填写标题与内容，自动生成并插入符合 Obsidian 语法的标注块（Callout）。

> ⚠️ **使用方法**：将下方代码块中的全部内容（从 `<%*` 到最后的 `-%>` ）完整复制，粘贴到一个新建的 Markdown 文件中，确保文件内容仅包含模板代码，不含任何其他文字，保存后放入 Templater 的模板文件夹。

````
<%*
const { setIcon } = tp.obsidian;

const calloutDefs = [
  { type: 'note',     label: 'Note',                          icon: 'pencil'         },
  { type: 'info',     label: 'Info',                          icon: 'info'           },
  { type: 'todo',     label: 'Todo',                          icon: 'check-circle-2' },
  { type: 'tip',      label: 'Tip / Hint / Important',        icon: 'flame'          },
  { type: 'abstract', label: 'Abstract / Summary / Tldr',     icon: 'clipboard-list' },
  { type: 'question', label: 'Question / Help / Faq',         icon: 'help-circle'    },
  { type: 'quote',    label: 'Quote / Cite',                  icon: 'quote'          },
  { type: 'example',  label: 'Example',                       icon: 'list'           },
  { type: 'success',  label: 'Success / Check / Done',        icon: 'check'          },
  { type: 'warning',  label: 'Warning / Caution / Attention', icon: 'alert-triangle' },
  { type: 'failure',  label: 'Failure / Fail / Missing',      icon: 'x'             },
  { type: 'danger',   label: 'Danger / Error',                icon: 'zap'            },
  { type: 'bug',      label: 'Bug',                           icon: 'bug'            },
];

function makeCalloutRow(parent, def, onClick) {
  const row = parent.createEl('div', {
    cls: 'callout anp-row',
    attr: { 'data-callout': def.type }
  });
  const titleEl = row.createEl('div', { cls: 'callout-title' });
  const iconEl  = titleEl.createEl('div', { cls: 'callout-icon' });
  setIcon(iconEl, def.icon);
  titleEl.createEl('div', { cls: 'callout-title-inner', text: def.label });
  if (onClick) row.addEventListener('click', () => onClick(def));
  return row;
}

const result = await new Promise((resolve) => {
  const modal = new tp.obsidian.Modal(app);
  modal.titleEl.setText('插入标注（Ctrl+Enter 确认）');

  modal.contentEl.createEl('style').textContent = `
    .anp-row,
    .anp-row.callout,
    .anp-row[data-callout],
    .anp-trigger-preview,
    .anp-trigger-preview.callout,
    .anp-trigger-preview[data-callout] {
      display: block !important;
      height: auto !important;
      min-height: 0 !important;
      max-height: none !important;
      overflow: visible !important;
      visibility: visible !important;
      opacity: 1 !important;
      margin: 0 !important;
      padding: 0 !important;
    }
    .anp-row .callout-title,
    .anp-trigger-preview .callout-title {
      display: flex !important;
      align-items: center !important;
      height: auto !important;
      min-height: 32px !important;
      padding: 5px 10px !important;
      overflow: visible !important;
      visibility: visible !important;
      opacity: 1 !important;
      gap: 8px !important;
    }
    .anp-row .callout-title-inner,
    .anp-trigger-preview .callout-title-inner {
      display: block !important;
      visibility: visible !important;
      overflow: visible !important;
      white-space: nowrap !important;
      font-size: 13px !important;
    }
    .anp-row .callout-icon,
    .anp-trigger-preview .callout-icon {
      display: flex !important;
      align-items: center !important;
      width: 16px !important;
      height: 16px !important;
      flex-shrink: 0 !important;
    }
    .anp-row .callout-icon svg,
    .anp-trigger-preview .callout-icon svg {
      display: block !important;
      width: 16px !important;
      height: 16px !important;
    }
    .anp-row .callout-content,
    .anp-row .callout-fold,
    .anp-trigger-preview .callout-content,
    .anp-trigger-preview .callout-fold {
      display: none !important;
    }
    .anp-label {
      font-weight: bold;
      font-size: 13px;
      margin-bottom: 6px;
    }
    .anp-trigger {
      display: flex;
      align-items: center;
      gap: 8px;
      padding: 7px 10px;
      border: 1px solid var(--background-modifier-border);
      border-radius: 6px;
      cursor: pointer;
      background: var(--background-secondary);
      transition: border-color 0.15s ease;
      user-select: none;
      min-height: 36px;
      box-sizing: border-box;
    }
    .anp-trigger:hover    { border-color: var(--interactive-accent); }
    .anp-trigger.anp-open { border-color: var(--interactive-accent); border-bottom-left-radius: 0; border-bottom-right-radius: 0; }
    .anp-trigger-placeholder { color: var(--text-muted); font-size: 13px; flex: 1; }
    .anp-trigger-arrow {
      color: var(--text-muted);
      font-size: 12px;
      flex-shrink: 0;
      transition: transform 0.15s ease;
    }
    .anp-trigger.anp-open .anp-trigger-arrow { transform: rotate(180deg); }
    .anp-trigger .anp-trigger-preview {
      flex: 1;
      border-radius: 4px !important;
      pointer-events: none;
    }
    .anp-trigger .anp-trigger-preview .callout-title {
      padding: 2px 8px !important;
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
      max-height: 300px;
      margin-bottom: 14px;
    }
    .anp-list-wrap.anp-open { display: block; }
    .anp-list-inner {
      display: flex;
      flex-direction: column;
      gap: 2px;
      padding: 4px;
    }
    .anp-row {
      border-radius: 4px !important;
      cursor: pointer;
      transition: filter 0.1s ease;
    }
    .anp-row:hover { filter: brightness(1.15); }
    .anp-fold-group { display: flex; gap: 6px; margin-bottom: 14px; }
    .anp-fold-btn {
      flex: 1; padding: 5px 0;
      border-radius: 6px;
      border: 1px solid var(--background-modifier-border);
      background: var(--background-secondary);
      color: var(--text-normal);
      font-size: 13px; cursor: pointer;
      transition: background 0.1s ease, border-color 0.1s ease;
    }
    .anp-fold-btn:hover    { background: var(--background-modifier-hover); }
    .anp-fold-btn.anp-sel  { background: var(--interactive-accent); border-color: var(--interactive-accent); color: var(--text-on-accent); }
    .anp-input {
      width: 100%; box-sizing: border-box; font-size: 13px; padding: 6px;
      margin-bottom: 12px; border-radius: 4px;
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
  let selectedType = null;
  let selectedFold = null;
  let isOpen       = false;

  el.createEl('div', { cls: 'anp-label', text: '标注类型' });

  const trigger     = el.createEl('div', { cls: 'anp-trigger' });
  const placeholder = trigger.createEl('span', { cls: 'anp-trigger-placeholder', text: '请点击选择标注类型…' });
  const arrow       = trigger.createEl('span', { cls: 'anp-trigger-arrow', text: '▾' });
  let   previewRow  = null;

  const listWrap  = el.createEl('div', { cls: 'anp-list-wrap' });
  const listInner = listWrap.createEl('div', { cls: 'anp-list-inner' });

  for (const def of calloutDefs) {
    makeCalloutRow(listInner, def, (chosen) => {
      selectedType = chosen.type;
      isOpen = false;
      listWrap.removeClass('anp-open');
      trigger.removeClass('anp-open');
      placeholder.style.display = 'none';
      if (previewRow) previewRow.remove();
      previewRow = trigger.createEl('div', { cls: 'callout anp-trigger-preview', attr: { 'data-callout': chosen.type } });
      const tEl = previewRow.createEl('div', { cls: 'callout-title' });
      const iEl = tEl.createEl('div', { cls: 'callout-icon' });
      setIcon(iEl, chosen.icon);
      tEl.createEl('div', { cls: 'callout-title-inner', text: chosen.label });
      trigger.appendChild(arrow);
    });
  }

  trigger.addEventListener('click', () => {
    isOpen = !isOpen;
    listWrap.toggleClass('anp-open', isOpen);
    trigger.toggleClass('anp-open', isOpen);
  });

  el.createEl('div', { cls: 'anp-label', text: '折叠选项' });
  const foldGroup = el.createEl('div', { cls: 'anp-fold-group' });
  const foldOpts  = [{ l: '无', v: '' }, { l: '展开 +', v: '+' }, { l: '折叠 −', v: '-' }];
  const foldBtns  = {};
  for (const o of foldOpts) {
    const b = foldGroup.createEl('button', { cls: 'anp-fold-btn', text: o.l });
    foldBtns[o.v] = b;
    b.addEventListener('click', () => {
      if (selectedFold !== null) foldBtns[selectedFold]?.removeClass('anp-sel');
      selectedFold = o.v;
      b.addClass('anp-sel');
    });
  }

  el.createEl('div', { cls: 'anp-label', text: '标题 Title：' });
  const titleInput = el.createEl('input', { attr: { type: 'text', placeholder: '请输入标注标题…', class: 'anp-input' } });

  el.createEl('div', { cls: 'anp-label', text: '内容 Content：' });
  const textarea = el.createEl('textarea', { attr: { placeholder: '在此处粘贴或输入多行内容…', rows: '8', class: 'anp-textarea' } });

  const btnRow     = el.createEl('div', { cls: 'anp-btns' });
  const confirmBtn = btnRow.createEl('button', { text: '确认插入', cls: 'anp-confirm-btn' });
  const cancelBtn  = btnRow.createEl('button', { text: '取消' });
  cancelBtn.style.marginLeft = '8px';

  const confirm = () => {
    if (!selectedType) {
      trigger.style.borderColor = 'var(--color-red)';
      setTimeout(() => { trigger.style.borderColor = ''; }, 1200);
      if (!isOpen) { isOpen = true; listWrap.addClass('anp-open'); trigger.addClass('anp-open'); }
      return;
    }
    const fold = selectedFold !== null ? selectedFold : '';
    resolve({ type: selectedType, fold: fold, title: titleInput.value, content: textarea.value });
    modal.close();
  };
  const cancel = () => { resolve(null); modal.close(); };

  confirmBtn.addEventListener('click', confirm);
  cancelBtn.addEventListener('click',  cancel);
  [titleInput, textarea].forEach(i => i.addEventListener('keydown', e => { if (e.key === 'Enter' && e.ctrlKey) confirm(); }));

  modal.onClose = () => resolve(null);
  modal.open();
  setTimeout(() => titleInput.focus(), 50);
});

if (!result) {
  tR += '';
} else {
  const title   = result.title ?? '';
  let   content = (result.content ?? '').replace(/\r\n/g, '\n');
  content = content.split('\n').map(line => `> ${line}`).join('\n');
  tR += `> [!${result.type}]${result.fold} ${title}\n${content}`;
}
-%>
````

> 📎 **获取完整代码**
>
> 1. 以上即为完整代码，可直接复制使用。
> 2. 关注微信公众号：**Obsidianist**，回复关键词：**标注模板**，后台即会自动回复完整代码文件下载地址，下载后直接放入 Templater 模板文件夹，无需任何额外配置，即可使用。
