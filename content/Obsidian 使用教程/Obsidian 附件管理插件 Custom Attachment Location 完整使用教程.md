---
tags:
  - Obsidian
  - 第三方插件
  - Custom_Attachment_Location
  - 附件
---

# Obsidian 附件管理插件 Custom Attachment Location 完整使用教程

Obsidian 默认的附件管理方式对大多数人来说都不够用。你截了张图粘进笔记，它落在仓库根目录下某个叫 `Pasted image 20240512143022.png` 的文件里；你拖进来一份 PDF，它和那张截图并排待在同一个文件夹；时间一长，几百个附件混在一起，每次想找某张图都像在沙堆里摸针。

Obsidian 自带的「文件与链接」设置里虽然提供了几种附件存储位置的选项，但都是全局一刀切——要么统一放到某个固定文件夹，要么放在笔记旁边，无法根据不同笔记动态生成专属的子文件夹，也无法控制附件的命名规则。

Custom Attachment Location 插件解决的正是这个问题。它允许你用 `${noteFileName}`、`${date:{momentJsFormat:'YYYY-MM'}}`、`${folderPath}` 等动态 Token（变量占位符）来定义附件的存储路径和文件名格式，粘贴或拖入任何附件时，插件会自动把 Token 替换成实际值，将附件存到正确的位置并按你定义的规则命名。更进一步，它还提供了「收集附件」命令，可以把库里散落各处不符合规则的旧附件批量整理到位；在笔记重命名时，自动同步移动和重命名对应的附件文件夹与文件，始终保持库结构整洁有序。

本文从安装配置讲起，逐一说明每个设置项的作用与推荐值，再介绍所有内置 Token 的用法，之后展示几种典型场景的完整配置方案，最后补充进阶用法、常见问题与注意事项。无论你是第一次接触这个插件，还是已经在用但想用得更深，应该都能在对应章节找到所需内容。

---

## 目录

