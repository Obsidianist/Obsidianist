---
tags:
  - Obsidian
  - Quartz
  - Cloudflare
  - GitHub
  - 博客
  - 数字花园
  - 网站
---

# Obsidian 发布方案之 Quartz + Cloudflare：免费搭建个人博客（数字花园）网站，无需编程基础，小白也能上手

很多人用 Obsidian 积累了大量笔记，但这些内容一直锁在本地仓库里，只有自己能看。偶尔想把某篇写得不错的文章分享给别人，要么截图、要么复制粘贴，既麻烦又失去了双向链接、标签导航这些 Obsidian 原生的阅读体验。

Obsidian 官方提供了一项叫做 **Obsidian Publish** 的原生发布服务，月付定价为每个站点 **10 美元/月**（按年订阅则为 8 美元/月，合计 96 美元/年）。它的最大优势是零配置——在 Obsidian 里直接勾选文章点击发布，几秒钟内就能上线，双向链接、图谱、悬浮预览一应俱全，还包含 4 GB 托管空间、自定义域名、密码保护等功能，适合完全不想碰命令行的用户。但它的代价是持续的订阅费用，且样式自定义空间相对有限，数据也托管在 Obsidian 的服务器上，而非你自己掌控。

本文介绍的是另一条路：用 **Quartz + Cloudflare Pages** 搭建一套永久免费、完全自主的发布方案。两条路各有所长，第 2 节会做详细对比，帮你在动手之前做出适合自己的选择。

Quartz 解决的正是这个问题。它是一个开源的静态网站生成器，专门为 Obsidian 用户设计，能把你的 Markdown 笔记直接转换成一个保留双向链接、关系图谱和标签系统的完整网站，发布到互联网上让任何人都可以访问。搭配 Cloudflare Pages 托管，整套方案永久免费，你每次在 Obsidian 里写完笔记、运行一条命令，几分钟后网站就自动更新了。

本文从零开始，完整记录了从安装必要工具、初始化 Quartz、配置 GitHub 仓库，到最终部署上线的每一个操作步骤。读者不需要有任何前端开发背景，只需要按照步骤逐步操作即可。文章后半部分也覆盖了日常使用流程、Frontmatter 格式说明、常见报错排查，以及主题颜色、统计接入等进阶配置。

---

## 目录

1. [Quartz 是什么](Obsidian%20发布方案之%20Quartz%20+%20Cloudflare：免费搭建个人博客（数字花园）网站，无需编程基础，小白也能上手.md#1.%20Quartz%20是什么)
2. [为什么选择 Quartz + Cloudflare Pages](Obsidian%20发布方案之%20Quartz%20+%20Cloudflare：免费搭建个人博客（数字花园）网站，无需编程基础，小白也能上手.md#2.%20为什么选择%20Quartz%20+%20Cloudflare%20Pages)
3. [前提条件与准备工作](Obsidian%20发布方案之%20Quartz%20+%20Cloudflare：免费搭建个人博客（数字花园）网站，无需编程基础，小白也能上手.md#3.%20前提条件与准备工作)
4. [安装 Node.js 与 Git](Obsidian%20发布方案之%20Quartz%20+%20Cloudflare：免费搭建个人博客（数字花园）网站，无需编程基础，小白也能上手.md#4.%20安装%20Node.js%20与%20Git)
5. [安装与初始化 Quartz](Obsidian%20发布方案之%20Quartz%20+%20Cloudflare：免费搭建个人博客（数字花园）网站，无需编程基础，小白也能上手.md#5.%20安装与初始化%20Quartz)
6. [配置 Quartz](Obsidian%20发布方案之%20Quartz%20+%20Cloudflare：免费搭建个人博客（数字花园）网站，无需编程基础，小白也能上手.md#6.%20配置%20Quartz)
7. [配置 GitHub 仓库](Obsidian%20发布方案之%20Quartz%20+%20Cloudflare：免费搭建个人博客（数字花园）网站，无需编程基础，小白也能上手.md#7.%20配置%20GitHub%20仓库)
8. [部署到 Cloudflare Pages](Obsidian%20发布方案之%20Quartz%20+%20Cloudflare：免费搭建个人博客（数字花园）网站，无需编程基础，小白也能上手.md#8.%20部署到%20Cloudflare%20Pages)
9. [日常使用：发布新内容](Obsidian%20发布方案之%20Quartz%20+%20Cloudflare：免费搭建个人博客（数字花园）网站，无需编程基础，小白也能上手.md#9.%20日常使用：发布新内容)
10. [文章格式与发布控制](Obsidian%20发布方案之%20Quartz%20+%20Cloudflare：免费搭建个人博客（数字花园）网站，无需编程基础，小白也能上手.md#10.%20文章格式与发布控制)
11. [绑定自定义域名](Obsidian%20发布方案之%20Quartz%20+%20Cloudflare：免费搭建个人博客（数字花园）网站，无需编程基础，小白也能上手.md#11.%20绑定自定义域名)
12. [进阶配置参考](Obsidian%20发布方案之%20Quartz%20+%20Cloudflare：免费搭建个人博客（数字花园）网站，无需编程基础，小白也能上手.md#12.%20进阶配置参考)
13. [常见问题排查](Obsidian%20发布方案之%20Quartz%20+%20Cloudflare：免费搭建个人博客（数字花园）网站，无需编程基础，小白也能上手.md#13.%20常见问题排查)
14. [参考资源](Obsidian%20发布方案之%20Quartz%20+%20Cloudflare：免费搭建个人博客（数字花园）网站，无需编程基础，小白也能上手.md#14.%20参考资源)

---

## 1. Quartz 是什么

Quartz 是由开发者 Jacky Zhao 开发的开源静态网站生成器，专为把 Obsidian 笔记发布成网站而设计。与一般的博客框架不同，Quartz 理解 Obsidian 的双向链接语法（`[[笔记名]]`）、嵌入（`![[图片]]`）、标签，并在生成的网站中完整还原这些功能，包括可交互的关系图谱、页面间的反向链接列表，以及鼠标悬停时弹出预览的链接卡片。对于长期积累 Obsidian 笔记的用户而言，Quartz 生成的网站不只是 " 把笔记变成网页 "，而是一个保留了知识库完整语义结构的数字花园。

Quartz 还原生支持 Obsidian 的 Callout（标注块）语法，可以把 `> [!note]`、`> [!warning]` 等 Callout 在网站上以对应样式正确显示，无需额外配置。此外，Quartz 内置对 LaTeX 公式渲染（基于 KaTeX）的支持，理工科笔记可以直接带公式发布。Mermaid 图表、代码块语法高亮也均属开箱即用的功能。

目前的主流版本是 **Quartz v4**，相比 v3 完全重写，基于 TypeScript 构建，配置文件更清晰，构建速度大幅提升，官方文档也更为完善。

整个发布流程中涉及三个主要角色：**Quartz** 负责把 Markdown 文件转换成静态 HTML；**GitHub** 负责存储代码，充当版本仓库；**Cloudflare Pages** 负责托管并把网站发布到互联网，每次你把更新推送到 GitHub，Cloudflare 会自动重新构建并发布最新版本。这三者的组合是 Quartz 官方推荐的部署方案，全程免费，国内访问速度也相对友好。

---

## 2. 为什么选择 Quartz + Cloudflare Pages

如果你已经在考虑发布自己的 Obsidian 笔记，这一节可以帮助你判断这套方案是否适合自己。

### 2.1 与 Obsidian Publish 的深度对比

Obsidian 官方提供的 Publish 服务是功能最完整、体验最流畅的发布方案，值得在决定使用 Quartz 之前认真了解。

**定价：** Obsidian Publish 按月计费为每个站点 **10 美元/月**；选择年付则折算为 **8 美元/月**（即 96 美元/年）。学生、教师和非营利组织员工可申请 40% 折扣，折后月付约为 6 美元、年付约为 4.8 美元/月。Obsidian 官网还支持 7 天无理由退款，试用成本较低。

下表从多个维度对两套方案进行对比，供你做出选择：

| 对比维度 | Obsidian Publish | Quartz + Cloudflare Pages |
|---------|-----------------|--------------------------|
| **费用** | $10/月（月付）或 $8/月（年付） | 永久免费 |
| **配置门槛** | 极低，Obsidian 内点击发布，无需命令行 | 中等，初次配置需运行约 10 条命令 |
| **双向链接** | ✅ 原生支持 | ✅ 原生支持 |
| **关系图谱** | ✅ 内置 | ✅ 内置 |
| **悬浮预览** | ✅ 内置 | ✅ 内置 |
| **全文搜索** | ✅ 内置 | ✅ 内置 |
| **Mermaid 图表** | ✅ 支持 | ✅ 支持 |
| **Callout 标注块** | ✅ 支持 | ✅ 支持 |
| **LaTeX 公式** | ✅ 支持 | ✅ 支持（基于 KaTeX） |
| **样式自定义** | 支持自定义 CSS | 完全开源，可修改任意源代码 |
| **自定义域名** | ✅ 支持（含在订阅内） | ✅ 支持（免费） |
| **密码保护** | ✅ 支持（含在订阅内） | ❌ 需自行实现 |
| **SEO 优化** | ✅ 官方内置 | ✅ 内置，可进一步定制 |
| **移动端发布** | ✅ Obsidian 移动端直接操作 | 需借助第三方 Git 应用 |
| **数据所有权** | 托管于 Obsidian 服务器 | 存储在自有 GitHub 仓库 |
| **托管空间** | 4 GB（含在订阅内） | Cloudflare Pages 免费层（无存储上限） |
| **停服风险** | 依赖 Obsidian 服务连续性 | 自主可控，可随时迁移 |

**如何选择：** 如果你愿意为省去配置时间付费，对命令行没有兴趣，并且预算允许每年约 100 美元的持续支出，Obsidian Publish 是更舒适的选择。如果你不排斥一次性花 30～60 分钟完成初始配置，之后希望零成本运营，且有兴趣深度定制网站外观，Quartz 是更经济灵活的方案。

> 💡 **说明**
> 两套方案并不互斥。有用户同时运营 Obsidian Publish 站（用于主要内容，享受极致便捷）和 Quartz 站（用于实验性内容或需要深度定制的项目）。选择并非一刀切，可以根据具体需求灵活组合。

### 2.2 自由度与可定制性

Quartz 是完全开源的项目，网站的样式、布局、组件都可以通过修改源代码来定制。颜色主题、字体、侧边栏内容、页脚信息，都可以精细调整。社区已有开发者将 Obsidian 的主流主题（包括 Kanagawa、Rosé Pine 等）完整移植到 Quartz，一键切换即可让你的网站外观与 Obsidian 库保持一致。

### 2.3 数据所有权

使用 Quartz，你的笔记存储在自己的 GitHub 仓库里，网站文件也在自己的 Cloudflare 项目下，不依赖任何订阅服务，随时可以迁移或备份。

### 2.4 Cloudflare Pages 的选择理由

官方文档列出了 GitHub Pages、Cloudflare Pages、Vercel、Netlify 等多个部署目标。选择 Cloudflare Pages 有几个实际优势：Cloudflare 的 CDN 节点覆盖广，国内访问速度通常优于 GitHub Pages；Cloudflare Pages 不要求仓库必须公开，可以使用私有仓库；Cloudflare 处理 HTML 文件路径的方式（不带尾部斜线）与 Quartz 的链接生成逻辑完全吻合，不会出现 GitHub Pages 上偶发的链接跳转问题。

> ⚠️ **注意（Cloudflare Pages 产品动向）**
> Cloudflare 自 2025 年起已将产品重心从 Pages 转向 Workers，并鼓励新项目使用 Workers 进行部署。目前 Cloudflare Pages 仍可正常创建新项目并使用，Quartz 官方文档也将其列为推荐的部署平台，本文教程流程仍然有效。但需了解：Cloudflare 的新功能会优先落地在 Workers，Pages 主要维持现有功能。如果你在若干年后看到本文，建议在操作前先查阅 Quartz 官方文档（`quartz.jzhao.xyz/hosting`）确认最新推荐的部署方式，再行操作。

当然，这套方案也有自己的边界：初次配置需要安装 Node.js、Git 并运行几条终端命令，对完全没有命令行经验的用户会有一定门槛。如果你从未打开过终端，本文后面的步骤会详细解释每个命令的含义，跟着操作即可。

---

## 3. 前提条件与准备工作

在开始之前，需要确认以下几项。

**账号准备。** 你需要一个 **GitHub 账号**（免费注册，地址：`github.com`）用来存储博客代码，以及一个 **Cloudflare 账号**（免费注册，地址：`dash.cloudflare.com`）用来托管网站。这两项注册只需要邮箱，整个过程无需付费。

**设备要求。** Windows、macOS、Linux 均支持完整的部署流程，本文对三个平台的差异步骤均有说明。移动端无法完成初次配置，但配置完成后日常发布在移动端借助 Git 类应用也可以实现。

**网络。** 部分步骤需要访问 GitHub，建议在网络连接稳定的环境下操作。`npm i` 安装依赖时会从 npm 服务器下载文件，若速度过慢可以考虑设置 npm 镜像（见第 13 节常见问题）。

**已有 Obsidian 仓库（可选）。** 你不需要已有 Obsidian 仓库才能开始。Quartz 初始化时可以选择从空白内容开始，之后再把已有笔记逐步放进来。如果你已有仓库，第 6 节会说明几种不同的内容接入方式。

**基本的文件管理与终端操作能力。** 配置过程中需要找到文件夹路径、在终端中执行命令。如果你从未打开过终端，下面的步骤会一一解释。Windows 用户搜索「cmd」或「PowerShell」可以打开命令行工具，macOS 用户在「应用程序 → 实用工具」中找到「终端」，或者直接用 Spotlight 搜索「Terminal」。

下表是完整部署所需的工具与账号速览：

| 所需项目 | 说明 | 是否需要付费 |
|---------|------|------------|
| Node.js v22 或更高 | Quartz 的运行环境 | 免费 |
| npm v10.9.2 或更高 | Node.js 包管理器（随 Node.js 一起安装） | 免费 |
| Git | 代码版本管理工具 | 免费 |
| GitHub 账号 | 存储代码 | 免费 |
| Cloudflare 账号 | 网站托管 | 免费 |

---

## 4. 安装 Node.js 与 Git

Quartz 的运行依赖 Node.js 和 Git 这两个基础工具，必须在开始安装 Quartz 之前完成这一步。

### 4.1 安装 Node.js

> ⚠️ **注意（版本要求）**
> Quartz v4 要求 **Node.js v22 或更高版本，以及 npm v10.9.2 或更高版本**，官方文档明确指定了这两项最低要求。旧版本（如 v18、v20）在执行 `npm i` 时会报 `EBADENGINE` 错误，请务必安装 v22 而非 LTS 通道的旧版本。npm 会随 Node.js 一起安装，安装 v22 后 npm 通常也会满足版本要求。

**Windows / macOS 安装方式：** 打开 `nodejs.org/zh-cn/download`，选择你的操作系统对应的安装包（Windows 下载 `.msi`，macOS 下载 `.pkg`），双击安装，全程点击 " 下一步 " 即可。macOS 用户也可以用 Homebrew 安装：

```bash
brew install node@22
```

**Linux / macOS 推荐用 nvm 安装**，nvm 是 Node.js 的版本管理工具，方便日后在不同项目中切换版本：

```bash
# 安装 nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

# 重启终端后安装并激活 Node 22
nvm install 22
nvm use 22
```

### 4.2 安装 Git

打开 `git-scm.com/downloads`，选择你的操作系统下载并安装。Windows 安装时所有选项保持默认即可，不需要做额外选择。macOS 用户可以通过 Homebrew 安装：

```bash
brew install git
```

### 4.3 验证安装

安装完成后，打开终端，依次运行以下三条命令：

```bash
node -v
npm -v
git -v
```

三条命令均有输出，且 Node.js 版本显示为 `v22.x.x` 或更高，npm 版本显示为 `10.9.2` 或更高，说明安装成功，可以继续下一步。预期输出类似：

```
v22.14.0
10.9.2
git version 2.47.0
```

> ⚠️ **注意（Windows 常见问题）**
> 如果 `git -v` 提示 " 不是内部或外部命令 "，说明 Git 安装后终端未刷新环境变量。请关闭当前终端窗口，重新打开一个新的终端再执行命令。若问题仍然存在，重启电脑后重试。

---

## 5. 安装与初始化 Quartz

### 5.1 选择存放位置并克隆 Quartz

首先在终端中进入你希望存放博客代码的文件夹。建议放在用户主目录下一个专门的项目文件夹里，方便日后管理。

**macOS / Linux：**

```bash
cd ~
mkdir Projects
cd Projects
```

**Windows（PowerShell）：**

```powershell
cd C:\Users\你的用户名\
mkdir Projects
cd Projects
```

> 💡 **说明（Windows 用户名怎么看）**
> 打开文件资源管理器，在地址栏输入 `C:\Users\` 并回车，看到的文件夹名称即为你的用户名。

进入目标文件夹后，依次运行以下命令：

```bash
# 从 GitHub 下载 Quartz 代码
git clone https://github.com/jackyzha0/quartz.git

# 进入 quartz 文件夹
cd quartz

# 安装依赖（需联网，约 1～3 分钟）
npm i
```

`npm i` 执行期间终端会持续输出下载进度，等待它完成即可，不要中断。

### 5.2 初始化 Quartz 内容目录

依赖安装完成后，运行初始化命令：

```bash
npx quartz create
```

终端会依次出现两个交互式选择题。

**第一个问题**：如何初始化内容目录。

```
? Choose how to initialize the content in `/path/to/quartz/content`
  ❯ Empty Quartz
    Copy an existing folder
    Symlink an existing folder
```

用键盘方向键 ↑↓ 移动光标，按 Enter 确认。三个选项的含义分别是：**Empty Quartz** 从空白开始，你之后手动把笔记放入 `content` 文件夹；**Copy an existing folder** 把现有 Obsidian 库整个复制进来；**Symlink an existing folder** 创建一个符号链接指向现有库，修改立即反映。

> 💡 **说明（如何选择初始化方式）**
> 新手建议选择 **Empty Quartz**，先让整套流程跑通，再把笔记放进去，不容易出错。如果你已有大量笔记且希望立刻全部发布，可以选择 **Copy an existing folder**。如果你熟悉符号链接的概念，**Symlink** 是最方便日常同步的方式——在 Obsidian 里修改笔记，Quartz 的 `content` 目录同步更新，无需手动复制。

**第二个问题**：链接解析方式。

```
? Choose how Quartz should resolve links in your content.
  ❯ Treat links as shortest path
    Treat links as relative paths
    Treat all links as absolute paths
```

选择第一项 **Treat links as shortest path**，这与 Obsidian 的默认链接行为完全一致。

### 5.3 本地预览效果

初始化完成后，运行以下命令在本地预览博客样式：

```bash
npx quartz build --serve
```

终端输出 `Started a Quartz server listening at http://localhost:8080` 后，打开浏览器访问 `http://localhost:8080` 即可看到博客的初始外观。

按 `Ctrl + C` 可以停止预览服务器，之后继续进行后续配置步骤。

---

## 6. 配置 Quartz

### 6.1 打开配置文件

Quartz 的核心配置集中在根目录下的 `quartz.config.ts` 文件里。用你习惯的文本编辑器打开它，推荐使用 VS Code（`code.visualstudio.com`），如果没有安装，用系统自带的记事本（Windows）或文本编辑（macOS）也可以完成基础修改。

找到文件的方式：在文件管理器中打开第 5 节里 `cd quartz` 进入的那个文件夹，里面有 `quartz.config.ts`、`package.json` 等文件，右键点击 `quartz.config.ts` 选择用编辑器打开即可。

### 6.2 修改核心配置项

打开文件后，找到 `configuration: { … }` 部分，需要修改以下几个字段：

```typescript
configuration: {
  pageTitle: "我的数字花园",         // 改成你想要的网站名称
  pageTitleSuffix: "",               // 网站标题后缀，可留空
  enableSPA: true,                   // 单页应用模式，保持默认
  enablePopovers: true,              // 悬浮预览，保持默认
  analytics: null,                   // null 表示暂不启用统计
  locale: "zh-CN",                   // 改成 zh-CN，让日期显示为中文格式
  baseUrl: "your-site.pages.dev",    // 暂时先不改，部署后再填写
  ignorePatterns: ["private", "templates", ".obsidian"],
  defaultDateType: "modified",
  // ...其余字段
}
```

> 💡 **说明（按需调整）**
> 上面的 `pageTitle: "我的数字花园"` 是示例名称，请替换为你自己的博客名称。`baseUrl` 字段暂时不用填写，等第 8 节完成 Cloudflare 部署后会拿到实际域名，届时再回来填入并重新同步即可。

**baseUrl 填写格式说明：** 这个字段仅用于站点地图、RSS 订阅等需要绝对路径的功能。填写时不要加 `https://`，不要有首尾的 `/`。正确示例：`myblog.pages.dev`；错误示例：`https://myblog.pages.dev/`。

**ignorePatterns 说明：** 这个字段控制哪些文件夹不会被发布到网站。默认值已经包含了 `private`（私有笔记）、`templates`（模板文件夹）和 `.obsidian`（Obsidian 配置文件夹），这三个文件夹内的内容不会出现在网站上。

> 💡 **说明（按需调整）**
> `ignorePatterns` 中的文件夹名称是示例值，对应的是配置中预设的文件夹名称，并非 Quartz 的固定规则。如果你的 Obsidian 库中用来存放私稿的文件夹叫别的名字（例如 `草稿` 或 `inbox`），请将 `private` 替换为你实际使用的文件夹名称。你也可以在列表中添加多个文件夹名，例如 `["草稿", "模板", ".obsidian"]`。
>
> | 项目 | 示例值 | 替换建议 |
> |------|--------|---------|
> | 私有内容文件夹名 | `private` | 替换为你实际的草稿/私有文件夹名称 |
> | 模板文件夹名 | `templates` | 替换为你 Templater 或 Templates 插件设置的模板文件夹名称 |

### 6.3 把笔记放进 Quartz

Quartz 读取的内容存放在 `quartz/content/` 文件夹里，有以下几种方式把笔记放进去：

**方式 A：直接复制（新手推荐）。** 把 Obsidian 库中想公开发布的 `.md` 文件，复制粘贴到 `quartz/content/` 文件夹里。这是最稳妥的方式，不会影响你原始的 Obsidian 库。

**方式 B：把 content 文件夹作为 Obsidian 库打开。** 在 Obsidian 中点击左下角「打开其他库」→「打开文件夹作为库」，选择 `quartz/content` 这个文件夹。这样你在 Obsidian 里写的笔记就直接存到了 Quartz 的内容目录，不需要额外复制，日常使用最方便。

**方式 C：初始化时选择 Symlink（进阶）。** 如果在 5.2 节初始化时选择了 `Symlink an existing folder`，则已经将你的 Obsidian 库软链接到 `content` 目录，两边是同一份文件，无需任何额外操作。

**必须创建首页文件。** 无论哪种方式，都需要在 `content/` 根目录下创建一个名为 `index.md` 的文件作为博客首页。内容可以很简单：

```
---
title: 首页
---

# 欢迎来到我的博客

这里是我记录知识和想法的地方。
```

如果 `content/` 里没有 `index.md`，本地预览时首页会显示空白。

---

## 7. 配置 GitHub 仓库

GitHub 在这套方案中充当代码仓库：Quartz 的代码和你的 Markdown 笔记都存储在这里，Cloudflare Pages 会监听这个仓库，每次有新提交就自动触发重新构建和部署。

### 7.1 配置 Git 身份信息

如果你是第一次在这台电脑上使用 Git，需要先告诉 Git 你的姓名和邮箱，这些信息会出现在提交记录里：

```bash
git config --global user.name "你的名字"
git config --global user.email "你的邮箱@example.com"
```

建议使用与 GitHub 账号注册时相同的邮箱，方便提交记录与账号关联。

### 7.2 在 GitHub 上新建仓库

登录 GitHub，点击右上角「+」号 →「New repository」，按以下方式填写：

仓库名可以任意填写（如 `my-blog`），建议用纯英文小写字母，避免中文和特殊字符。可见性选择 **Public**（公开）。

> ⚠️ **注意**
> 创建时不要勾选「Add a README file」、「.gitignore」、「Choose a license」这三项，保持全部未勾选，否则仓库会产生一个初始提交，导致后续推送 Quartz 代码时产生冲突。

填写完成后点击「Create repository」。

### 7.3 配置 SSH 连接 GitHub

SSH 连接方式一旦配置好，之后推送代码不需要每次输入密码，是更稳定、安全的连接方式。

**第一步：检查是否已有 SSH 密钥。**

```bash
ls ~/.ssh
```

如果看到 `id_ed25519` 和 `id_ed25519.pub` 这两个文件，说明已有密钥，可以跳到第三步。

**第二步：生成新密钥。**

```bash
ssh-keygen -t ed25519 -C "你的邮箱@example.com"
```

连续按三次回车（接受默认路径，密码留空），终端显示随机密钥图表示生成成功。

**第三步：复制公钥内容。**

macOS / Linux：

```bash
cat ~/.ssh/id_ed25519.pub
```

Windows（PowerShell）：

```powershell
Get-Content "$env:USERPROFILE\.ssh\id_ed25519.pub"
```

输出以 `ssh-ed25519` 开头的一长串文本，全部选中并复制。

**第四步：把公钥添加到 GitHub。**

登录 GitHub，点击右上角头像 → **Settings** → 左侧菜单 **「SSH and GPG keys」** → **「New SSH key」**。Title 填写任意名称（如「我的电脑」），Key type 选 **Authentication Key**，将复制的公钥粘贴到 Key 字段，点击「Add SSH key」。

**第五步：测试连接。**

```bash
ssh -T git@github.com
```

看到 `Hi 你的用户名! You've successfully authenticated` 说明连接成功。如果出现 `Are you sure you want to continue connecting?` 的提示，输入 `yes` 回车即可。

### 7.4 将 Quartz 推送到 GitHub

回到终端，确认当前在 `quartz` 目录中，运行以下命令：

```bash
# 查看当前远程仓库地址
git remote -v

# 将 origin 改为你自己的 GitHub 仓库地址（SSH 格式）
git remote set-url origin git@github.com:你的用户名/仓库名.git

# 添加 upstream，用于日后接收 Quartz 官方更新
git remote add upstream https://github.com/jackyzha0/quartz.git

# 验证设置是否正确
git remote -v
```

> 💡 **说明（按需调整）**
> 命令中的 `你的用户名` 和 `仓库名` 需要替换为你的实际 GitHub 用户名和第 7.2 节创建的仓库名。GitHub 用户名是登录后个人主页 URL 中的那段，例如 `github.com/zhangsan` 中的 `zhangsan`，不是昵称。

`git remote -v` 应当显示类似以下内容：

```
origin    git@github.com:你的用户名/仓库名.git (fetch)
origin    git@github.com:你的用户名/仓库名.git (push)
upstream  https://github.com/jackyzha0/quartz.git (fetch)
upstream  https://github.com/jackyzha0/quartz.git (push)
```

设置正确后，执行第一次推送：

```bash
npx quartz sync --no-pull
```

第一次推送使用 `--no-pull` 参数，避免因为远端仓库为空而产生合并冲突。看到 `Done!` 或类似提示说明推送成功。

> ⚠️ **注意（推送失败时的处理）**
> 如果提示缓冲区不足，执行以下命令后重试：
> ```bash
> git config http.postBuffer 524288000
> npx quartz sync --no-pull
> ```
> 如果提示权限错误，检查 SSH 密钥是否已添加到 GitHub，并重新运行 `ssh -T git@github.com` 确认连接。

---

## 8. 部署到 Cloudflare Pages

### 8.1 创建 Pages 项目并连接 GitHub

登录 Cloudflare Dashboard（`dash.cloudflare.com`），在左侧菜单找到 **「Compute (Workers)」** → **「Workers & Pages」**，点击 **「Create application」**。

> ⚠️ **注意（Cloudflare 界面变更）**
> Cloudflare 在 2025 年底更新了创建入口界面。点击「Create application」后，你会看到一个名为 **「Ship something new」** 的页面，这个页面是用来创建 Workers 的，不是 Pages。不要在这里点击「Continue with GitHub」——那样会创建 Worker 项目而非 Pages 项目。
>
> 正确做法：**忽略该页面上所有选项**，直接找到并点击页面**底部**的 **「Looking to deploy Pages? Get started」** 链接，才会进入真正的 Pages 部署流程。
>
> 如果误点创建了 Worker 项目，可以回到「Workers & Pages」列表，进入错误创建的项目，在「Settings」底部找到「Delete」，输入项目名称确认删除，然后重新操作。删除 Worker 项目不会影响你的 GitHub 仓库。

### 8.2 连接 GitHub 账号

进入 Pages 部署流程后，点击 **「Connect to Git」** → **「Connect GitHub」**，在弹出的 GitHub 授权页面中点击 **「Authorize Cloudflare Pages」**。

授权方式可以选择「All repositories」（授权全部仓库，方便日后管理多个站点）或「Only select repositories」（仅授权你创建的这个仓库）。点击「Save」完成授权后，页面会显示你的 GitHub 仓库列表，选择第 7.2 节创建的仓库，继续下一步。

### 8.3 配置构建参数

进入「Set up builds and deployments」配置页面，按照下表填写：

| 配置项 | 填写内容 |
|--------|---------|
| Production branch（生产分支） | `v4` |
| Framework preset（框架预设） | `None`（不选任何框架） |
| Build command（构建命令） | `npx quartz build` |
| Build output directory（输出目录） | `public` |

> ⚠️ **注意（Production branch 必须填 v4）**
> Quartz 使用 `v4` 作为主分支名称，不是常规的 `main` 或 `master`。如果这里填错，Cloudflare 监听不到你的代码更新，每次推送后网站都不会自动重新部署。

填写完成后点击 **「Save and deploy」**，Cloudflare 开始自动构建，通常需要 1～3 分钟。

### 8.4 查看部署结果与获取域名

构建成功后，Cloudflare 会为你的项目分配一个免费域名，格式类似：

```
https://你的项目名-随机字符.pages.dev
```

点击这个地址即可看到已经上线的博客。记下这个域名，下一步需要把它填回 Quartz 的配置文件里。

### 8.5 将域名填回配置文件

拿到 Cloudflare 分配的域名后，回到本地的 `quartz.config.ts`，找到第 6 节中留空的 `baseUrl` 字段，填入实际域名（不含 `https://`）：

```typescript
baseUrl: "你的项目名-随机字符.pages.dev",
```

保存后，在终端中运行：

```bash
npx quartz sync
```

Cloudflare 检测到代码更新后会自动触发重新构建。至此，完整的部署流程全部完成。

---

## 9. 日常使用：发布新内容

配置完成后，日常的发布流程极为简单。每次在 Obsidian 里写完新笔记或修改了已有文章，想把更新推送到网站，只需要在终端中运行一条命令：

**macOS / Linux：**

```bash
cd ~/Projects/quartz
npx quartz sync
```

**Windows（PowerShell）：**

```powershell
cd C:\Users\你的用户名\Projects\quartz
npx quartz sync
```

> 💡 **说明（按需调整）**
> 上面的路径 `~/Projects/quartz` 是第 5.1 节中建议的存放位置，如果你当时选择了其他路径，替换为实际路径即可。`~/` 在 macOS / Linux 中代表用户主目录，在 Windows 中等同于 `C:\Users\你的用户名\`。

`npx quartz sync` 执行后，Cloudflare Pages 会自动检测到 GitHub 上的新提交，1～2 分钟内完成重新构建和发布，打开网站即可看到最新内容。

### 9.1 借助插件实现免终端发布

如果你安装了 Obsidian 的 **Git** 社区插件（由 Vinzent 开发），也可以在 Obsidian 内直接点击工具栏按钮完成推送，不需要打开终端。该插件支持定时自动提交，是桌面端最流畅的日常发布体验。

> ⚠️ **注意（移动端限制）**
> Obsidian Git 插件在移动端（iOS / Android）通过 isomorphic-git（一个 JavaScript 版的 Git 实现）运行，并非原生 Git，存在已知的性能问题和功能限制——在文件数量较多的仓库中，检测文件变更会非常耗时（数千文件的仓库可能需要数分钟），且 SSH 认证在移动端不受支持，只能使用 HTTPS + Personal Access Token 方式连接 GitHub。如果你的仓库规模较大，移动端的发布体验可能并不理想，仍建议以桌面端为主。

此外，社区还有一款专为 Quartz 设计的 **Quartz Syncer** 插件（由 saberzero1 开发），可以在 Obsidian 内直接把指定文件夹的笔记推送到 GitHub 上的 Quartz 仓库，适合 " 主库与发布内容分离 " 的工作流——你的 Obsidian 库保持不变，Quartz Syncer 只将你选定的笔记同步过去，无需手动移动或复制文件。该插件通过 GitHub REST API 进行操作，也适用于移动端。在 Obsidian 社区插件库中搜索「Quartz Syncer」即可找到。

以下是几条常用命令的速查表：

| 命令 | 作用 |
|------|------|
| `npx quartz build --serve` | 本地预览博客（访问 `http://localhost:8080`） |
| `npx quartz build` | 仅构建静态文件，不推送也不启动服务器 |
| `npx quartz sync` | 推送更新到 GitHub，自动触发 Cloudflare 重新部署 |
| `npx quartz sync --no-pull` | 第一次推送时使用，避免空仓库合并冲突 |
| `npx quartz update` | 从 upstream 拉取 Quartz 官方更新 |

---

## 10. 文章格式与发布控制

Quartz 支持在每篇 Markdown 文件顶部添加 Frontmatter（前置属性），通过 YAML 格式的键值对来控制文章在网站上的显示行为。这套系统与 Obsidian 的 Properties 功能完全兼容。

常用的 Frontmatter 字段如下：

```yaml
---
title: 我的第一篇文章        # 网站上显示的文章标题（不填则用文件名）
date: 2024-01-15             # 创建日期，影响排序和显示
tags:
  - 笔记
  - 学习
draft: true                  # 设为 true 则该文章不会发布到网站
---

正文内容从这里开始...
```

**关于 draft 字段。** 将某篇笔记的 `draft` 设为 `true`，这篇文章会被 Quartz 跳过，不会出现在网站的任何页面上。这是控制个别文章是否发布最干净的方式，不需要移动文件的位置。

**关于私有文件夹。** 除了 `draft` 字段，也可以把不想发布的笔记放入 `content/private/` 文件夹，Quartz 会完整忽略这个文件夹及其子文件夹内的所有内容。两种方式可以配合使用：`private` 文件夹适合整批隐藏，`draft: true` 适合在正常文件夹内临时隐藏单篇文章。

> 💡 **说明（按需调整）**
> `content/private/` 是 Quartz 在 `quartz.config.ts` 的 `ignorePatterns` 配置中预设的忽略文件夹，`private` 这个名称本身并没有特殊含义，完全可以改用其他名称（如 `草稿` 或 `inbox`）。修改的地方是 `quartz.config.ts` 的 `ignorePatterns` 数组，与实际文件夹名称保持一致即可。

**图片的存放位置。** Quartz 能正确处理 Obsidian 的图片嵌入语法（`![[图片.png]]`），但前提是图片文件必须存放在 `content/` 目录内。建议在 `content/` 下创建 `images/` 子文件夹统一存放附件，同时在 Obsidian「设置 → 文件与链接」中将「新附件的默认存放位置」改为 `当前文件所在文件夹` 或 `指定文件夹`，并将路径设为 `images`，确保每次粘贴图片时 Obsidian 自动存放到正确位置。

**关于 Callout 标注块。** Quartz 原生支持 Obsidian 的 Callout 语法，无需任何额外配置。你在 Obsidian 里使用的 `> [!note]`、`> [!warning]`、`> [!tip]` 等标注块，在发布后会以对应的样式正确渲染。如果你大量使用 Callout 来组织笔记内容，发布后的阅读体验与 Obsidian 内部高度一致。

---

## 11. 绑定自定义域名

如果你拥有自己购买的域名（如 `myblog.com`），可以将其绑定到 Cloudflare Pages，替换掉默认的 `.pages.dev` 域名。

在 Cloudflare Pages 的项目页面中，点击 **「Custom domains」**（自定义域名）→ **「Set up a custom domain」**，输入你的域名，按照页面提示配置 DNS 记录。如果你的域名本身也托管在 Cloudflare，整个过程几乎自动完成；如果域名在其他注册商，需要在那边的 DNS 后台手动添加 Cloudflare 提供的 CNAME 或 A 记录。

完成域名绑定后，回到本地的 `quartz.config.ts`，将 `baseUrl` 更新为你的自定义域名：

```typescript
baseUrl: "myblog.com",   // 不含 https://，不含结尾斜线
```

保存后运行 `npx quartz sync` 重新部署即可。

> 💡 **说明（SSL 证书）**
> Cloudflare 会自动为自定义域名签发并续期 SSL 证书，无需额外配置，域名绑定完成后通常几分钟内即可通过 HTTPS 访问。

---

## 12. 进阶配置参考

这一节涵盖几项常见的进阶设置，均为可选内容，不影响基础部署流程，可以在网站上线后按需逐步调整。

### 12.1 自定义颜色主题与字体

在 `quartz.config.ts` 的 `theme` 部分可以修改字体和配色方案。字体字段接受 Google Fonts 上的任意字体名称，配色字段分别对应亮色模式和暗色模式：

```typescript
theme: {
  typography: {
    header: "Schibsted Grotesk",   // 标题字体
    body: "Source Sans Pro",        // 正文字体
    code: "IBM Plex Mono",          // 代码字体
  },
  colors: {
    lightMode: {
      light: "#faf8f8",             // 页面背景色
      secondary: "#284b63",         // 链接颜色
    },
    darkMode: {
      light: "#161618",
      // ...
    },
  },
},
```

修改后运行 `npx quartz build --serve` 本地预览确认效果，满意后再 `npx quartz sync` 推送到线上。

> 💡 **说明（Obsidian 主题移植）**
> 社区已有开发者将多款 Obsidian 主流主题完整移植到 Quartz，涵盖 Kanagawa、Kanagawa Paper、Rosé Pine 等，可以在 `emilebangma.com/Quartz/` 找到对应资源。如果你在 Obsidian 里长期使用某款主题，移植版能让你的网站与知识库保持视觉一致性。

### 12.2 接入网站访问统计

Quartz 支持 Plausible、Umami、Google Analytics 等多种分析服务，在 `quartz.config.ts` 的 `analytics` 字段中配置即可。将 `analytics: null` 修改为对应服务的配置对象后重新部署，具体各服务的配置格式可以参考 Quartz 官方文档（`quartz.jzhao.xyz`）。

### 12.3 接收 Quartz 官方更新

Quartz 持续迭代，会修复 bug 并添加新功能。在第 7.4 节中已经添加了 `upstream` 远程仓库，当 Quartz 官方发布更新后，可以运行以下命令将更新合并到你的本地版本：

```bash
git fetch upstream
git merge upstream/v4

npm i
npx quartz sync
```

合并时可能会出现冲突，通常是 `quartz.config.ts` 中你修改过的字段与官方更新产生冲突。冲突文件中会出现 `<<<<<<< HEAD` 和 `>>>>>>> upstream/v4` 标记，手动保留你自己的修改部分，删除标记行，保存后再继续推送。

> ⚠️ **注意**
> 如果你修改了 Quartz 的组件源代码（`quartz/components/` 下的文件），更新时冲突的概率更高，需要仔细对比官方修改和你的自定义内容。建议尽量只通过 `quartz.config.ts` 进行配置调整，减少直接修改框架代码，以便更顺滑地接收官方更新。

### 12.4 设置 Cloudflare 构建时 git 时间戳

Cloudflare Pages 默认执行浅克隆（shallow clone），只拉取最近一次提交，这会导致 Quartz 无法读取文件的 git 历史来准确计算「最后修改时间」，所有文章的日期可能显示不正确。

解决方式是将构建命令从 `npx quartz build` 改为：

```
git fetch --unshallow && npx quartz build
```

在 Cloudflare Pages 的项目设置中，找到「Settings → Builds & deployments → Build configurations」，将 Build command 更新为上面这条命令后保存即可。

> 💡 **说明**
> 这条命令先拉取完整的 git 历史记录（`git fetch --unshallow`），再执行 Quartz 构建，能确保每篇文章的修改时间戳准确。如果你在 Frontmatter 里为每篇文章手动填写了 `date` 字段，也可以不做此修改——Quartz 会优先使用 Frontmatter 中的日期值。

---

## 13. 常见问题排查

### 13.1 运行 `npm i` 报错 EBADENGINE

这个错误说明 Node.js 版本过低，不满足 Quartz v4 的最低要求（v22+）。运行 `node -v` 确认当前版本，如果低于 v22，参考第 4.1 节用 nvm 安装 v22：

```bash
nvm install 22
nvm use 22
```

安装完成后，在 `quartz` 目录中重新运行 `npm i`。如果版本已经是 v22 但仍报错，可以先清除缓存再重试：

```bash
npm cache clean --force
npm i
```

### 13.2 `npm i` 下载速度极慢

npm 默认连接海外服务器，国内网络环境下速度有时不理想。可以临时切换到淘宝镜像：

```bash
npm i --registry https://registry.npmmirror.com
```

也可以永久切换镜像（注意：使用镜像后官方包的最新版本可能有延迟）：

```bash
npm config set registry https://registry.npmmirror.com
```

### 13.3 `npx quartz sync` 提示权限错误

常见原因是 SSH 密钥未正确添加到 GitHub，或当前仓库地址不是 SSH 格式。依次检查：运行 `ssh -T git@github.com` 测试连接；运行 `git remote -v` 确认 origin 地址以 `git@github.com:` 开头，而非 `https://`。如果 origin 地址是 HTTPS 格式，运行以下命令切换为 SSH：

```bash
git remote set-url origin git@github.com:你的用户名/仓库名.git
```

### 13.4 Cloudflare 构建失败

在 Cloudflare Pages 项目页面点击失败的部署记录，查看完整构建日志定位具体错误。

**最常见的原因一：Production branch 填写错误。** 检查「Settings → Builds & deployments」中的 Production branch 是否为 `v4`，不是 `main` 或 `master`。

**最常见的原因二：git 时间戳问题。** 如果日志中出现与时间戳相关的警告，将 Build command 改为：

```
git fetch --unshallow && npx quartz build
```

**其他情况：** 如果日志显示依赖安装失败，检查 Build command 是否完整，Framework preset 是否已设为 `None`。

### 13.5 本地 `http://localhost:8080` 打开是空白页

两个常见原因：一是 `content/` 文件夹里没有 `index.md` 文件，Quartz 找不到首页；二是某个 Markdown 文件存在语法错误导致构建异常。先确认 `content/index.md` 存在，再重新运行 `npx quartz build --serve`，观察终端输出是否有报错信息。

### 13.6 网站上图片不显示

确认图片文件存放在 `content/` 目录内（如 `content/images/`），并且 Markdown 中的图片引用路径正确。Obsidian 的 `![[图片.png]]` 语法 Quartz 能识别，但图片文件本身必须在 `content/` 目录内，存放在外部 Obsidian 库里的图片不会被打包进去。

### 13.7 如何更新 Quartz 到最新版本

```bash
git fetch upstream
git merge upstream/v4
npm i
npx quartz sync
```

合并时若出现冲突，按第 12.3 节的说明手动解决。

### 13.8 推送后 Cloudflare 迟迟没有重新部署

登录 Cloudflare Dashboard，在项目页面的「Deployments」选项卡下查看是否有新的构建任务。如果没有，检查 Production branch 是否确实是 `v4`，以及 GitHub 授权是否仍然有效（可以在 Cloudflare 项目设置的「Connected Git repository」处查看）。

### 13.9 Callout 在网站上显示为普通引用块而非标注样式

这种情况通常是因为 `quartz.config.ts` 中没有启用 ObsidianFlavoredMarkdown 插件或其 `callouts` 选项被关闭。检查配置文件中的 `plugins.transformers` 数组，找到 `Plugin.ObsidianFlavoredMarkdown(…)` 条目，确认其中 `callouts: true`（这是默认值，一般无需修改）。如果该插件整体被注释掉，取消注释重新部署即可恢复 Callout 渲染。

---

## 14. 参考资源

- **Quartz 官方文档**（`quartz.jzhao.xyz`）：涵盖所有配置项说明、插件系统、主题定制等完整内容，遇到本文未覆盖的问题首先查阅这里。
- **Quartz GitHub 仓库**（`github.com/jackyzha0/quartz`）：源代码、Issue 追踪和 Release 记录，查看最新版本更新内容和已知问题的最佳入口。
- **Quartz Hosting 官方文档**（`quartz.jzhao.xyz/hosting`）：包含 Cloudflare Pages、GitHub Pages、Vercel、Netlify 等各平台的官方部署指引，本文 Cloudflare 部分所用的构建配置均出自这里。
- **Cloudflare Pages 官方文档**（`developers.cloudflare.com/pages`）：自定义域名配置、构建参数说明、访问限制等高级功能的完整参考。
- **Obsidian Publish 官方页面**（`obsidian.md/publish`）：Obsidian 原生发布服务的完整功能说明与定价，适合在选择方案前做对比参考。
- **Obsidian 社区论坛 Quartz 相关讨论**（`forum.obsidian.md`）：搜索「Quartz」可以找到大量用户在部署和定制过程中的实际经验与踩坑记录，中英文均有。
- **nvm 官方 GitHub**（`github.com/nvm-sh/nvm`）：Node.js 版本管理工具，Linux / macOS 上管理多个 Node 版本的标准方案。
- **Obsidian Git 插件**（Obsidian 社区插件库搜索「Git」，由 Vinzent 开发）：在 Obsidian 内直接完成提交和推送，是最流畅的桌面端免终端日常发布方案。注意移动端功能受限，详见第 9.1 节说明。
- **Quartz Syncer 插件**（Obsidian 社区插件库搜索「Quartz Syncer」，由 saberzero1 开发）：专为 Quartz 设计的发布插件，支持从独立 Obsidian 库向 Quartz GitHub 仓库同步指定文件夹，适合主库与发布内容分离的工作流，桌面端与移动端均可使用。
- **Obsidian 主题移植资源**（`emilebangma.com/Quartz/`）：汇集了社区将多款主流 Obsidian 主题移植到 Quartz 的成果，可以让你的知识网站与 Obsidian 库保持视觉一致性。