1. [插件背景与核心价值](Obsidian%20附件管理插件%20Custom%20Attachment%20Location%20完整使用教程.md#1.%20插件背景与核心价值)
2. [前提条件](Obsidian%20附件管理插件%20Custom%20Attachment%20Location%20完整使用教程.md#2.%20前提条件)
3. [安装与启用](Obsidian%20附件管理插件%20Custom%20Attachment%20Location%20完整使用教程.md#3.%20安装与启用)
4. [设置界面总览](Obsidian%20附件管理插件%20Custom%20Attachment%20Location%20完整使用教程.md#4.%20设置界面总览)
5. [路径模板：Token 系统详解](Obsidian%20附件管理插件%20Custom%20Attachment%20Location%20完整使用教程.md#5.%20路径模板：Token%20系统详解)
6. [设置项逐一讲解](Obsidian%20附件管理插件%20Custom%20Attachment%20Location%20完整使用教程.md#6.%20设置项逐一讲解)
7. [三种典型配置方案](Obsidian%20附件管理插件%20Custom%20Attachment%20Location%20完整使用教程.md#7.%20三种典型配置方案)
8. [收集附件：整理旧库的利器](Obsidian%20附件管理插件%20Custom%20Attachment%20Location%20完整使用教程.md#8.%20收集附件：整理旧库的利器)
9. [笔记重命名时的自动联动](Obsidian%20附件管理插件%20Custom%20Attachment%20Location%20完整使用教程.md#9.%20笔记重命名时的自动联动)
10. [JPEG 自动转换](Obsidian%20附件管理插件%20Custom%20Attachment%20Location%20完整使用教程.md#10.%20JPEG%20自动转换)
11. [自定义 Token：用 JavaScript 扩展变量](Obsidian%20附件管理插件%20Custom%20Attachment%20Location%20完整使用教程.md#11.%20自定义%20Token：用%20JavaScript%20扩展变量)
12. [路径过滤：Include/Exclude 规则](Obsidian%20附件管理插件%20Custom%20Attachment%20Location%20完整使用教程.md#12.%20路径过滤：Include/Exclude%20规则)
13. [常见问题与排查](Obsidian%20附件管理插件%20Custom%20Attachment%20Location%20完整使用教程.md#13.%20常见问题与排查)
14. [与其他插件的联动](Obsidian%20附件管理插件%20Custom%20Attachment%20Location%20完整使用教程.md#14.%20与其他插件的联动)
15. [版本说明与平台支持](Obsidian%20附件管理插件%20Custom%20Attachment%20Location%20完整使用教程.md#15.%20版本说明与平台支持)

---

## 1. 插件背景与核心价值

Custom Attachment Location 插件最初由 RainCat1998 于 2021 年 10 月创建。2024 年 7 月起，Michael Naumov（GitHub 用户名 mnaoumov）接手维护，此后插件进入了持续活跃的开发阶段，功能大幅扩充，Bug 修复也更为及时。2025 年 12 月，项目主仓库正式迁移至 `mnaoumov/obsidian-custom-attachment-location`，原 `RainCat1998` 仓库作为历史归档保留。插件目前已上架 Obsidian 官方社区插件库，可直接在设置中搜索安装。

这个插件的核心思路借鉴自 Typora——一款以所见即所得闻名的 Markdown 编辑器，Typora 允许用户用 `${filename}` 等变量定义图片的存储路径。Custom Attachment Location 将这套逻辑带进了 Obsidian，并在此基础上扩展了更多 Token 类型、更精细的行为控制和批量整理能力。

对于 Obsidian 用户而言，这个插件的核心价值体现在以下几点。

**附件与笔记一一对应。** 通过 `${noteFileName}` Token，每篇笔记的附件可以自动落入以笔记名命名的专属子文件夹，笔记与其附件始终绑定在一起，移动笔记时附件跟着走，删除笔记时附件可以自动清理，库结构不再混乱。

**命名有意义、可检索。** 告别 `Pasted image 20240512143022.png` 这样毫无意义的文件名，改为 `会议记录-img-20240512.jpg` 这样一眼就能看出归属的命名，附件管理变得真正可用。

**批量整理历史遗留附件。** 对于已有大量散乱附件的老库，插件提供的「收集附件」命令可以按照你设定的规则，把全库或指定范围内的附件一次性整理到正确位置，不需要手动移动文件。

**笔记重命名时联动更新。** 当笔记文件名发生变化时，插件可以自动将对应的附件文件夹和文件名同步更新，链接也随之刷新，避免重命名后出现大量断链。

---

## 2. 前提条件

**Obsidian 版本。** 建议使用 Obsidian 1.4 或以上版本，以确保与插件的完整兼容性。插件的部分功能（如实时路径预览）依赖较新的 API，过旧的版本可能出现功能缺失或异常。

**操作系统。** 插件在 Windows、macOS、Linux 桌面端均可正常使用。Android 移动端基本功能可用，但「自定义 Token」中的 JavaScript 执行环境在移动端有一定限制。iOS 端受系统沙盒机制影响，建议以桌面端为主力配置端。

**仓库备份。** 在首次使用「收集附件」命令或开启「笔记重命名时联动」功能之前，强烈建议先完整备份整个仓库文件夹。这类批量移动操作是不可逆的（Obsidian 的撤销功能不覆盖文件系统操作），备份是最后的保障。

**无需额外依赖。** 本插件不依赖任何其他社区插件，也不需要安装额外的第三方工具或注册任何账号。

---

## 3. 安装与启用

安装过程与所有 Obsidian 社区插件完全相同，步骤如下。

**第一步：关闭安全模式。** 打开 Obsidian「设置」，进入左侧「社区插件」选项卡。如果这是你第一次安装社区插件，页面顶部会提示「安全模式已开启」，点击「关闭安全模式」按钮，然后确认即可。

**第二步：搜索并安装。** 点击「社区插件」页面中的「浏览」按钮，在搜索框中输入 `Custom Attachment Location`，在结果列表中找到由 `RainCat1998` 发布（目前由 mnaoumov 维护）的插件，点击进入详情页，然后点击「安装」。

**第三步：启用插件。** 安装完成后，点击「启用」按钮，或返回「已安装插件」列表，找到 Custom Attachment Location 并将其右侧的开关拨到「开启」状态。

**第四步：进入设置。** 在「已安装插件」列表中，点击 Custom Attachment Location 右侧的齿轮图标，或在 Obsidian 左侧设置栏滚动找到「Custom Attachment Location」选项，进入插件设置页面。

> ⚠️ **注意（与 Obsidian 原生设置的关系）**
> 启用本插件后，「设置 → 文件与链接 → 新建附件的默认位置」这项 Obsidian 原生设置会被插件接管。插件的「Location for New Attachments」设置项会覆盖原生设置，原生设置中的值不再生效。两者不要同时配置，以插件设置为准。

---

## 4. 设置界面总览

插件的设置页面内容较多，按功能划分为以下几个区块：

**路径模板区块（Core）**，包含三个核心设置：附件存储路径、附件文件名模板、Markdown 链接 URL 格式。这是整个插件最核心的配置区域，也是最需要理解 Token 语法的地方。

**移动与重命名区块（Move/renames）**，控制在粘贴/拖入附件时是否重命名、笔记重命名时是否联动附件的文件夹和文件名，以及多笔记共用附件时的移动策略等。

**删除行为区块（Deletion）**，包含孤立附件清理、空文件夹处理等选项。

**特殊字符区块（Special characters）**，处理特殊字符替换、大小写转换等文件名细节。

**收集附件区块（Collected attachments）**，控制使用「收集附件」命令时的重命名行为和多笔记共用附件的处理策略。

**图片处理区块（Images）**，包含默认图片尺寸、JPEG 转换等图片相关选项。

**路径过滤区块（Path）**，通过 Include/Exclude 规则限制插件生效的范围，以及将特定扩展名视为附件的设置。

**自定义 Token 区块（Custom tokens）**，通过 JavaScript 代码定义自己的变量，用于更高级的路径生成逻辑。

**高级设置区块（Advanced）**，包含 Markdown URL 格式和操作超时时间等高级选项。

> 💡 **说明**
> 在正式配置各设置项之前，建议先阅读下一节关于 Token 系统的介绍，因为核心区块和大多数设置项的填写都需要用到 Token 语法。了解 Token 的种类与格式后，再回头配置具体设置项，会顺畅得多。

---

## 5. 路径模板：Token 系统详解

路径模板是这个插件的灵魂所在。在**新附件位置**「Location for New Attachments」和**生成的附件文件名**「Generated attachment file name」两个字段中，你可以输入包含 Token 的模板字符串，插件在保存附件时会将 Token 替换为实际值。

Token 的语法格式是 `${tokenName}` 或 `${tokenName:{…}}`，其中花括号内的部分是 JSON5 格式的参数对象。Token 名称不区分大小写，但参数对象的键值区分大小写。

> ⚠️ **注意（Token 格式说明）**
> 新版本插件（mnaoumov 维护版）对日期等带参数的 Token 采用 JSON5 对象格式，例如 `${date:{momentJsFormat:'YYYY-MM-DD'}}`，而非旧版本的简写格式 `${date:YYYY-MM-DD}`。如果你从旧版本迁移，请按本文示例更新路径模板中的 Token 写法。

### 5.1 内置 Token 完整列表

**笔记相关 Token**

| Token | 说明 | 示例输出 |
|-------|------|----------|
| `${noteFileName}` | 当前笔记的文件名（不含扩展名） | `会议记录` |
| `${noteFilePath}` | 当前笔记在库中的完整路径（含扩展名） | `工作/项目/会议记录.md` |
| `${folderName}` | 当前笔记所在文件夹的名称 | `项目` |
| `${folderPath}` | 当前笔记所在文件夹的完整路径 | `工作/项目` |
| `${heading}` | 光标所在位置上方最近的标题文字，不存在则为空 | `第一章` |

`${heading}` 支持格式参数 `level`，可以指定只匹配特定级别的标题，例如 `${heading:{level:'1'}}` 只获取一级标题，`${heading:{level:'any'}}` 获取任意级别（默认值）。

**附件原始文件相关 Token**

| Token | 说明 | 示例输出 |
|-------|------|----------|
| `${originalCopiedFileName}` | 被复制或拖入文件的原始文件名（不含扩展名） | `screenshot` |
| `${originalCopiedFileExtension}` | 被复制或拖入文件的原始扩展名（不含点号） | `png` |
| `${originalAttachmentFileName}` | 原始附件文件名（含扩展名），可用于文件名模板 | `screenshot.png` |
| `${attachmentFileSize}` | 附件文件的大小 | `123`（字节） |
| `${generatedAttachmentFileName}` | 生成后的附件文件名（不含扩展名），仅用于 Markdown URL 格式字段 | `会议记录-img-20240512` |
| `${generatedAttachmentFilePath}` | 生成后的附件完整路径，仅用于 Markdown URL 格式字段 | `assets/会议记录/会议记录-img.png` |

`${attachmentFileSize}` 支持格式参数，例如 `${attachmentFileSize:{unit:'KB',decimalPoints:2}}` 输出以 KB 为单位并保留两位小数的文件大小。

**日期与时间 Token**

| Token | 说明 | 示例输出 |
|-------|------|----------|
| `${date:{momentJsFormat:'…'}}` | 当前时间，按 Moment.js 格式化 | `2024-05-12` |
| `${noteFileCreationDate:{momentJsFormat:'…'}}` | 当前笔记的创建时间 | `2024-01-01` |
| `${noteFileModificationDate:{momentJsFormat:'…'}}` | 当前笔记的最后修改时间 | `2024-05-12` |

常用的 Moment.js 日期格式：`YYYY` 四位年份、`MM` 两位月份、`DD` 两位日期、`HH` 24 小时制小时、`mm` 分钟、`ss` 秒、`SSS` 毫秒。组合示例：`${date:{momentJsFormat:'YYYYMMDDHHmmss'}}` 会输出 `20240512143022`。

**随机值 Token**

| Token | 说明 | 示例输出 |
|-------|------|----------|
| `${random}` | 随机字符（数字或字母），支持格式参数控制长度与字符类型 | `K7` |
| `${uuid}` | 随机 UUID | `550e8400-e29b-41d4-a716-446655440000` |

`${random}` 支持格式参数，例如 `${random:{length:4,digits:true,letters:false}}` 生成 4 位纯数字随机字符串；`${uuid:{case:'upper',hyphens:false}}` 生成无连字符的大写 UUID。

**Frontmatter Token**

| Token | 说明 | 示例输出 |
|-------|------|----------|
| `${frontmatter:{key:'fieldName'}}` | 读取笔记 Frontmatter 中指定字段的值 | `工作` |

`${frontmatter:{key:'…'}}` 支持点号嵌套读取，例如 `${frontmatter:{key:'meta.author'}}` 可以读取 `meta` 对象下的 `author` 字段；`${frontmatter:{key:'tags.0'}}` 读取 `tags` 数组的第一个元素。

**交互式 Token**

| Token | 说明 |
|-------|------|
| `${prompt}` | 粘贴附件时弹出对话框，由用户手动输入一个值 |

`${prompt}` 适合需要为每个附件单独指定分类的场景，粘贴附件时会弹出输入框，你可以在对话框中预览该附件（Obsidian 支持的图片、视频、PDF 均可预览）。使用频繁时会影响操作流畅度，按需开启。

### 5.2 路径书写规则

路径有两种书写方式，效果不同，需要根据需求选择。

**不加 `./` 前缀（相对于库根目录）：** 路径从仓库根目录开始计算。例如 `assets/${noteFileName}`，无论笔记在库中哪个子目录，附件都会落入库根目录的 `assets/笔记名/` 下。

**加 `./` 前缀（相对于笔记所在目录）：** 路径从当前笔记的位置开始计算。例如 `./assets/${noteFileName}`，笔记在哪个文件夹，`assets` 子文件夹就创建在哪里。这种方式配合 `${folderPath}` 使用时效果最佳。

> ⚠️ **注意**
> 路径不要以 `/` 开头（绝对路径），也不要以 `/` 结尾，否则会导致路径解析错误。正确写法是 `assets/${noteFileName}`，错误写法是 `/assets/${noteFileName}/`。

---

## 6. 设置项逐一讲解

### 6.1 Core

#### 6.1.1 新附件位置（Location for New Attachments）

这是最核心的设置，定义了粘贴或拖入附件时，附件文件应该存储到哪个文件夹。

**默认值：** `./assets/${noteFileName}`（相对于笔记所在目录的 `assets/笔记名/` 子文件夹）

**常用配置举例：**

```
assets/${noteFileName}
```

所有附件统一存在库根目录 `assets/` 下，按笔记名分子文件夹。适合喜欢统一管理附件的用户。

```
./assets/${noteFileName}
```

附件存在笔记旁边的 `assets/笔记名/` 子文件夹中，笔记与附件物理位置紧邻，便于整体移动或导出。

```
${folderPath}/assets/${noteFileName}
```

附件存在笔记所在文件夹的 `assets/笔记名/` 子文件夹中，逻辑与上一种相近，但路径写法更显式。

```
assets/${date:{momentJsFormat:'YYYY'}}/${date:{momentJsFormat:'MM'}}/${noteFileName}
```

按年/月/笔记名三级目录组织附件，适合日记类笔记库。

> 💡 **说明（按需调整）**
> 上述配置均为示例，并非通用推荐。选择哪种路径结构取决于你的库的整体组织方式。如果你的库按项目分文件夹，用 `${folderPath}/assets/${noteFileName}` 更合适；如果你的库是扁平结构，用 `assets/${noteFileName}` 更简洁。关键是与你已有的目录结构保持一致。

#### 6.1.2 生成的附件文件名（Generated Attachment File Name）

定义粘贴或拖入附件时，附件文件的命名规则。

**Obsidian 原生默认值：** `Pasted image ${date:{momentJsFormat:'YYYYMMDDHHmmss'}}`（仅对剪贴板粘贴图片有效）

**插件默认值：** `file-${date:{momentJsFormat:'YYYYMMDDHHmmssSSS'}}`

**常用配置举例：**

```
${noteFileName}-img-${date:{momentJsFormat:'YYYYMMDDHHmmss'}}
```

以笔记名 + 时间戳命名，例如 `会议记录-img-20240512143022.png`，既能体现归属，又不重复。

```
${originalCopiedFileName}-${date:{momentJsFormat:'YYYYMMDDHHmmss'}}
```

保留原始文件名并加时间戳，适合想保留文件来源信息的场景。

```
${noteFileName}-${uuid}
```

以笔记名 + 随机 UUID 命名，彻底避免重名，适合附件数量极大的场景。

> ⚠️ **注意（特定性说明）**
> 文件名模板只影响文件名部分，不包含扩展名——插件会自动保留原始文件的扩展名（或在开启 JPEG 转换时改为 `.jpg`），你不需要在模板中写 `.png` 或 `.jpg`。

### 6.2 Move/renames

#### 6.2.1 重复文件名分隔符（Duplicate Name Separator）

当同名附件要保存到同一位置时，插件会在文件名后加上编号以区分（类似系统的「文件名 (2).png」机制）。此设置控制编号前使用什么字符作为分隔符。

**默认值：空格（` `）**

例如，将文件 `existingFile.pdf` 再次拖入同一位置时，新文件将依次命名为 `existingFile 1.pdf`、`existingFile 2.pdf`，以此类推，取第一个可用的名称。如果你更喜欢 `-` 或 `_` 分隔，可以在这里修改。

#### 6.2.2 附件重命名模式（Attachment Rename Mode）

控制哪些情况下插件会对附件进行重命名处理。有三个选项：

**无（None）：** 插件只控制附件的存储位置，不修改文件名，文件以原始名称（或 Obsidian 默认名）保存。

**仅粘贴的图片（Only pasted images）：** 默认值。只对从剪贴板粘贴的截图（即没有原始文件名的图片对象）进行重命名，直接拖入的文件保留原始文件名。这个选项适合大多数用户——粘贴截图通常名称无意义，需要重命名；而拖入的文件可能已有有意义的名称，不希望被修改。

**全部（All）：** 无论是粘贴、拖入还是通过其他方式添加的附件，全部按照文件名模板进行重命名。适合追求统一命名规范的用户。

#### 6.2.3 是否处理重命名事件（Should Handle Renames）

这是重命名联动功能的总开关。开启后，插件会监听 Obsidian 中的文件重命名事件，并在笔记被重命名时自动触发附件文件夹和附件文件的同步更新。

**默认值：开启（true）**

关闭此项后，即使「Should Rename Attachment Folder」和「Should Rename Attachment Files」都处于开启状态，笔记重命名也不会触发任何附件层面的联动更新。如果你的库规模非常大，担心重命名时引发大量文件操作，可以临时关闭此开关。

> ⚠️ **注意**
> 此设置是 6.2.4 和 6.2.5 的前提条件。「Should Handle Renames」关闭时，6.2.4 和 6.2.5 的设置不会生效，无论它们是否开启。

#### 6.2.4 是否重命名附件文件夹（Should Rename Attachment Folder）

当此选项开启，且路径模板中包含 `${noteFileName}` Token 时：每次你重命名笔记文件，插件会自动将对应的附件文件夹重命名为新的笔记名。

**默认值：开启（true）**

**推荐保持开启。** 如果你的路径模板用了 `${noteFileName}`，那附件文件夹名和笔记名是绑定的，笔记重命名后若不同步更新文件夹名，就会造成逻辑断裂（虽然链接会自动更新，但文件夹名与笔记名不再对应）。

#### 6.2.5 是否重命名附件文件（Should Rename Attachment Files）

与上一项类似，当此选项开启，且文件名模板中包含 `${noteFileName}` Token 时：笔记重命名后，插件会将所有对应附件文件的文件名也按新的笔记名重新生成。

**默认值：关闭（false）**

> 💡 **说明（谨慎开启的原因）**
> 这个功能比文件夹重命名激进得多。附件文件的物理重命名会触发库内所有引用该附件的链接同步更新，在笔记数量多时可能造成一次性的大量文件修改操作。建议先在小范围测试，确认行为符合预期后再全库开启。

#### 6.2.6 重命名的附件文件名（Renamed Attachment File Name）

这是一个独立于「Generated Attachment File Name」之外的文件名模板，专门用于控制**重命名联动时**附件文件的命名方式。

**默认值：空白**

这项设置的存在意义在于：有时你希望新粘贴的附件和因重命名而更新的附件采用不同的命名逻辑。例如，新粘贴时可以带上精确时间戳（`${noteFileName}-img-${date:{momentJsFormat:'YYYYMMDDHHmmss'}}`），而重命名时只需要更新笔记名前缀，不再改动时间部分，就可以在此处使用 `${noteFileName}-img`（配合 `${originalCopiedFileName}` 等保留原有信息）。

> 💡 **说明（按需调整）**
> 大多数情况下保持默认（与 Generated Attachment File Name 一致）即可。只有当你发现重命名联动后的文件名与预期不符时，才需要在此处单独配置。

#### 6.2.7 多笔记共用附件的移动模式（Move Attachment to Proper Folder Used by Multiple Notes Mode）

当一个附件文件被多篇笔记同时引用，而某次操作（重命名或收集）需要将该附件移动到新位置时，插件需要决定如何处理这种「一对多」的情况。此设置提供以下选项：

**跳过（Skip）：** 当检测到该附件被多篇笔记引用时，不对该附件执行任何移动操作，将其保留在原始位置。各笔记的链接也不发生变化。这是最安全的选项，适合不确定共用附件应如何归属的场景。

**复制到所有位置（Copy all）：** 默认值。将附件复制一份到每篇引用它的笔记各自对应的附件文件夹中，各笔记的链接分别指向自己文件夹内的副本。操作结束后，各笔记各持有一份独立的附件副本，彼此不再共用同一文件。选用此选项会增加磁盘存储占用，但可以保证每篇笔记的附件完全独立。

**取消（Cancel）：** 一旦检测到有附件被多笔记共用，立即中止当前整个重命名/移动操作，不进行任何文件变动。这是最保守的选项，适合对库结构改动格外谨慎的用户。

**弹窗询问（Prompt）：** 每当遇到被多笔记共用的附件时，弹出对话框，由用户手动选择处理方式（跳过、复制还是取消）。适合偶尔处理此类情况、希望逐个确认的用户。

**默认值：Copy all（复制到所有位置）**

> 💡 **说明**
> 如果你的库中存在大量被多篇笔记共同引用的附件（如公用的图示、模板图片等），建议在执行大批量操作前先将此项设为「Skip」或「Prompt」，确认好处理逻辑后再执行，避免因 Copy all 模式产生大量重复文件消耗存储空间。如果你明确希望每篇笔记的附件各自独立，可以放心使用「Copy all」模式。

### 6.3 Deletion

#### 6.3.1 空附件文件夹处理（Empty Attachment Folder Behavior）

控制附件被删除或移走后，原本存放它们的空文件夹如何处理。有三个选项：

- **保留（Keep）：** 空文件夹不做处理，保留在原位。适合使用 Git 等版本控制工具管理库，需要保留文件夹结构的用户。
- **删除（Delete）：** 删除变空的附件文件夹本身。
- **删除并删除空父文件夹（Delete with empty parents）：** 默认值。删除变空的附件文件夹，同时向上检查父文件夹，如果父文件夹也因此变空，也一并删除，直到遇到非空文件夹为止。

**推荐保持默认的「删除并删除空父文件夹（Delete with empty parents）」。** 这样库中不会留下空壳文件夹，整体结构更干净。

#### 6.3.2 是否删除孤立附件（Should Delete Orphan Attachments）

开启后，当一篇笔记被删除时，插件会自动检查该笔记引用的附件文件，如果这些附件没有被任何其他笔记引用（即「孤立附件」），则一并删除。

**默认值：关闭（false）**

> ⚠️ **注意（开启前请仔细考虑）**
> 这个功能虽然能保持库的整洁，但也存在误删风险：如果某个附件文件同时被多个笔记引用，但链接写法不一致（如一处用 Wikilink，另一处用相对路径），插件可能无法识别到全部引用，从而误判为孤立并删除。建议在仓库有完整备份的前提下，先小范围测试再全局开启。

### 6.4 Special characters

#### 6.4.1 特殊字符（Special Characters）

定义哪些字符在附件文件名和文件夹名中需要被替换。默认值包含 `#^[]|*\<>:?/` 这些在某些文件系统或 Markdown 语法中有特殊含义的字符。

你可以根据实际情况增减此列表。例如，如果你的笔记名中经常使用圆括号 `()`，而你又不希望附件文件夹名中出现圆括号，可以将其加入此列表。

#### 6.4.2 特殊字符替换（Special Characters Replacement）

被替换时用什么字符串代替。默认值是 `-`（连字符）。

例如，如果笔记名是 `[项目] 会议记录`，默认配置下，附件文件夹名会变为 `-项目- 会议记录`，方括号被替换为了连字符。

> 💡 **说明（按需调整）**
> 如果你的笔记名中不含这些特殊字符，这两项设置不影响实际效果，保持默认即可。如果你希望替换后的文件名更简洁，可以将替换字符串改为空字符串（即直接删除特殊字符，不补任何内容）。

### 6.5 Collected attachments

#### 6.5.1 是否重命名收集的附件（Should Rename Collected Attachments）

开启后，使用「收集附件」命令批量整理时，除了移动文件到正确位置外，也会同时按文件名模板对附件进行重命名。

**默认值：关闭（false）**

> 💡 **说明**
> 如果你的库里有很多已有名称有意义的历史附件，不建议开启此项，否则「收集」命令会把它们都按模板重命名，原始名称信息丢失。只有在你希望全库附件统一按模板命名时才开启。

#### 6.5.2 收集的附件文件名（Collected Attachment File Name）

当「Should Rename Collected Attachments」开启时，此字段控制收集操作中附件文件的命名模板，与「Generated Attachment File Name」独立。

**默认值：空白**

单独设置此字段的场景：例如，你希望新粘贴的附件使用精确到毫秒的时间戳避免重名，而对历史附件进行收集整理时，只需要按 `${noteFileName}-${originalCopiedFileName}` 这样保留原始文件名特征的格式命名，不需要追加时间戳。这时可以在此处填入不含时间戳的模板，而「Generated Attachment File Name」仍保留带时间戳的格式。

> 💡 **说明（按需调整）**
>
> | 项目 | 示例特定值 | 通用替换建议 |
> |------|-----------|-------------|
> | 新粘贴文件名模板 | `${noteFileName}-img-${date:{momentJsFormat:'YYYYMMDDHHmmss'}}` | 保持在「Generated Attachment File Name」中 |
> | 收集文件名模板 | `${noteFileName}-${originalCopiedFileName}` | 在「Collected Attachment File Name」中单独设置 |

#### 6.5.3 收集被多个笔记使用的附件模式（Collect Attachments Used by Multiple Notes Mode）

与 6.2.7 类似，此设置控制「收集附件」命令在处理被多篇笔记共同引用的附件时的行为策略。由于收集操作往往一次性覆盖全库，这里的每个选项的影响范围比单次重命名更广，需要特别注意。

**跳过（Skip）：** 默认值。对被多篇笔记引用的附件跳过处理，保留在原位，不做任何移动或复制。执行收集后，这些共用附件的链接保持不变。在大多数场景下，这是最稳妥的策略，既不会产生重复文件，也不会破坏原有的引用关系。

**移动（Move）：** 将附件移动到当前正在处理的笔记所对应的附件文件夹中，其他引用该附件的笔记中的链接会自动更新为新路径。此模式下，附件最终只保留一份，归属给「收集时最后处理到的那篇笔记」，其他笔记的链接随之指向新位置。这在逻辑上可能令人困惑，使用时需谨慎。

**复制（Copy）：** 将附件复制到每篇引用它的笔记各自对应的附件文件夹中，所有相关笔记最终各持有一份独立副本，链接各自指向自己文件夹内的副本。此模式确保每篇笔记的完整性，但会产生多份重复文件，占用更多存储空间。

**取消（Cancel）：** 一旦遇到被多笔记共用的附件，立即中止整个收集操作，不进行任何文件变动。

**弹窗询问（Prompt）：** 每次遇到共用附件时弹出对话框，由用户手动选择如何处理（跳过、移动、复制或取消）。

**默认值：Skip（跳过）**

> 💡 **说明**
> 收集操作往往是对全库的批量处理，涉及范围比单次重命名更广。对于被多笔记共用的附件，建议在执行全库收集前先确认此处的设置符合预期，以免因 Copy 模式带来大量重复文件消耗存储空间，或因 Move 模式造成引用关系混乱。绝大多数情况下，保持默认的 Skip 模式最为安全。

### 6.6 Images

#### 6.6.1 默认图像大小（Default Image Size）

设置粘贴图片时，Obsidian 在笔记中插入的图片显示宽度（以像素为单位）。

**默认值：空（不设置尺寸）**

当此值留空时，插入的图片链接不携带尺寸信息，例如 `![[image.png]]`，图片按原始尺寸显示。当此值设为具体数字（如 `800`）时，插入的链接会带上宽度参数，例如 `![[image.png|800]]`，图片在笔记中以 800 像素宽度显示。

> 💡 **说明（按需调整）**
> 这项设置适合你经常粘贴高分辨率截图、希望统一控制图片在笔记中的显示大小的场景。常用的推荐值因屏幕和使用习惯而异，`600`~`900` 是大多数宽屏笔记布局下比较合适的范围。如果你的笔记样式本身已通过 CSS 控制图片宽度，则不需要在这里设置。

#### 6.6.2 是否将粘贴图片转换为 JPEG（Convert Images to JPEG Mode）

控制图片粘贴或拖入时是否自动转换为 JPEG 格式，以及转换的范围。此设置有四个选项：

**不转换（None）：** 默认值。所有图片保持原始格式不做任何转换。适合对图像质量要求高、或常有含透明背景图片的用户。

**仅粘贴的 PNG 截图（Only pasted clipboard PNG images）：** 只对从剪贴板粘贴的 PNG 截图进行 JPEG 转换，直接拖入的文件以及其他格式的粘贴图片不受影响。这是最常用的节省空间方案，既能压缩无意义的大尺寸截图，又不影响通过拖拽方式导入的原始素材文件。

**除已是 JPEG 外的所有图片（All images except already JPEG）：** 对所有粘贴或拖入的非 JPEG 图片（包括 PNG、GIF、WebP 等）统一转换为 JPEG 格式。适合希望附件存储格式高度统一的用户。

**全部图片（All images）：** 对所有图片均执行 JPEG 转换，包括已经是 JPEG 格式的文件（会对其重新压缩）。此选项可用于对现有 JPEG 文件统一重新压缩，但会丢失原始 JPEG 文件的质量信息，一般不推荐。

**默认值：None（不转换）**

> ⚠️ **注意**
> JPEG 是有损压缩格式，不支持透明通道。含透明背景的图片（如 UI 图标、Logo 等）转换后透明部分会变为白色，且无法还原。如果你的笔记中频繁嵌入此类图片，建议不要开启 JPEG 转换，或只选择「仅粘贴的 PNG 截图」模式并配合手动排查。

#### 6.6.3 JPEG 质量（JPEG Quality）

`0.1` 到 `1.0` 之间的数值，数值越高质量越好，文件越大。**默认值：0.8**，在质量与文件体积之间取得了较好的平衡，一般不需要调整。如果你的附件以文字截图为主（对清晰度要求高），可以将其调至 `0.9`；如果主要是照片类内容（对体积敏感），可以适当降低至 `0.7`。

### 6.7 Path

#### 6.7.1 包含路径（Include Paths）

只对匹配这些路径的笔记应用插件规则。留空时表示对库中所有笔记生效（即不做路径限制）。

每行填写一条规则，支持两种写法：

**普通字符串（路径前缀匹配）：** 例如填写 `工作/` 表示只对路径以 `工作/` 开头的笔记启用插件。

**正则表达式：** 在字符串两侧加 `/` 即视为正则，例如 `/^工作\//` 与前一种写法效果相同，但正则可以实现更复杂的匹配逻辑。

> 💡 **说明（按需调整）**
>
> | 项目 | 示例特定值 | 通用替换建议 |
> |------|-----------|-------------|
> | 包含路径 | `工作/` | 替换为你希望插件生效的文件夹路径 |
> | 正则写法 | `/^工作\//` | 替换为与你的目录结构匹配的正则表达式 |

#### 6.7.2 排除路径（Exclude Paths）

对匹配这些路径的笔记不应用插件规则，即使这些笔记落在 Include Paths 的范围内也不例外。留空时不排除任何路径。

写法与 Include Paths 相同，支持普通字符串和正则表达式。

**常见用途：** 排除「模板」文件夹（模板文件通常不需要自动处理附件）、排除「归档」文件夹（归档内容不再新增附件，无需插件干预）。

> 💡 **说明（按需调整）**
>
> | 项目 | 示例特定值 | 通用替换建议 |
> |------|-----------|-------------|
> | 排除路径 | `模板/` | 替换为你希望插件忽略的文件夹路径 |

#### 6.7.3 从附件收集中排除路径（Exclude from Attachment Collection）

此设置专门用于「收集附件」命令（`Collect attachments`），其作用范围只影响收集操作，不影响日常粘贴/拖入时的路径处理。

填写的路径或正则表达式所匹配的笔记，在执行全库收集时会被跳过，其附件不参与收集整理。

**典型使用场景：** 你的库中有一个「参考资料」文件夹，其中的附件是从外部来源导入的，文件名和路径都有特定含义，不希望被收集命令统一整理，可以在此处将该文件夹排除在外。

> 💡 **说明（按需调整）**
> 此设置与 6.7.2「Exclude Paths」是独立的两套规则：6.7.2 影响所有插件行为（包括日常粘贴和收集），而此处只影响收集操作。两者可以同时配置，互不干扰。

#### 6.7.4 视为附件的扩展名（Treat as Attachment Extensions）

某些特殊文件在 Obsidian 中以 `.md` 为扩展名，但实际上是附件而非普通笔记，例如 Excalidraw 绘图文件（`.excalidraw.md`）。通过在此处添加这类扩展名，插件会将它们作为附件处理，而不是笔记，从而使路径规则对它们同样生效。

**默认值：** `.excalidraw.md`（已预置 Excalidraw 支持）

如果你使用了其他类似的插件（如 Canvas 相关、特殊格式笔记插件），可以在此处手动添加对应的扩展名，每行一条。

### 6.8 Custom tokens

#### 6.8.1 自定义令牌（Custom Tokens）

这是一个内置代码编辑器区域（带语法高亮），允许你用 JavaScript 编写函数，通过 `registerCustomToken` 注册为自定义 Token，之后就可以像使用 `${noteFileName}` 这样的内置 Token 一样，在「Location for New Attachments」「Generated Attachment File Name」「Markdown URL Format」三个路径/文件名模板字段中调用它。

**默认值：空**

一个最简单的自定义 Token 示例——将笔记名转为全大写：

```js
registerCustomToken('upperName', (ctx) => {
  return ctx.noteFileName.toUpperCase();
});
```

注册后，即可在路径模板中使用 `${upperName}` 这个自定义 Token，其输出就是当前笔记名的大写形式。

> 🚫 **安全警告（务必阅读）**
>
> 自定义 Token 中的 JavaScript 代码直接运行在 Obsidian 的渲染进程中，与 Obsidian 本体享有相同的权限，理论上可以访问本地文件系统、发起网络请求、读写你的整个仓库。这不是插件设计缺陷，而是 Obsidian 社区插件机制的底层特性。因此请严格遵守以下原则：
>
> - **只运行你自己编写、或完全读懂的代码。** 来历不明的代码段请勿直接粘贴执行。
> - **不要从不信任来源复制 Token 代码**，包括匿名论坛帖子、未经核实的教程，以及任何他人发来的代码。
> - **如果代码出现意外行为**（如文件被移动、内容被修改），应立即清空此字段并检查仓库完整性。

> 💡 **说明**
> 此处的代码编辑区域集成了 Prism 语法高亮，Token 语法（`${…}`）会以不同颜色显示，填写路径模板时可以直观区分哪些是 Token、哪些是普通字符串。

自定义 Token 的完整定义语法、`ctx` 上下文对象的全部属性（`ctx.app`、`ctx.obsidian`、`ctx.format`、`ctx.fillTemplate` 等）、同步/异步写法示例以及实用场景，详见第 11 节「自定义 Token：用 JavaScript 扩展变量」。

### 6.9 Advanced

#### 6.9.1 Markdown URL 格式（Markdown URL Format）

这是一个针对特殊需求的高级设置，控制插入笔记的链接格式。**一般用户可以将此字段留空。**

当此字段留空时，插件遵循 Obsidian 原生设置（「使用 Wiki 链接」或「使用相对路径」），行为与你在 Obsidian「文件与链接」设置中配置的一致。

如果希望强制使用 Markdown 格式链接（`![](path)`）而不是 Wikilink（`![[filename]]`），并且附件的链接更加简洁，可以输入以下内容：

```
assets/${noteFileName}/${generatedAttachmentFileName}
```

渲染器会从笔记所在位置出发，去解析这个相对路径。

> ⚠️ **注意（容易配错的陷阱）**
> 如果你发现此字段被自动填入了 `${noteFilePath}` 或其他值，请立即清空。这是旧版本插件在某次异常更新中写入的错误值，会导致附件链接指向笔记本身而不是附件文件，表现为图片无法显示。将此字段清空即可恢复正常。

#### 6.9.2 超时时间（秒）（Timeout in Seconds）

控制所有文件操作（移动、重命名、收集等）的最长等待时间，单位为秒。如果某次操作超出此时间限制，插件会中断并报错。

**默认值：** 5（秒）

如果设置为 `0`，则禁用超时限制，操作会一直等待直到完成。对于大型库执行全库收集时，如果遇到超时报错，可以将此值调大或设为 `0`。

---

## 7. 三种典型配置方案

不同的使用习惯对应不同的配置策略，下面给出三种常见场景的完整配置示例，可以直接参考修改。

### 7.1 方案一：按笔记名分文件夹，统一存放在库根目录的 assets 下

适合喜欢将所有附件集中管理、不希望附件散落在各子目录中的用户。

| 设置项 | 推荐值 |
|--------|--------|
| Location for New Attachments | `assets/${noteFileName}` |
| Generated Attachment File Name | `${noteFileName}-img-${date:{momentJsFormat:'YYYYMMDDHHmmss'}}` |
| Attachment Rename Mode | Only pasted images |
| Should Rename Attachment Folder | 开启 |
| Empty Attachment Folder Behavior | Delete with empty parents |

效果：在笔记「会议记录」中粘贴截图，附件存储在库根目录 `assets/会议记录/会议记录-img-20240512143022.png`。

### 7.2 方案二：附件紧挨笔记存放，便于整体迁移和导出

适合按项目或主题划分目录的库结构，每篇笔记和它的附件是一个可以整体移动的单元。

| 设置项 | 推荐值 |
|--------|--------|
| Location for New Attachments | `./assets/${noteFileName}` |
| Generated Attachment File Name | `${noteFileName}-img-${date:{momentJsFormat:'YYYYMMDDHHmmss'}}` |
| Attachment Rename Mode | Only pasted images |
| Should Rename Attachment Folder | 开启 |
| Empty Attachment Folder Behavior | Delete with empty parents |

效果：笔记「工作/项目 A/会议记录.md」粘贴截图，附件落入 `工作/项目A/assets/会议记录/会议记录-img-20240512143022.png`，附件和笔记始终在同一个项目文件夹里。

> ⚠️ **注意（特定性提示）**
> 以上两种方案的路径均为示例配置，并非通用默认值。路径前缀（`assets/` 或 `./assets/`）和文件名格式都可以根据你的偏好替换。
>
> | 项目 | 示例特定值 | 通用替换建议 |
> |------|-----------|-------------|
> | 附件子文件夹名 | `assets` | 可替换为 `attachments`、`files`、`_resources` 等任意名称 |
> | 文件名中的 `-img-` 标识 | `-img-` | 可替换为 `-`、`_attach_` 或直接省略 |
> | 日期格式 | `YYYYMMDDHHmmss` | 可简化为 `YYYYMMDD`，或改用其他 Moment.js 格式 |

### 7.3 方案三：日记库，按年/月归档附件

适合以日期为主轴组织内容的日记型知识库。

| 设置项 | 推荐值 |
|--------|--------|
| Location for New Attachments | `assets/${date:{momentJsFormat:'YYYY'}}/${date:{momentJsFormat:'MM'}}/${noteFileName}` |
| Generated Attachment File Name | `${noteFileName}-${date:{momentJsFormat:'HHmmss'}}` |
| Attachment Rename Mode | Only pasted images |
| Should Rename Attachment Folder | 开启 |
| Empty Attachment Folder Behavior | Delete with empty parents |

效果：在 2024 年 5 月 12 日的日记中粘贴截图，附件落入 `assets/2024/05/20240512/20240512-143022.png`，按年月形成自然的归档结构。

---

## 8. 收集附件：整理旧库的利器

配置好路径规则之后，新粘贴或拖入的附件会自动按规则存放。但如果你的库里已经积累了大量散乱的历史附件，该怎么办？「收集附件」命令就是为此而生的。

### 8.1 命令说明

打开命令面板（`Ctrl/Cmd + P`），搜索 `Custom Attachment Location`，会看到以下几条命令：

**Collect attachments in current note（收集当前笔记的附件）：** 扫描当前打开的笔记，找出它所引用的所有附件，按照当前的路径规则将它们移动到正确位置，并同步更新笔记中的链接。

**Collect attachments in current folder（收集当前文件夹的附件）：** 对当前笔记所在文件夹内的所有笔记执行上述操作。

**Collect attachments for entire vault（收集全库的附件）：** 对整个库内的所有笔记执行收集操作，是最彻底的整理方式，也是风险最高的操作，执行前务必备份。

### 8.2 执行收集时发生了什么

插件在执行收集时，会逐一扫描每篇笔记中的附件链接，根据路径模板计算出该附件「应当在哪里」，然后将附件移动过去，同时将笔记中的链接更新为新路径。

如果同一个附件文件被多篇笔记引用，插件会根据 6.5.3「收集被多个笔记使用的附件模式」的设置决定行为：默认情况下保留源文件在原位（Skip 模式），各笔记的链接不变；若设为「Copy」模式，则复制到各笔记对应的位置，各链接指向自己的副本。

如果「Should Rename Collected Attachments」设置为开启，收集时还会同时按 6.5.2 中配置的文件名模板对附件进行重命名。

> ⚠️ **注意（执行全库收集前的检查清单）**
>
> 执行「收集全库附件」前，建议完成以下检查：
> 1. 已完整备份仓库（包括 `.obsidian` 文件夹）
> 2. 路径模板已配置完毕并在单篇笔记上测试过效果
> 3. 确认「Should Rename Collected Attachments」的开关状态符合预期
> 4. 关闭 Obsidian 的自动同步插件（如 Remotely Save），避免整理过程中触发同步

---

## 9. 笔记重命名时的自动联动

这是 Custom Attachment Location 相比其他附件管理方案最明显的优势之一：笔记重命名时，附件跟着走。

### 9.1 联动机制

当你在 Obsidian 中将某篇笔记重命名（例如从「会议记录」改为「2024-05-12 项目启动会」），插件会自动完成以下工作：

1. 首先检查「Should Handle Renames」是否开启，这是所有联动功能的总开关。
2. 如果路径模板包含 `${noteFileName}` 且「Should Rename Attachment Folder」已开启，将对应的附件文件夹从 `assets/会议记录/` 重命名为 `assets/2024-05-12 项目启动会/`。
3. 如果文件名模板包含 `${noteFileName}` 且「Should Rename Attachment Files」已开启，将文件夹内的所有附件文件也按「Renamed Attachment File Name」模板重新命名。
4. 自动更新笔记中所有附件链接，指向新路径。

### 9.2 建议开启的组合

对于大多数用户，建议的组合是：「Should Handle Renames」开启，「Should Rename Attachment Folder」开启，「Should Rename Attachment Files」视情况决定。

文件夹的重命名是轻量操作，影响范围有限，出错后容易手动修正，建议常态开启。文件的重命名操作更激进，特别是在有大量附件或复杂引用关系时，建议先在测试库上验证行为，再决定是否在主库开启。

---

## 10. JPEG 自动转换

截图默认以 PNG 格式保存，文件体积较大。对于在笔记中大量嵌入截图的用户，开启 JPEG 自动转换可以显著节省存储空间。

### 10.1 开启方式

在插件设置中找到「Convert images to JPEG mode」，从下拉菜单中选择适合你使用习惯的转换模式（推荐从「Only pasted clipboard PNG images」开始尝试）。同时可以调整「JPEG Quality」（建议保持 0.8 不变，这个质量对大多数截图内容已经足够清晰）。

四种模式的适用对象如下：「None」适合所有不需要压缩的场景；「Only pasted clipboard PNG images」适合日常截图多、拖入文件需保留原格式的用户；「All images except already JPEG」适合希望强制统一存储格式的用户；「All images」适合需要对已有 JPEG 重新压缩的极少数特殊场景。

### 10.2 适用范围

JPEG 转换主要针对从剪贴板粘贴的截图。对于直接拖入的图片文件，是否也进行转换，取决于所选的转换模式：选择「Only pasted clipboard PNG images」时，拖入的图片文件保持原格式；选择「All images except already JPEG」或「All images」时，拖入的图片文件也会被转换为 JPEG。

### 10.3 注意事项

JPEG 是有损压缩格式，不适合存储需要保留精确像素信息的图像，例如包含文字的截图在低质量设置下可能出现模糊，含有透明背景的图标会在转换后透明部分变为白色（因为 JPEG 不支持透明通道）。

> 💡 **说明**
> 如果你的笔记中频繁嵌入含透明背景的 UI 图标、Logo 等图像，不建议开启 JPEG 转换。此类场景保留 PNG 更为合适。对于普通的界面截图、照片类内容，JPEG 转换的效果通常令人满意。

---

## 11. 自定义 Token：用 JavaScript 扩展变量

内置 Token 无法覆盖所有场景。Custom Attachment Location 提供了自定义 Token 功能，允许你编写 JavaScript 代码来定义自己的变量，用于路径和文件名模板。

### 11.1 定义方式

在插件设置页面「Custom tokens」文本区域，编写 JavaScript 代码，使用 `registerCustomToken` 函数注册自定义 Token：

```js
registerCustomToken('projectName', (ctx) => {
  return ctx.noteFileName.split('-')[0];
});
```

注册完成后，即可在路径模板中使用 `${projectName}` 这个自定义 Token。Token 名称同样不区分大小写，使用方式与内置 Token 完全一致。

### 11.2 ctx 对象说明

`ctx`（上下文）对象提供了以下可用属性：

- `ctx.noteFileName`：当前笔记文件名（不含扩展名）
- `ctx.app`：Obsidian 的 App 实例，可访问所有 Obsidian API
- `ctx.obsidian`：Obsidian 模块对象，可使用其工具函数
- `ctx.format`：Token 冒号后面的 format 参数（如 `${myToken:{key:'val'}}` 中的 `{key:'val'}`）
- `ctx.fillTemplate(template)`：异步函数，可在自定义 Token 内部调用其他 Token 进行二次处理

自定义 Token 支持同步和异步两种写法：

```js
// 同步写法
registerCustomToken('upperName', (ctx) => {
  return ctx.noteFileName.toUpperCase();
});

// 异步写法
registerCustomToken('fromFrontmatter', async (ctx) => {
  const file = ctx.app.workspace.getActiveFile();
  if (!file) return 'unknown';
  const cache = ctx.app.metadataCache.getFileCache(file);
  return cache?.frontmatter?.category ?? 'uncategorized';
});
```

> ⚠️ **注意（安全提示）**
> 自定义 Token 的代码是 JavaScript，直接在 Obsidian 的渲染进程中执行。请只使用你信任来源的代码，避免运行来源不明的 Token 脚本，以防潜在的安全风险。

### 11.3 接收格式参数

自定义 Token 也可以像内置 Token 一样接收格式参数。格式参数通过 `ctx.format` 获取，对应模板中 `${myToken:{…}}` 写法里花括号内的 JSON5 对象。

```js
registerCustomToken('prefix', (ctx) => {
  const defaultPrefix = ctx.format?.prefix ?? 'file';
  return `${defaultPrefix}-${ctx.noteFileName}`;
});
```

定义后，可以在路径模板中通过 `${prefix:{prefix:'doc'}}` 这样的方式传入自定义前缀，动态改变 Token 的输出结果。

### 11.4 实用场景示例

**读取 Frontmatter 中的项目名，作为路径层级：** 如果你的笔记 Frontmatter 中有 `project: ProjectA` 这个字段，可以定义一个 Token 读取它，然后在路径模板中用 `${myProject}/assets/${noteFileName}` 来按项目归档附件：

```js
registerCustomToken('myProject', (ctx) => {
  const file = ctx.app.workspace.getActiveFile();
  if (!file) return 'uncategorized';
  const cache = ctx.app.metadataCache.getFileCache(file);
  return cache?.frontmatter?.project ?? 'uncategorized';
});
```

> ⚠️ **注意（特定性提示）**
> 上述代码读取的 `project` 字段是示例特定值，并非通用默认值。请将 `project` 替换为你实际使用的 Frontmatter 字段名，并将 `'uncategorized'` 替换为在字段缺失时你希望使用的回退值。
>
> | 项目 | 示例特定值 | 通用替换建议 |
> |------|-----------|-------------|
> | Frontmatter 字段名 | `project` | 替换为你实际使用的字段名，如 `category`、`type`、`area` 等 |
> | 回退值 | `'uncategorized'` | 替换为字段缺失时希望使用的默认文件夹名 |
> | 路径模板 | `${myProject}/assets/${noteFileName}` | 替换为你的实际目录结构 |

**在 Token 内部调用其他 Token 进行组合处理：** `ctx.fillTemplate` 允许在自定义 Token 中嵌套调用其他 Token，实现更复杂的路径生成逻辑：

```js
registerCustomToken('smartPath', async (ctx) => {
  const yearMonth = await ctx.fillTemplate('${date:{momentJsFormat:\'YYYY-MM\'}}');
  return `${yearMonth}/${ctx.noteFileName}`;
});
```

### 11.5 移动端注意事项

自定义 Token 中的 JavaScript 在 Android 端理论上支持，但复杂的异步逻辑或调用了特定 Obsidian API 的代码可能在移动端出现异常。iOS 端对 JavaScript 执行环境的限制更为严格，建议复杂的自定义 Token 以桌面端为主，移动端保持简单路径模板即可。

---

## 12. 路径过滤：Include/Exclude 规则

如果你不希望插件在库内的所有地方都生效，可以通过路径过滤规则限制其作用范围。各项设置在第 6.7 节已做逐一说明，这里重点补充实际使用中的组合思路。

### 12.1 Include Paths（包含路径）

只对匹配这些路径的笔记应用插件规则。留空时表示对库中所有笔记生效。

支持普通字符串（路径前缀匹配）和正则表达式（在字符串两侧加 `/` 即为正则，如 `/^工作\//`）。

**示例：** 只对「工作」文件夹下的笔记启用附件规则：

```
工作/
```

### 12.2 Exclude Paths（排除路径）

对匹配这些路径的笔记不应用插件规则，即使这些笔记落在 Include Paths 的范围内。留空时不排除任何路径。

**示例：** 排除「模板」文件夹（模板文件通常不需要自动处理附件）：

```
模板/
```

### 12.3 Exclude from Attachment Collection（从附件收集中排除路径）

仅影响「收集附件」命令，不影响日常粘贴/拖入行为。对于某些你希望保持原样、不参与全库整理的笔记或文件夹，在此处设置排除规则。

### 12.4 Treat as Attachment Extensions（额外的附件扩展名）

某些特殊文件在 Obsidian 中以 `.md` 为扩展名，但实际上是附件而非普通笔记，例如 Excalidraw 绘图文件（`.excalidraw.md`）。通过在此处添加这类扩展名，插件会将它们作为附件处理，而不是笔记。

**默认值：** `.excalidraw.md`（已预置 Excalidraw 支持）

---

## 13. 常见问题与排查

### 13.1 粘贴图片后，附件没有按路径模板存放

**首先检查路径模板是否填写正确。** 路径不能以 `/` 开头，也不能以 `/` 结尾；Token 名称需用 `${…}` 包裹，大括号是英文花括号；日期等带参数的 Token 需使用 JSON5 对象格式，例如 `${date:{momentJsFormat:'YYYY-MM-DD'}}`。

**其次检查是否有其他插件干扰。** Paste Image Rename、Image Toolkit 等附件处理插件可能与 Custom Attachment Location 产生冲突，导致其中一个插件的设置被另一个覆盖。遇到异常时，尝试禁用其他附件相关插件，单独测试 Custom Attachment Location。

**确认插件已启用。** 进入「设置 → 社区插件 → 已安装插件」，确认 Custom Attachment Location 的开关为开启状态。

### 13.2 收集附件后，笔记中的图片显示为断链

这通常是路径计算出现了偏差，导致附件被移到了与笔记中链接不匹配的位置。

首先检查路径模板是否使用了相对路径（`./` 前缀），相对路径的基准是笔记所在文件夹，需要与笔记在库中的实际位置匹配。

出现断链后，在 Obsidian 右键断链图片，选择「显示文件」可以找到图片实际存放位置，与笔记中的链接路径对比，找出偏差所在。

如果是批量操作导致的大范围断链，优先从备份恢复，再重新配置路径模板后谨慎测试。

### 13.3 Markdown URL Format 字段出现了奇怪的值

如第 6.9.1 节所述，这是旧版本的一个遗留问题。将该字段清空保存即可。清空后重新粘贴一张图片，确认链接格式恢复正常。

### 13.4 笔记重命名后，附件文件夹没有跟着改名

首先确认「Should Handle Renames」已开启，这是所有联动功能的总开关。然后检查「Should Rename Attachment Folder」是否已开启，以及路径模板中是否包含 `${noteFileName}` Token。三个条件都满足，联动功能才会生效。

另外需要确认路径模板中 Token 的拼写正确（虽然 Token 名不区分大小写，但整体路径模板中不能有语法错误）。

### 13.5 Token 没有被替换，路径中出现了字面量 `${noteFileName}`

这通常是 Token 拼写有误，例如写成了 `{noteFileName}`（缺少 `$` 符号）或 `$(noteFileName)`（使用了错误的括号类型）。检查路径模板中每个 Token 的 `${…}` 语法是否完整。

如果使用的是旧版本的 `${filename}` 写法（RainCat1998 原版插件的 Token 格式），在 mnaoumov 维护的新版本中已更名为 `${noteFileName}`，需要更新。

### 13.6 自定义 Token 报错，路径生成失败

打开 Obsidian 开发者工具（`Ctrl/Cmd + Shift + I`，进入「Console」标签），重新粘贴一张图片触发路径生成，查看控制台中是否有 JavaScript 错误信息。根据错误提示修正自定义 Token 的代码逻辑。

### 13.7 执行收集命令时报超时错误

如果你的库规模较大，执行全库收集时可能触发超时。进入「Advanced」设置区块，将「超时时间」调大（如 `120` 秒），或设为 `0` 禁用超时限制，再重新执行收集命令。

### 13.8 开启 JPEG 转换后，含透明背景的图片出现白色背景

这是 JPEG 格式不支持透明通道的已知限制，无法通过调整插件设置解决。对于频繁使用含透明背景图片的笔记，可以将该笔记所在路径加入「Exclude Paths」，使其不受插件影响；或将「Convert Images to JPEG Mode」改为「None」，不做转换。

---

## 14. 与其他插件的联动

### 14.1 Consistent Attachments and Links

这是另一款专注于维护库内附件与链接一致性的插件（dy-sh/obsidian-consistent-attachments-and-links）。它负责在笔记移动时将附件随之迁移，而 Custom Attachment Location 负责定义新附件的存放规则。两者功能互补，组合使用可以实现「新附件自动归位 + 移动笔记时附件跟着走」的完整方案。

需要注意的是，在同时启用两个插件时，Consistent Attachments and Links 依赖 Obsidian 原生的「新建附件默认位置」设置，而 Custom Attachment Location 会接管这个设置。按照 Consistent Attachments and Links 的官方建议，应先配置好 Custom Attachment Location，再使用 Consistent Attachments and Links 的整理命令，避免两者设置相互覆盖。

### 14.2 Templater

如果你使用 Templater 模板插件，可以在模板中通过调用命令的方式与 Custom Attachment Location 联动：在创建日记或特定类型笔记时，通过 Templater 的 `tp.obsidian` API 触发「收集附件」命令，实现自动化整理。这属于较高级的用法，需要对 Templater 有一定的使用经验。

### 14.3 Paste Image Rename

Paste Image Rename 是另一款专注于粘贴图片重命名的插件，与 Custom Attachment Location 的功能存在重叠。两者同时启用时，可能发生冲突（其中一个插件的规则被另一个覆盖，导致行为不符合预期）。建议只选择其中一款使用，不要同时启用。功能完整性方面，Custom Attachment Location 覆盖面更广，优先选择它。

### 14.4 Excalidraw

Custom Attachment Location 的「Treat as Attachment Extensions」设置默认预置了 `.excalidraw.md`，这意味着插件会将 Excalidraw 绘图文件视为附件来处理，在它被嵌入到笔记中时，路径规则同样适用。如果你的库中大量使用 Excalidraw，这项默认设置能帮助你将绘图文件与对应笔记的附件统一存放。

### 14.5 BRAT（Beta Reviewers Auto-update Tester）

如果你想提前体验最新的 Beta 功能，可以通过 BRAT 插件安装 Custom Attachment Location 的预发布版本。在 BRAT 设置中添加仓库地址 `mnaoumov/obsidian-custom-attachment-location` 即可获取最新的预发布版本，适合愿意接受潜在不稳定性、希望第一时间使用新功能的用户。

---

## 15. 版本说明与平台支持

**当前版本：** 插件在官方社区插件库中持续更新，截至 2026 年 5 月，最新版本已超过 9.26，仓库主地址为 `github.com/mnaoumov/obsidian-custom-attachment-location`。

**Obsidian 版本要求：** 建议使用 Obsidian 1.4 或以上版本。过旧的版本可能缺少插件依赖的 API，导致部分功能无法使用。

**桌面端（Windows / macOS / Linux）：** 完整支持所有功能，包括自定义 Token 的 JavaScript 执行环境、JPEG 转换、收集附件命令等全部特性。

**Android 移动端：** 基本的路径模板、附件重命名功能可用。自定义 Token 中的 JavaScript 执行在 Android 端理论上也支持，但受移动端 Obsidian 环境的限制，复杂的异步 Token 可能出现异常，建议在桌面端配置好后在移动端验证。

**iOS 移动端：** 基本功能可用，但 iOS 的沙盒机制对文件操作有额外限制，「收集附件」等批量文件操作命令在 iOS 端的表现可能不及桌面端稳定。建议将主要配置工作放在桌面端完成，iOS 端以日常粘贴附件的使用场景为主。

> 💡 **说明（Beta 版本安装）**
> 如果你想体验最新的 Beta 功能，可以通过 BRAT 插件（Beta Reviewers Auto-update Tester）安装，在 BRAT 设置中添加仓库地址 `mnaoumov/obsidian-custom-attachment-location` 即可获取最新的预发布版本。
