---
tags:
  - Obsidian
  - 同步
  - GitHub
---

# Obsidian 同步方案之 GitHub

同步笔记库这件事，Obsidian 社区有不少成熟的方案。[Remotely Save](Obsidian%20同步方案之%20Remotely%20Save.md) 把文件搬到云端，[Syncthing](Obsidian%20同步方案之%20Syncthing.md) 在设备之间直连对传，各有各的适用场景。GitHub 方案走的是第三条路：用 Git 把整个笔记库变成一个有版本历史的代码仓库，每次同步都是一次提交，每个历史节点都可以回溯。

这带来了其他方案没有的东西。它不只是「把文件同步过去」，而是「记录你对文件做了什么、什么时候做的」。一年前写下的某段文字，删掉了、后来想找，还能找回来。在两台设备上同时改了同一个文件，不是静默覆盖，而是留下可追溯的冲突记录供你处置。如果某次操作把笔记搞乱了，可以直接退回到上一个干净的状态。

当然，代价也是真实存在的：配置比其他方案复杂，上手需要一定的耐心；仓库体积大了之后，初次克隆和推送都会比较慢；移动端的体验受限于技术架构，与桌面端存在明显差距。

本文的电脑端方案以 GitHub Desktop 图形客户端为主线，适合不习惯命令行的用户；移动端则通过社区插件 Git 来实现提交与推送，使用的是 isomorphic-git 这个 JavaScript 版 Git 实现。两套方案共用同一个 GitHub 远程仓库，互为补充。

---

## 目录

1. [方案定位与适用场景](Obsidian%20同步方案之%20GitHub.md#1.%20方案定位与适用场景)
2. [前提条件](Obsidian%20同步方案之%20GitHub.md#2.%20前提条件)
3. [在 GitHub 上创建远程仓库](Obsidian%20同步方案之%20GitHub.md#3.%20在%20GitHub%20上创建远程仓库)
4. [安装 GitHub Desktop 并克隆仓库](Obsidian%20同步方案之%20GitHub.md#4.%20安装%20GitHub%20Desktop%20并克隆仓库)
5. [在 Obsidian 中打开克隆的仓库](Obsidian%20同步方案之%20GitHub.md#5.%20在%20Obsidian%20中打开克隆的仓库)
6. [配置 `.gitignore`：排除高频变动文件](Obsidian%20同步方案之%20GitHub.md#6.%20配置%20`.gitignore`：排除高频变动文件)
7. [桌面端日常工作流：提交与推送](Obsidian%20同步方案之%20GitHub.md#7.%20桌面端日常工作流：提交与推送)
8. [版本回退：如何找回历史状态](Obsidian%20同步方案之%20GitHub.md#8.%20版本回退：如何找回历史状态)
9. [移动端初始准备：将仓库传入手机](Obsidian%20同步方案之%20GitHub.md#9.%20移动端初始准备：将仓库传入手机)
10. [移动端安装与配置 Git 插件](Obsidian%20同步方案之%20GitHub.md#10.%20移动端安装与配置%20Git%20插件)
11. [生成 GitHub Personal Access Token](Obsidian%20同步方案之%20GitHub.md#11.%20生成%20GitHub%20Personal%20Access%20Token)
12. [GitHub Desktop 偏好设置](Obsidian%20同步方案之%20GitHub.md#12.%20GitHub%20Desktop%20偏好设置)
13. [GitHub Desktop 进阶操作](Obsidian%20同步方案之%20GitHub.md#13.%20GitHub%20Desktop%20进阶操作)
14. [Git 插件设置详解](Obsidian%20同步方案之%20GitHub.md#14.%20Git%20插件设置详解)
15. [常见问题与排查](Obsidian%20同步方案之%20GitHub.md#15.%20常见问题与排查)
16. [进阶用法与调整建议](Obsidian%20同步方案之%20GitHub.md#16.%20进阶用法与调整建议)
17. [平台支持与注意事项](Obsidian%20同步方案之%20GitHub.md#17.%20平台支持与注意事项)
18. [附录 1：命令行初始化仓库（Git CLI 方案）](Obsidian%20同步方案之%20GitHub.md#18.%20附录%201：命令行初始化仓库（Git%20CLI%20方案）)
19. [附录 2：搭配 Version History Diff 插件增强历史对比体验](Obsidian%20同步方案之%20GitHub.md#19.%20附录%202：搭配%20Version%20History%20Diff%20插件增强历史对比体验)
20. [参考资源](Obsidian%20同步方案之%20GitHub.md#20.%20参考资源)

---

## 1. 方案定位与适用场景

在进入具体步骤之前，有必要先想清楚：这套方案能解决什么问题，不适合哪些场景。

**核心价值：版本控制与可追溯性。** GitHub 方案最大的区别性优势不是同步本身，而是同步过程中自动建立的历史记录。每次提交都保存了一个快照，注明了时间和改动摘要。这个快照序列意味着你随时可以把笔记库退回到任意历史节点，这是 [Remotely Save](Obsidian%20同步方案之%20Remotely%20Save.md) 或 [Syncthing](Obsidian%20同步方案之%20Syncthing.md) 做不到的事。

**备份层面的价值。** 笔记库推送到 GitHub 之后，即便本地设备损坏或丢失，远端仓库里的内容依然完整。相比于本地磁盘备份，GitHub 提供了一个与设备物理分离的副本。

**不太适合的场景。** 如果你的主要需求是实时的双向同步——在手机上写了一段话，马上在电脑上看到——GitHub 方案不是最顺滑的选择。Git 的同步是有意识的手动或半自动操作，而非透明的后台同步。如果仓库里有大量附件（高分辨率图片、视频、录音），Git 并不适合管理二进制大文件，推送会非常慢，GitHub 的免费仓库也有单文件 100MB 的限制。

**最理想的受众。** 习惯把笔记库当作一个长期项目来维护，希望保留历史、防止误操作的用户；愿意养成「改完就提交」习惯的用户；有时需要在两台电脑之间切换的用户；以及将 GitHub 作为笔记公开展示平台的用户。

> 💡 **说明**
> GitHub 方案和 [Syncthing](Obsidian%20同步方案之%20Syncthing.md)、[Remotely Save](Obsidian%20同步方案之%20Remotely%20Save.md) 并不互斥。一种常见的组合是：用 [Syncthing](Obsidian%20同步方案之%20Syncthing.md) 在设备间做日常快速同步，同时定期用 Git 提交推送到 GitHub，作为长期版本归档与远程备份。两者各司其职，互不干扰。

---

## 2. 前提条件

开始之前，确认以下准备工作已经就绪。

**GitHub 账号。** 访问 [github.com](https://github.com/) 注册账号，免费账号已经足够完成本文的所有操作，包括创建私有仓库。

**GitHub Desktop（桌面端）。** 这是 GitHub 官方出品的图形化 Git 客户端，不需要任何命令行操作就能完成仓库克隆、提交、推送、版本回退等核心操作。下载地址：[desktop.github.com/download](https://desktop.github.com/download/)，支持 Windows 和 macOS。

**移动端（可选）：安装 Obsidian 社区插件 Git。** 如果需要在手机或平板上也进行 Git 操作，需要在移动端 Obsidian 中安装名为「Git」的社区插件。该插件在 Obsidian 官方社区插件市场可以直接搜索到，作者为 Vinzent03。

**Obsidian 版本。** 建议使用 1.0 及以上的正式版本。本文所涉及的操作均不依赖任何特定版本的新特性，常见版本均可正常使用。

**已在 Obsidian 中开启社区插件（移动端需要）。** 进入「设置 → 第三方插件」，确认「限制模式」已关闭，否则无法安装 Git 插件。

> ⚠️ **注意**
> GitHub 免费账号的私有仓库在协作功能上有一定限制，但对个人笔记库的独立使用几乎没有任何影响，无需升级到付费计划。

---

## 3. 在 GitHub 上创建远程仓库

所有的 Git 同步都需要一个远端仓库作为中转站。先在 GitHub 上创建这个仓库，之后所有设备都向它推送和拉取。

**第一步：进入新建仓库界面。** 登录 GitHub，在主页左侧「Top repositories」区域，点击旁边的绿色按钮「New」，进入「Create a new repository」界面。

**第二步：填写仓库名称。** 在「Repository name」字段中，输入仓库的名称。建议使用与你的 Obsidian 仓库名相同或相关的名称，方便日后识别。仓库名称不支持空格，可以用连字符或下划线代替（例如 `my-obsidian-vault`）。

**第三步：设置可见性。** 在「Configuration」下方的「Choose visibility」选项中，默认是「Public」（公开，任何人都可以看到仓库内容）。对于存放个人笔记的仓库，**务必选择「Private」**（私有，只有你自己和你明确授权的人才能访问）。

**第四步：创建仓库。** 点击页面右下角的绿色按钮「Create repository」，完成创建。此时仓库是空的，我们接下来通过 GitHub Desktop 将本地内容推送上去。

> 💡 **说明**
> 新建仓库时，「Add a README file」、「Add .gitignore」、「Choose a license」这些选项可以直接跳过，不需要勾选。我们会在后续步骤中在本地手动处理 `.gitignore`，从空仓库开始更简洁。

---

## 4. 安装 GitHub Desktop 并克隆仓库

仓库创建完成后，需要把它「克隆」到本地——也就是在你的电脑上创建一个与远端仓库相对应的本地副本，以后在这里进行所有的读写操作。

**第一步：下载并安装 GitHub Desktop。** 访问 [desktop.github.com/download](https://desktop.github.com/download/)，下载适合你操作系统的安装包，完成安装并启动程序。

**第二步：登录 GitHub 账号。** 打开 GitHub Desktop，点击左上角菜单「File」，选择「Clone repository」，进入「Clone a repository」界面。如果是初次使用，需要先登录 GitHub 账号。选择左侧的「GitHub.com」标签页，点击右下角蓝色的「Sign in」按钮，在弹出的「Sign in using your browser」界面点击「Continue with browser」。这会打开浏览器，跳转到 GitHub 的授权页面，在页面右下角点击绿色按钮「Authorize desktop」完成授权。授权成功后，浏览器会提示返回 GitHub Desktop。

**第三步：搜索并克隆你的仓库。** 回到 GitHub Desktop 的「Clone a repository」界面，在「Your repositories」区域的搜索框中，输入刚刚在 GitHub 上创建的仓库名称，找到并点击选中。在「Local path」字段中，点击「Choose」按钮，选择一个本地文件夹路径用于存放克隆的仓库。这个路径就是将来 Obsidian 打开的文件夹，建议放在惯常存放文件的位置，路径中不要包含中文或特殊字符。选定路径后，点击右下角蓝色按钮「Clone」。

克隆完成后，本地会出现一个与仓库同名的文件夹，这就是我们将要用作 Obsidian 仓库的地方。

> ⚠️ **注意（特定设置）**
> 本地路径的选择因人而异，以下为示例。
>
> | 项目 | 示例值 | 修改建议 |
> |------|--------|---------|
> | Local path | `D:\Notes\my-obsidian-vault` | 替换为你自己电脑上习惯存放文件的路径 |
>
> 请避免将仓库放在 OneDrive、iCloud 或其他网盘自动同步的文件夹内。多个同步机制同时作用于同一个文件夹，极易产生冲突和损坏 `.git` 数据库。

---

## 5. 在 Obsidian 中打开克隆的仓库

克隆到本地的文件夹，现在需要作为 Obsidian 仓库被打开。

打开 Obsidian，在初始界面点击「打开本地仓库」右侧的「打开」按钮，进入「打开本地仓库」的文件选择界面。找到并选中刚才克隆的文件夹，点击右下角的「选择文件夹」按钮。如果 Obsidian 询问是否信任此仓库的作者，选择「Trust author and enable plugins」（信任作者并启用插件）。

打开后，这个文件夹就成了你的 Obsidian 仓库。在这里新建的所有笔记，都会在后续的 Git 提交中被纳入版本管理。

> 💡 **说明**
> 新打开的仓库看起来是空的（除了 GitHub Desktop 可能添加的一个 `.git` 隐藏文件夹之外），这是正常的。`.git` 文件夹是 Git 的内部数据库，记录所有的版本历史，不要手动删除或移动它。Obsidian 默认不显示以点开头的隐藏文件夹，所以通常你也看不到它。

---

## 6. 配置 `.gitignore`：排除高频变动文件

Obsidian 在运行时会持续更新若干内部状态文件，其中最典型的是 `.obsidian/workspace.json` 和 `.obsidian/workspace-mobile.json`。这两个文件记录的是当前工作区的界面状态（比如打开了哪些文件、分栏如何布局），每次你打开、切换、关闭笔记时都会被修改，与笔记内容本身没有直接关系。

如果不处理这两个文件，每次在 GitHub Desktop 里打开，你都会看到它们出现在变更列表中，每次提交都得处理它们，而且多台设备之间的工作区状态几乎必然产生冲突。解决方式是把它们加入 `.gitignore`，告诉 Git 忽略对这两个文件的追踪。

**第一步：新建 .gitignore 文件。** 在仓库的根目录（也就是克隆的那个文件夹的顶层）中，用任意文本编辑器新建一个文件。文件名必须严格命名为 `.gitignore`，注意：不是 `.gitignore.txt`，也不是 `gitignore`，就是 `.gitignore`（以点开头，没有扩展名）。

> ⚠️ **注意**
> 在 Windows 上，如果你通过文件资源管理器「新建文本文档」再重命名，要确保把 `.txt` 扩展名也一并删掉。如果资源管理器里「隐藏已知文件类型的扩展名」选项是开启的，建议先在文件夹选项中将其关闭，否则容易出现文件实际是 `.gitignore.txt` 但显示是 `.gitignore` 的情况。最稳妥的方式是用代码编辑器（如 VS Code、Notepad++）的「新建文件 → 另存为」功能，手动输入完整文件名 `.gitignore`。

**第二步：写入忽略规则并保存。** 打开新建的 `.gitignore` 文件，写入以下内容并保存：

```
.obsidian/workspace.json
.obsidian/workspace-mobile.json
```

两行分别对应桌面端和移动端的工作区状态文件。保存完毕后，后续 Git 提交就不会再追踪这两个文件的变化了。

> 💡 **说明（按需调整）**
> 以上是步骤记录中的最小化忽略配置，只排除最容易造成冲突的两个文件，`.obsidian` 文件夹下的插件配置、主题等其他文件仍然会被 Git 追踪和同步。如果你有多台电脑、且每台电脑安装了不同的插件，同步 `.obsidian` 文件夹有时会造成混乱；若遇到此类情况，可以考虑在 `.gitignore` 中进一步排除 `.obsidian/` 整个文件夹，在每台设备上各自管理插件配置。这是一个权衡选择，并非必须操作。

> 💡 **补充说明：Git 插件自动生成的冲突文件**
> 安装 Git 插件后，当发生合并冲突且无法自动解决时，插件会在仓库根目录生成一个名为 `conflict-files-obsidian-git.md` 的文件，列出所有冲突的文件路径，提示你手动处理。这个文件是插件的临时辅助文件，处理完冲突后不再需要。如果你不想让它出现在 Git 追踪列表中，可以将其加入 `.gitignore`：
>
> ```
> conflict-files-obsidian-git.md
> ```
>
> 同样地，Git 插件将自身的认证配置（包括 Personal Access Token）存储在 `.obsidian/plugins/obsidian-git/data.json` 中。**强烈建议**将这个文件加入 `.gitignore`，避免 Token 信息随仓库推送到 GitHub，造成安全泄露风险：
>
> ```
> .obsidian/plugins/obsidian-git/data.json
> ```
>
> 因此，一份综合了上述所有实践经验的 `.gitignore` 参考模板如下，可按需取用：
>
> ```
> # Obsidian 工作区状态（设备各异，必须排除）
> .obsidian/workspace.json
> .obsidian/workspace-mobile.json
>
> # Git 插件认证数据（包含 Token，必须排除）
> .obsidian/plugins/obsidian-git/data.json
>
> # Git 插件冲突提示文件（临时文件，可选排除）
> conflict-files-obsidian-git.md
>
> # Obsidian 缓存与软删除回收站（可选）
> .obsidian/cache
> .obsidian/updates.json
> .trash/
>
> # 操作系统生成的元数据（推荐排除）
> .DS_Store
> Thumbs.db
> ```
>
> 其中前两条（工作区状态和 Token 数据）是**强烈建议**添加的，其余为按需选择。

---

## 7. 桌面端日常工作流：提交与推送

完成以上配置后，日常的桌面端使用流程就固定下来了。这个流程的核心动作是：在 Obsidian 里写笔记，然后定期在 GitHub Desktop 里把改动打包成一次提交，推送到 GitHub。

**提交（Commit）：为一组改动创建历史快照。**

打开 GitHub Desktop，点击左侧的「Changes」标签。界面会列出所有自上次提交以来被修改、新增或删除的文件。勾选「changed files」前面的复选框，选中所有你想纳入这次提交的文件（默认是全选）。在左下方「Summary (required)」输入框中填写提交说明——这是描述这次改动内容的简短文字，可以是「更新读书笔记」「新增项目计划」，或者直接填写当前时间也行，总之是让你日后看历史时能快速判断这个节点做了什么。填写完成后，点击蓝色按钮「Commit to main」（具体按钮文字取决于当前分支名，通常是 `main` 或 `master`）。

提交完成后，这些改动就被记录进了本地的 Git 历史，但还没有推送到 GitHub 远端。

**推送（Push）：把本地提交同步到 GitHub。**

提交之后，GitHub Desktop 界面顶部会出现「Push origin」按钮，点击它，本地的提交就会被推送到 GitHub 的远端仓库。此后，在浏览器登录 GitHub、进入对应仓库，就能看到刚才提交的文件和历史记录。

**拉取（Pull）：从 GitHub 获取另一台设备推送的内容。**

如果你有另一台电脑，或者在移动端做了提交并推送到 GitHub，在这台电脑上打开 GitHub Desktop，点击顶部的「Fetch origin」按钮检查远端是否有新内容，如果有，按钮会变为「Pull origin」，点击拉取即可将远端的最新内容同步到本地。

> ✅ **推荐做法**
> 养成「写完就提交，提交就推送」的节奏。不需要每改一行就提交，但写完一篇笔记、完成一个阶段性工作之后，及时提交是好习惯。提交信息写得有意义，日后回溯历史时会省很多力气；实在想不到写什么，填当前时间也好过留空或随便填「update」。

---

## 8. 版本回退：如何找回历史状态

这是 GitHub 方案最有价值的功能之一。当你发现某个笔记被误删、某段内容被意外覆盖，或者某次操作把格式搞乱了，可以通过 GitHub Desktop 的历史记录找回对应版本。

**查看历史记录。** 在 GitHub Desktop 的左侧，切换到「History」标签页，可以看到所有历史提交的列表，每个提交都有时间戳和你当时填写的提交说明。点击某条提交记录，右侧会显示那次提交涉及的所有文件改动，可以逐行对比前后差异。

**针对单个文件的操作。** 在「Changes」或「History」视图中，找到你需要操作的文件，右键点击，弹出菜单中会出现若干操作选项，包括「Discard Changes」（丢弃此文件的当前未提交改动，回到上次提交时的状态）、「Reveal in Finder/Explorer」（在文件管理器中定位文件）等。

> ⚠️ **注意**
> 版本回退涉及的具体操作选项会因 GitHub Desktop 版本和当前视图（Changes 还是 History）有所不同。如需执行特定的回退操作（如将某文件恢复到指定历史版本），可以通过右键菜单探索当前版本提供的选项，或借助 GitHub 网页端的文件历史功能查看和下载历史版本。
>
> 更复杂的版本操作（如 `git revert`、`git reset` 到特定提交）需要使用命令行 Git，超出本文图形化方案的范围，有需要的读者可参考 Git 官方文档。

---

## 9. 移动端初始准备：将仓库传入手机

移动端的 Git 同步需要先在手机上建立一个已经包含 `.git` 文件夹的完整仓库，而不能从零开始在手机上克隆（isomorphic-git 的克隆速度极慢，大型仓库几乎不可用）。最可靠的方式是用数据线将电脑端的仓库直接复制到手机上。

**第一步：用数据线连接手机和电脑，并将仓库文件夹复制到手机。** 通过 USB 数据线将手机连接到电脑，在电脑的文件资源管理器中，找到手机内部存储下的 `Documents` 文件夹（路径通常是「此电脑 → 手机型号 → 内部存储 → Documents」或「我的手机 → Documents」）。将整个 Obsidian 仓库文件夹（就是你在电脑上克隆的那个文件夹）复制到这个位置。

> ⚠️ **注意**
> 复制时必须包含 `.git` 这个隐藏文件夹。`.git` 文件夹存储了 Git 仓库的所有历史记录和元数据，没有它，手机端的 Git 插件无法识别这是一个 Git 仓库，也无法与 GitHub 通信。确认资源管理器的「显示隐藏项目」选项已开启，可以看到 `.git` 文件夹后再复制。
>
> | 项目 | 示例值 | 修改建议 |
> |------|--------|---------|
> | 手机存储目标路径 | `我的手机/Documents/my-obsidian-vault` | 根据你的手机品牌和存储结构替换为实际路径 |
>
> 不同品牌手机通过数据线访问内部存储的方式略有差异，以手机文件管理器中实际看到的路径为准。

> 💡 **补充：iOS 设备的替代传输方式（AirDrop）**
> 对于 iPhone / iPad 用户，如果没有顺手的数据线或不方便使用 Finder 传文件，可以用 **AirDrop** 作为替代方案将仓库压缩包传入设备：
>
> 1. 在 Mac 上，将整个仓库文件夹（含 `.git`）压缩为一个 `.zip` 文件。注意压缩前确认 `.git` 隐藏文件夹已包含在内（macOS 默认会包含隐藏文件）。
> 2. 通过 AirDrop 将 `.zip` 文件发送到 iPhone / iPad，在 iOS 的「文件」App 中接收。
> 3. 在「文件」App 中长按该压缩包，选择「解压缩」，得到解压后的仓库文件夹。
> 4. 将解压后的文件夹移动到 iOS 上 Obsidian 可访问的位置（通常是「在我的 iPhone 上 → Obsidian」目录）。
> 5. 在 Obsidian 中选择「Open folder as vault」，找到并打开该文件夹即可。
> 
> AirDrop 方式的局限是：iOS 文件 App 的解压功能对大文件较慢，且部分情况下可能遗漏隐藏文件夹。如果解压后发现 `.git` 文件夹缺失，建议改用数据线 + Finder 的方式重新传输。

**第二步：在移动端 Obsidian 中打开仓库。** 打开手机上的 Obsidian，选择「Open folder as vault」，在文件选择界面中找到刚才复制过来的仓库文件夹，点击最下方的「使用此文件夹」，在弹出的权限询问中选择「允许」，随后选择「Trust author and enable plugins」。

仓库成功打开后，笔记内容应该与电脑端完全一致。接下来安装 Git 插件，完成后续配置。

---

## 10. 移动端安装与配置 Git 插件

**第一步：打开插件市场。** 在移动端 Obsidian 主界面，点击左上角按钮打开左侧边栏，点击右下角齿轮图标进入设置界面，在「选项」区域点击「第三方插件」，进入插件管理页面，点击「社区插件市场」下方的「浏览」按钮。

**第二步：搜索并安装 Git 插件。** 在搜索框中输入 `Git`，找到名称为「Git」、作者为 Vinzent03 的插件，点击进入详情页，点击「安装」，安装成功后点击「启用」，再点击「选项」按钮进入插件设置界面。

**第三步：配置提交作者信息。** 在 Git 插件的设置界面，找到「Authentication/commit author」区域，需要配置以下三个字段：

- **「Username on your git server」**：填写你的 GitHub 账号用户名（即登录 GitHub 时显示在右上角头像旁边的用户名，也就是你的 GitHub 个人主页地址中 `github.com/` 后面的那一段）。
- **「Author name for commit」**：同样填写 GitHub 用户名。这个字段对应 Git 的 `user.name`，会显示在提交历史里，填与 GitHub 用户名一致的值可以让提交记录与你的 GitHub 账号关联。
- **「Author email for commit」**：填写你 GitHub 账号注册时使用的邮箱地址。这个字段对应 Git 的 `user.email`，GitHub 依赖它来识别提交的归属人。

**第四步：填写 Personal Access Token（下一节获取）。** 「Password/Personal access token」字段暂时留空，待下一节生成 Token 后再回来填入。

---

## 11. 生成 GitHub Personal Access Token

移动端的 Git 插件使用 isomorphic-git 进行身份验证，不支持 SSH 密钥，只支持用户名加 Personal Access Token（个人访问令牌）的方式连接 GitHub。Token 是代替密码使用的一串授权码，需要在 GitHub 网页端生成。

这一步在电脑端的浏览器完成，生成好的 Token 复制到手机上使用。

**第一步：进入开发者设置。** 在电脑浏览器中登录 GitHub，点击右上角的头像，在下拉菜单中点击「Settings」（设置），进入账号设置页面。在左侧导航栏最底部，找到并点击「Developer settings」（开发者设置）。

**第二步：进入 Personal Access Tokens。** 在左侧菜单中，点击展开「Personal access tokens」，点击其下的「Tokens (classic)」选项，进入 Token 管理页面。

**第三步：新建 Token。** 点击「Generate new token」按钮，在弹出的选项中选择「Generate new token (classic)」。

**第四步：配置 Token 信息。** 在「New personal access token (classic)」页面中，进行以下设置：

- **「Note」**：给这个 Token 起一个名字，方便日后识别它的用途，可以填仓库名称或「Obsidian Mobile」之类。
- **「Expiration」**：设置 Token 的有效期。下拉菜单中可以选择 30 天、60 天、90 天等，也可以选择「No expiration」（永不过期）。选择「No expiration」意味着不需要定期更换，使用更方便，但如果 Token 泄露则风险更大；根据个人偏好选择。
- **「Select scopes」**：在权限列表中，勾选「repo」这一项（点击 `repo` 旁边的复选框），它会自动勾选所有子权限，已经覆盖了我们需要的读写仓库权限。其他权限项目无需勾选。

**第五步：生成并保存 Token。** 点击页面最下方的绿色按钮「Generate token」，页面会跳转回 Token 列表，新生成的 Token 以绿色背景高亮显示。点击 Token 旁边的复制按钮将其复制，并立即保存到安全的地方（如密码管理器）。

> ⚠️ **注意**
> Personal Access Token 只在生成后的当前页面完整显示一次，离开页面或刷新后就再也看不到完整内容，只会显示掩码。务必在离开这个页面之前将 Token 复制并妥善保存。如果忘记保存，只能重新生成一个新 Token，旧的将失效。

**第六步：在移动端填入 Token。** 回到手机端 Obsidian 的 Git 插件设置界面，找到「Authentication/commit author」区域中的「Password/Personal access token」输入框，将刚才生成并复制的 Token 粘贴进去。至此，移动端 Git 插件的基本身份验证配置完成。

> 💡 **补充：Fine-grained Personal Access Token（精细化令牌，更安全的选项）**
>
> 上述步骤使用的是 **Classic Token（经典令牌）**，它授予的是 `repo` 全范围权限，可以读写你账号下的所有仓库。这对于个人单用户的笔记场景已经足够，但从安全性角度而言略显宽泛。
>
> GitHub 目前推荐使用 **Fine-grained Personal Access Token（精细化个人访问令牌）** 作为更安全的替代选择。其核心优势在于：
>
> - **仓库范围可限制**：可以只授权访问你的 Obsidian 仓库，其他仓库不受影响。
> - **权限粒度更细**：只需授予「Contents（读写）」和「Metadata（只读）」两个权限，而不是宽泛的 `repo` 全权限。
> - **必须设置有效期**：强制要求设置过期时间（最长 1 年），降低 Token 长期泄露的风险。
> 
> **如何生成 Fine-grained Token：**
>
> 1. 进入「Developer settings → Personal access tokens → Fine-grained tokens」，点击「Generate new token」。
> 2. 在「Token name」填写用途说明，如「Obsidian Vault Mobile」。
> 3. 在「Expiration」设置有效期（建议选 90 天或更长，到期后重新生成）。
> 4. 在「Repository access」选择「Only select repositories」，然后在下拉框中选中你的 Obsidian 仓库名称。
> 5. 在「Permissions」中展开「Repository permissions」，将「Contents」设为 **Read and write**，将「Metadata」设为 **Read-only**（这是最小权限配置，足以支持 Git 的 push/pull 操作）。
> 6. 点击「Generate token」，复制并妥善保存。
> 
> Fine-grained Token 的使用方式与 Classic Token 完全相同，将其粘贴到 Git 插件的「Password/Personal access token」字段即可。
>
> **选择建议：** 如果你对安全性有较高要求，或担心 Token 万一泄露造成的影响，推荐使用 Fine-grained Token；如果只是个人使用、追求简单配置，Classic Token 也没有问题。

---

## 12. GitHub Desktop 偏好设置

GitHub Desktop 的设置界面（macOS 下位于菜单栏「GitHub Desktop → Settings」，Windows 下位于「File → Options」）分为若干功能标签页，每个标签页控制一个方面的行为。以下逐一介绍各标签页的功能，以及与 Obsidian 笔记库管理最密切相关的注意事项。

### 12.1 Accounts（账号管理）

这里显示当前已登录的 GitHub 账号信息，包括头像、用户名和关联邮箱。如果需要同时管理多个 GitHub 账号（例如个人账号和公司账号），也在此处切换登录。

对于 Obsidian 笔记库的个人用户，通常只需登录一个账号即可。如果推送时遇到「Authentication failed」错误，首先到这里确认当前登录的是正确的账号；若不确定，可以先「Sign out」退出，再「Sign in」重新走一遍 OAuth 授权流程。具体排查步骤见第 15.5 节。

### 12.2 Integrations（集成：外部编辑器与终端 Shell）

#### 12.2.1 外部编辑器（External Editor）

这个设置决定当你在 GitHub Desktop 中点击「Open in External Editor」（macOS 快捷键：`Shift+Command+A`；Windows 快捷键：`Ctrl+Shift+A`）时，仓库文件夹会在哪个编辑器中打开。

GitHub Desktop 内置支持的编辑器包括：VS Code、VS Code Insiders、Sublime Text、Atom、JetBrains 系列 IDE（IntelliJ IDEA、WebStorm、PyCharm 等）、Notepad++、Brackets、RStudio、Cursor、JetBrains RustRover 等。从 3.4.4 版本起，还新增了**自定义编辑器**功能：在「External Editor」下拉菜单中选择「Configure Custom Editor」，通过「Choose」按钮指定任意可执行文件路径，并在「Arguments」字段填写启动参数，从而支持任何未被官方列出的编辑器。

**对 Obsidian 用户的实际用途：**

- **批量文件编辑**：需要编辑 `.gitignore`、修改仓库根目录下的配置文件，或者对多个 Markdown 文件进行批量查找替换时，可以一键用 VS Code 打开整个仓库文件夹，操作完毕后回到 GitHub Desktop 提交改动。
- **冲突解决**：遇到合并冲突时（见第 13.5 节），可以从 GitHub Desktop 直接跳转到编辑器，在编辑器中查看并处理冲突标记后返回提交。
- **查看 Markdown 渲染效果**：VS Code 等编辑器对 Markdown 有预览功能，在某些场景下可以补充 Obsidian 的查看视角。

> 💡 **说明**
> 如果你在 GitHub Desktop 已打开的情况下安装了一个新编辑器，需要**完全退出并重新启动 GitHub Desktop**，新安装的编辑器才会出现在下拉菜单中。

#### 12.2.2 终端 Shell

决定点击「Open in Terminal」（macOS：`Shift+Command+T`；Windows：`Ctrl+Shift+\`，路径在菜单栏「Repository → Open in Command Prompt / Git Bash」）时启动哪个终端。

可选的 Shell 包括：系统默认 Terminal、iTerm2（macOS）、PowerShell（Windows）、Git Bash（Windows）、Hyper、Alacritty 等。同样支持从 3.4.4 版本起加入的自定义 Shell 配置。

从 GitHub Desktop 内打开终端的优势在于：终端会**自动 `cd` 进入当前仓库的根目录**，省去手动导航的步骤。当需要执行 `git rm --cached`、`git reflog`、`git stash list` 等 GitHub Desktop 图形界面未提供的命令时（如第 15.2 节所述），用这个入口最为便捷。

### 12.3 Git（Git 全局配置）

这里直接对应系统级别的 `~/.gitconfig` 文件中的 `[user]` 部分，包含两个字段：

**Name（作者姓名）**：对应 `git config --global user.name`。这个名字会出现在每一条提交记录的「Author」字段，在「History」标签页和 GitHub 网页端的提交列表中均可见。

**Email（作者邮箱）**：对应 `git config --global user.email`。GitHub 依赖这个邮箱来识别提交的归属——如果邮箱与 GitHub 账号注册的邮箱匹配，该提交会显示你的头像；如果不匹配，则显示为匿名贡献者。

建议填写与 GitHub 账号关联的用户名和邮箱，保持提交记录的归属准确清晰。

> ⚠️ **注意（隐私邮箱）**
> 如果你的仓库是或将来可能变为公开（Public），个人邮箱地址会出现在所有历史提交记录中，互联网上任何人都能看到。如果有隐私顾虑，可以使用 GitHub 提供的私密邮箱格式：`{用户ID}+{用户名}@users.noreply.github.com`。登录 GitHub 网页端，进入「Settings → Emails」，找到「Keep my email addresses private」区域，可以看到并复制这个格式的地址，将其填入此处的 Email 字段即可。同时建议在 GitHub 网页端勾选「Block command line pushes that expose my email」，防止命令行操作绕过此设置。

> 💡 **说明**
> 如果此处填写的 Name/Email 与 Git 插件的「Author name for commit / Author email for commit」设置不一致，GitHub Desktop 和 Git 插件产生的提交会显示不同的归属信息。建议两处保持一致，避免提交历史出现混乱的作者信息。

### 12.4 Appearance（外观）

控制 GitHub Desktop 的界面主题，可以选择：

- **Light**（浅色主题）
- **Dark**（深色主题）
- **System**（跟随操作系统的深浅色设置自动切换）

此为纯粹的视觉偏好设置，不影响任何功能。如果你的 Obsidian 使用深色主题，将 GitHub Desktop 也切换成深色，视觉上更统一、减少眼睛疲劳。

### 12.5 Prompts（操作确认对话框）

这里可以开启或关闭在执行某些「破坏性操作」前弹出的确认对话框，具体包括：

- **Before force pushing（强制推送前确认）**：强制推送会覆盖远端历史，操作不可逆，建议始终保持开启。
- **Before discarding changes（丢弃本地改动前确认）**：在「Changes」标签页执行「Discard Changes」时弹窗二次确认，防止误操作丢失未提交的笔记内容，建议保持开启。

对于个人笔记库用户，建议两者都保持开启状态，避免因误触导致数据丢失。

### 12.6 Advanced（高级设置）

**Usage data（使用数据统计）**：是否向 GitHub 发送匿名的应用使用统计数据，纯粹的隐私偏好设置，开启或关闭均不影响核心同步功能。

**System dialog for Authentication（系统级认证对话框，Windows 限定）**：启用后，认证操作会调用 Windows 凭据管理器（Windows Credential Manager）进行身份验证，而不是 GitHub Desktop 内置的授权流程。在企业网络环境或使用代理的情况下可能需要调整此选项。如果你的网络环境正常、认证也没有问题，保持默认即可。

### 12.7 Accessibility（无障碍设置）

提供高对比度模式、键盘导航增强等无障碍相关选项，与笔记库操作无直接关联，按个人需求调整即可。

---

## 13. GitHub Desktop 进阶操作

掌握了日常的提交→推送→拉取工作流之后，GitHub Desktop 还提供了一套更细粒度的操作工具，用于应对笔记库管理中的进阶场景。这些功能在 Obsidian 的日常使用中不是每天都会用到，但在需要的时候能提供清晰的操作路径和安全的回退手段——无论是隔离一次大规模的库结构调整、暂存半成品的修改，还是处理两台设备之间产生的内容冲突。

### 13.1 分支管理：用分支隔离不同写作状态

#### 13.1.1 什么时候用分支

Git 的分支功能允许你在不影响主线（`main`）的情况下，开辟一条独立的修改轨道。对于笔记库来说，典型的使用场景包括：

- **长文写作隔离**：开始撰写一篇需要较长周期的文章，写作期间不确定是否会完成，不想把零散草稿和频繁修改混入主线历史。
- **结构性实验**：尝试性地重组文件夹结构、大幅调整分类体系，确认无误后再合并到主线；如果发现不满意，随时可以丢弃实验分支，主线不受影响。
- **尝试新主题或新插件**：想在 Obsidian 中试用一个新主题（修改 `.obsidian/themes/` 配置）或安装一批新插件（改动 `.obsidian/plugins/` 下的文件），但不确定是否满意——可以在独立分支上进行调整和试用，满意后合并回主线，不满意直接丢弃分支，主线的插件和外观配置始终保持稳定。这对于需要在多台设备间同步 `.obsidian` 配置的用户尤其实用：在分支上充分测试后再合并，避免把半成品配置推送到其他设备。
- **公开/私密内容分离**：维护一个「public」分支（已发布的正式内容）和一个「draft」分支（进行中的草稿），分别对应不同公开程度的内容。

#### 13.1.2 创建新分支

在 GitHub Desktop 顶部的「Current Branch」下拉框中，点击展开，点击「New Branch」按钮，输入分支名称（建议使用能描述用途的名称，如 `draft/long-article`、`experiment/folder-reorg`、`public`），点击「Create Branch」。新分支创建后会自动切换到该分支，之后的所有提交都会进入这个新分支，`main` 分支保持不变。

#### 13.1.3 切换分支

点击顶部「Current Branch」下拉框，从列表中选择目标分支名称即可切换。GitHub Desktop 会立即更新工作区文件，Obsidian 中看到的笔记内容也会随之变化——这正是分支的意义所在。

> ⚠️ **注意**
> 切换分支时，如果当前有**未提交的改动**，GitHub Desktop 会弹出选择框，提示你：
>
> - **Bring my changes to [分支名]**：将当前未提交的改动一起带入新分支；
> - **Leave my changes on [当前分支名]**：将未提交改动暂存（Stash）在当前分支，切换到新分支后工作区是干净的。
> 
> 对于笔记库的使用场景，建议**在切换分支前先提交当前的改动**，保持工作区干净，避免不同主题的内容混淆在一次提交里。

#### 13.1.4 合并分支

当某个分支上的工作完成，需要将其内容合并进 `main`，操作步骤如下：

1. 先切换到目标分支（通常是 `main`）；
2. 在顶部菜单栏选择「Branch → Merge into Current Branch」；
3. 从列表中选择来源分支（如 `draft/long-article`），点击「Merge [来源分支] into main」；
4. 如果两个分支有冲突，GitHub Desktop 会提示并引导你解决（见第 13.5 节）；
5. 合并完成后，点击「Push origin」将结果推送到 GitHub。

#### 13.1.5 删除分支

分支合并完毕、确认不再需要后，可以删除它以保持仓库整洁。在「Current Branch」下拉框中找到目标分支，点击右键，选择「Delete」。GitHub Desktop 会询问是否同时删除远端（GitHub 上）的同名分支，通常选择一并删除，避免远端遗留大量废弃分支。

> 💡 **说明**
> 删除分支不会删除分支上已提交的历史记录——合并后，那些提交会成为目标分支历史的一部分。只有未合并的提交在分支删除后才有丢失的风险，GitHub Desktop 会在你试图删除未合并分支时发出警告。

### 13.2 Stash（临时搁置改动）

Stash 功能用于临时保存尚未提交的改动，让工作区恢复到上次提交后的干净状态。典型场景：你正在修改某篇笔记，突然需要切换到另一个分支处理紧急事务，但当前的修改又尚未完成、不到提交的程度——这时用 Stash 把改动暂存起来，处理完紧急事务后再恢复继续。

#### 13.2.1 搁置改动

在「Changes」标签页中，右键点击「changed files」列表的标题区域，选择「Stash All Changes」。所有未提交的改动会被暂时移出工作区，「Changes」标签页变为空，表示工作区已干净。

或者，在切换分支时，在弹出的选择框中选择「Leave my changes on [当前分支]」，GitHub Desktop 也会自动执行 Stash。

#### 13.2.2 恢复搁置的改动

切换回原来的分支后，「Changes」标签页中会出现「Stashed Changes」区域，点击「Restore」即可将之前搁置的改动恢复到工作区，继续之前的工作。

如果不需要这些改动了，也可以点击「Discard」将其彻底丢弃。

> ⚠️ **注意**
> GitHub Desktop 每个分支只支持保存**一组** Stash。如果你在同一分支上做了多次 Stash，只有最新的一组可以通过图形界面恢复；更早的 Stash 记录需要通过命令行 `git stash list` 查看并手动恢复（`git stash apply stash@{n}`）。对于笔记库日常使用，一般不会产生多层 Stash 的需求。

### 13.3 提交历史管理：修改、撤销与整理

GitHub Desktop 提供了一套完整的图形化提交历史管理工具，可以在「History」标签页中对已有提交进行多种操作。

> ⚠️ **重要原则**
> 以下操作会改写本地提交历史。**强烈建议只对尚未推送到 GitHub 的本地提交执行这些操作。** 对已经推送的提交进行历史改写，再次推送时需要强制推送（Force Push），会覆盖远端历史——对于个人笔记库这通常不是灾难性的，但养成「不改写已推送提交」的习惯，是良好的 Git 使用习惯，也为将来可能的协作场景打好基础。

#### 13.3.1 修改最近一次提交（Amend）

刚刚提交完，发现提交信息写错了，或者漏掉了某个文件：在「History」标签页中右键点击最近一条提交，选择「Amend Commit」。GitHub Desktop 会在底部输入框中显示可编辑的提交信息，你可以修改它；如果需要追加文件，回到「Changes」标签页补充勾选后重新提交。

Amend 实际上是**用一个新提交替换最近一次提交**，原提交会从历史中消失。如果该提交已经推送到远端，Amend 后需要强制推送（Force Push）才能更新远端。

#### 13.3.2 撤销最近一次提交（Undo）

提交完之后立刻意识到需要撤销：在「History」标签页中右键点击最近一条提交，选择「Undo Commit」。这会取消提交，但文件的改动会完整保留在工作区（相当于命令行 `git reset --soft HEAD~1`），你可以继续修改后重新提交，不会丢失任何内容。

#### 13.3.3 回退某次提交（Revert）

与「撤销」不同，「回退」（Revert）不删除历史提交，而是**创建一个新的提交**来抵消某次历史提交的效果。原来的历史记录依然保留，只是追加了一条「反向操作」，历史线性地向前延伸。

在「History」标签页中右键点击目标提交，选择「Revert Changes in Commit」，GitHub Desktop 会自动生成一条 revert 提交。这是处理已推送提交时**最安全的方式**，因为不需要强制推送，不会改写已有历史。

对于笔记库场景：如果某次提交误删了一篇重要笔记，用 Revert 可以「撤回」那次删除，把那篇笔记重新添加回来，且不影响其后所有的提交历史。

#### 13.3.4 重新排序提交（Reorder）

在「History」标签页中，可以通过**拖拽**方式改变本地未推送提交的顺序：选中一个或多个提交，拖动到目标位置即可。这在整理提交历史、将相关的多次提交调整到相邻位置时有用；对日常笔记库使用来说，属于可选的整理手段。

#### 13.3.5 合并多次提交（Squash）

如果有多次内容高度相关的提交，想将它们合并成一条更清晰的记录：在「History」标签页中用 `Shift+点击` 选中要合并的那些提交，右键选择「Squash X Commits」，GitHub Desktop 会将它们合并成一次提交，并弹出输入框让你重新填写合并后的提交信息。

同样，建议只对未推送的提交执行 Squash 操作，已推送的提交执行 Squash 后需要强制推送。

### 13.4 Cherry-pick：将某次提交「摘」到另一个分支

Cherry-pick（遴选）允许你把某个分支上的特定一次（或多次）提交「复制」到另一个分支，而不是把整个分支合并过来。

**笔记库的典型场景：** 你有两个分支——`main`（正式内容）和 `draft`（草稿）。在 `draft` 分支上写了若干篇文章，其中某一篇已经成熟，可以加入 `main`，但 `draft` 上还有其他不想合并的草稿——这时可以用 Cherry-pick，只把那篇笔记所在的提交「摘」到 `main`，其余草稿继续保留在 `draft` 分支。

**操作步骤：**

1. 切换到来源分支（如 `draft`），在「History」标签页找到目标提交；
2. 右键点击该提交，选择「Cherry-pick Commit」，在弹出的分支列表中选择目标分支（如 `main`）；
3. 也可以直接将提交从「History」标签页**拖拽到**顶部「Current Branch」下拉框中展开的分支列表，拖到目标分支名称上松开；
4. 如果要 Cherry-pick 多个连续提交，用 `Shift+点击` 选中范围后再拖拽或右键操作；
5. 操作完成后切换到目标分支，确认 Cherry-picked 的提交已出现在历史中，推送到 GitHub。

> 💡 **说明**
> Cherry-pick 是「复制」而非「移动」，来源分支上的提交依然存在。如果 Cherry-pick 的内容与目标分支已有内容产生冲突，GitHub Desktop 会弹出冲突解决界面（见第 13.5 节）。操作完成后，GitHub Desktop 会显示一个成功横幅，其中包含「Undo」按钮，点击即可立即撤销这次 Cherry-pick，恢复到操作前的状态。

### 13.5 冲突解决详解

冲突（Merge Conflict）是 Git 同步中最让人困惑的情况，但理解其成因和解决流程之后，处理起来并不复杂。

#### 13.5.1 冲突的成因

冲突发生在：两台设备（或同一设备的两个分支）各自修改了**同一个文件的同一段内容**，并且都已经提交。Git 无法自动判断应该保留哪个版本，于是将决定权交给你。

#### 13.5.2 GitHub Desktop 的冲突解决流程

当 Pull 或 Merge 操作产生冲突时，GitHub Desktop 会弹出冲突提示窗口，并在「Changes」标签页中将冲突文件标注为特殊状态（显示警告图标 ⚠️）。此时有以下几种处理方式：

**方式一：在外部编辑器中手动解决**

点击冲突提示窗口中的「Open in [your editor]」，在编辑器中打开冲突文件。Git 会在文件内插入冲突标记，格式如下：

```
<<<<<<< HEAD
这是你本地版本的内容
=======
这是远端（或另一分支）的内容
>>>>>>> origin/main
```

手动编辑文件，决定保留哪段内容（可以只保留上方，只保留下方，或将两者合并重写），**然后删除所有冲突标记**（`<<<<<<<`、`=======`、`>>>>>>>` 这三行），保存文件。回到 GitHub Desktop，确认冲突文件的警告图标已消失，填写提交信息（通常写「Resolve merge conflict」），点击「Commit to main」完成这次合并提交。

**方式二：直接选择一方**

对于不需要手动合并内容的情况，在 GitHub Desktop 的冲突文件列表中，点击某个冲突文件旁边下拉框，选择「Use my version」（保留本地版本）或「Use their version」（使用远端版本），直接抛弃另一方，不需要打开编辑器。

**方式三：放弃这次合并**

如果冲突比较复杂，暂时不知道如何处理，可以点击冲突提示窗口中的「Abort Merge」（或 Rebase 流程中的「Abort Rebase」），回到合并之前的干净状态，等有时间时再重新处理。

> 💡 **说明（Obsidian 笔记冲突的特点）**
> 笔记库的冲突主要集中在两类文件：一是 `.obsidian` 文件夹下的配置文件（通过 `.gitignore` 排除 `workspace.json` 等高频变动文件可大幅降低此类冲突）；二是在两台设备上几乎同时修改的同一篇笔记正文。对于后者，由于 Markdown 是纯文本格式，冲突标记清晰可读，手动阅读并保留正确内容并不困难。养成「改完即提交，提交即推送，换设备前先拉取」的节奏，可以将冲突发生概率降到最低。

### 13.6 查看文件差异（Diff View）

在提交之前，「Changes」标签页不只是一个文件列表。点击列表中的任意一个文件，右侧会展开**差异对比视图（Diff View）**，以绿色高亮标注新增内容、红色标注删除内容，可以逐行确认本次改动的范围和内容。

**对 Obsidian 笔记库的实际价值：** 在把一段时间内的笔记整批提交之前，通过 Diff 快速扫描各文件的变化，可以：

- 确认没有误删重要段落；
- 检查是否有不应该追踪的文件意外出现在提交列表中（如缓存文件、临时文件）；
- 在提交之前最后一次审阅自己的修改，保持提交内容的清晰和有意义。

**查看历史差异：** 切换到「History」标签页，点击任意历史提交，同样可以在右侧查看那次提交的详细 Diff，方便核实某次提交到底改动了什么——这在需要找回某段被修改内容时尤为有用。

### 13.7 通过 GitHub Desktop 调出命令行

对于 GitHub Desktop 图形界面未提供的 Git 命令（如第 15.2 节中的 `git rm --cached`、第 13.3 节提到的 `git stash list` 等），可以直接从 GitHub Desktop 打开终端：

- **macOS**：菜单栏「Repository → Open in Terminal」（快捷键 `Shift+Command+T`）
- **Windows**：菜单栏「Repository → Open in Command Prompt」或「Open in Git Bash」（快捷键 `Ctrl+Shift+\`）

打开的终端会自动定位到当前仓库的根目录，省去 `cd` 导航的步骤。完成命令行操作后，回到 GitHub Desktop 即可继续用图形界面提交——两者之间可以灵活切换，互不排斥。

> 💡 **说明**
> 如果你更倾向于完全使用图形化操作，上述命令行入口完全不需要用到。GitHub Desktop 覆盖了日常 Git 工作流中绝大多数操作；只有少数高级场景（如批量删除已追踪文件的追踪状态）才需要临时借用命令行。

---

## 14. Git 插件设置详解

Git 插件的设置项相当丰富，按功能分成了若干区域。以下逐一介绍各设置项的含义与推荐配置，供按需参考。

> 💡 **说明**
> Git 插件的设置界面会因平台不同而有所差异：桌面端（Windows/macOS/Linux）使用 SimpleGit 作为 Git 后端，功能较完整；移动端（Android/iOS）使用 isomorphic-git，部分高级功能不可用，插件本身也将移动端标注为「不稳定/实验性」。以下会在各设置项中注明平台限制。

### 14.1 自动同步与自动提交

**Split timers for automatic commit and sync（拆分提交和同步的计时器）**：关闭时，插件会把「自动提交」和「自动推送/拉取」视为同一个流程，一个计时器统一触发；打开后，两者各自独立计时，可以分别控制提交频率和推送频率。如果你希望「频繁本地提交、不频繁联网推送」，可以打开这个开关。

**Auto commit and sync interval（自动提交并同步的间隔，分钟）**：设置多少分钟自动执行一次「提交 + 同步」，设为 `0` 表示关闭自动。默认值是 `0`。如果你希望插件自动运行，从较长的间隔（如 15 分钟或 30 分钟）开始尝试，避免频繁提交带来的网络压力。

**Auto commit after stopping file edits（停止编辑后自动提交）**：开启后，插件会在你停止编辑文件、等待一个自动间隔之后，才触发自动提交，而不是机械地按固定时间触发。这样的好处是避免你还在连续编辑的过程中被自动提交打断，提交内容更完整。

**Auto commit after latest commit（相对于上次提交计算自动提交时间）**：开启后，自动提交的倒计时从「上次提交的时间」开始算，而不是从插件启动时算。手动提交之后，下次自动提交不会立刻触发，而是等待一个完整的间隔。

**Auto push interval（单独的自动推送间隔）** 和 **Auto pull interval（单独的自动拉取间隔）**：仅在开启「拆分计时器」时可见，分别控制推送和拉取的独立频率。

**Auto commit only staged files（自动提交时只提交已暂存的文件）**：打开后，自动提交只处理你手动 stage 的文件，其余未暂存的文件不会被自动提交。适合希望精细控制每次提交内容的用户，通常不需要改动默认值。

**Commit message on auto commit/sync（自动提交时的提交信息模板）**：可用的占位符包括 `{{date}}`（当前时间）、`{{hostname}}`（设备名）、`{{numFiles}}`（本次提交的文件数）、`{{files}}`（具体文件名列表）。默认值是 `vault backup: {{date}}`，对大多数场景已经够用，可按需修改。

### 14.2 手动提交信息

**Commit message on manual commit（手动提交时的默认提交信息）**：手动执行提交时使用的信息模板，留空则每次手动提交都会弹窗要求临时输入。默认值同样是 `vault backup: {{date}}`。

**`{{date}}` placeholder format（日期占位符的格式）**：控制 `{{date}}` 展开后的显示格式，使用 Moment.js 格式字符串。默认是 `YYYY-MM-DD HH:mm:ss`，通常无需修改。

**`{{hostname}}` placeholder replacement（主机名占位符的自定义值）**：桌面端默认会使用操作系统的主机名；如果你的电脑主机名是一串无意义的字符串，可以在这里填一个自定义名称（如「MacBook」「工作电脑」），让提交历史里的来源设备一目了然。

### 14.3 拉取、合并与启动行为

**Pull on startup（启动时自动拉取）**：开启后，每次 Obsidian 启动时自动执行一次 pull，确保本地内容与远端保持同步。对于多设备用户来说，这个选项非常有用，建议开启。默认是关闭。

**Merge strategy（拉取时的合并策略）**：决定 pull 时如何整合远端提交。`merge` 是最常见的默认选项，适合大多数场景；`rebase` 历史更线性，适合有 Git 经验的用户；`reset` 只更新 HEAD，通常用于搭配其他同步服务的特殊场景，普通用户保持默认 `merge` 即可。

**Merge strategy on conflicts（冲突时的合并倾向）**：`none` 表示 Git 默认行为（产生冲突标记，需手动解决）；`ours` 表示遇到冲突时优先保留本地版本；`theirs` 表示优先保留远端版本。对于单人使用的笔记库，冲突较少见，保持默认 `none` 即可。

**Push on commit-and-sync / Pull on commit-and-sync**：控制「提交并同步」这个一键操作是否包含推送和拉取。如果关掉其中一项，一键操作就只执行另一项。通常保持两者都开启，这样一次操作就能完成完整的同步流程。

### 14.4 视图、状态栏与界面行为

**Pull on startup（启动时拉取）**：建议开启，让每次打开 Obsidian 都自动拉取最新内容。

**Show status bar（显示状态栏）** 和 **Show branch status bar（在状态栏显示分支信息）**：状态栏是 Obsidian 界面底部的信息条，开启后会显示当前分支名、待提交文件数等信息，修改后需要重启 Obsidian 才生效。默认两者均开启，视个人偏好调整。

**Disable informative notifications（关闭提示通知）**：关闭插件在提交、推送等操作完成后弹出的普通通知，减少打扰。如果觉得通知太频繁，可以考虑开启此选项。

**File menu integration（右键菜单集成）**：开启后，在 Obsidian 的文件或文件夹上右键时，菜单中会出现「Stage」「Unstage」「Add to .gitignore」等 Git 相关选项，方便快速操作。默认开启。

### 14.5 Hunk 与行级作者信息（桌面端）

以下功能**仅在桌面端 + SimpleGit 环境**下完整可用，移动端不支持。

**Signs（编辑器侧边栏改动标记）**：在编辑器左侧显示改动了哪些行，并支持按块（hunk）stage 或 reset。默认关闭，喜欢 IDE 式 Git 体验的用户可以打开。

**Show commit authoring information next to each line（行级提交归因）**：在每一行旁边显示这一行是谁在哪次提交里写的，类似 VS Code 的 GitLens 功能。单人笔记场景通常不需要这个功能，默认关闭。

### 14.6 认证（Authentication）

**Username on your git server / Password/Personal access token**：这是移动端（isomorphic-git）专用的认证配置，对应第 10、11 节中配置的内容，桌面端通常不需要在这里配置（GitHub Desktop 或系统级 Git 凭证会处理身份验证）。

**Author name for commit / Author email for commit**：对应 Git 的 `user.name` 和 `user.email`，这两个字段会出现在提交历史中。建议填写与 GitHub 账号关联的用户名和邮箱，保持提交记录的归属准确。

### 14.7 高级设置

**Custom Git binary path（自定义 Git 路径）**：普通用户保持默认的 `git`（使用系统 PATH 中的 Git）即可。只有当电脑上有多个 Git 安装版本、需要指定特定版本时才需要修改。

**Disable on this device（仅在本设备禁用插件）**：仅对当前设备生效，且这个设置不会通过 Git 同步到其他设备。适合某台设备临时不想让 Git 插件在后台运行的情况，不影响其他设备。

### 14.8 推荐配置参考

如果你是多设备同步的普通用户，以下是一份简洁的起点配置建议：

| 设置项 | 建议值 | 说明 |
|--------|--------|------|
| Auto commit and sync interval | 15 或 30 分钟 | 按使用节奏调整，0 表示不自动 |
| Auto commit after stopping file edits | 开启 | 避免编辑中途被自动提交 |
| Pull on startup | 开启 | 每次打开先拉取最新内容 |
| Commit message on auto commit/sync | `vault backup: {{date}}` | 默认值即可，按需修改 |
| `{{hostname}}` 自定义 | 填设备名 | 如「iPhone」「工作电脑」，方便区分来源 |
| Show status bar | 开启 | 随时看到同步状态 |

---

## 15. 常见问题与排查

### 15.1 .gitignore 文件无法创建或命名

这是 Windows 用户最常遇到的障碍。Windows 文件资源管理器不允许直接创建以点开头、没有扩展名的文件。解决方式：使用文本编辑器（如 VS Code、Notepad++、甚至系统自带的记事本）通过「文件 → 另存为」，手动输入完整文件名 `.gitignore`（包含前面的点），保存类型选择「所有文件」而非「文本文档」，保存到仓库根目录即可。

### 15.2 已经提交了 workspace.json，现在如何排除

如果在创建 `.gitignore` 之前就已经提交了 `workspace.json`，仅仅把它加入 `.gitignore` 是不够的——Git 已经在追踪它，忽略规则不会自动停止追踪已有文件。需要先告诉 Git「从追踪列表中移除这个文件，但保留本地文件」：

```
git rm --cached .obsidian/workspace.json
git rm --cached .obsidian/workspace-mobile.json
```

在 GitHub Desktop 中没有对应的图形操作，这两行需要在命令行中执行（在仓库根目录打开终端）。执行完后，再在 GitHub Desktop 里提交这次改动，`.gitignore` 规则就会完整生效。

### 15.3 移动端推送失败：认证错误

最常见的原因是 Token 填写有误或已过期。逐一检查：Token 是否完整复制（没有首尾空格）；如果 Token 设置了有效期，是否已经超出；GitHub 账号密码是否被更改（密码更改会让所有已有 Token 失效）。Token 失效后，按照第 11 节重新生成一个新 Token，填入「Password/Personal access token」字段即可。

### 15.4 移动端 Git 操作极慢

这是 isomorphic-git 的已知限制，与仓库中文件的数量高度相关。isomorphic-git 在检查工作目录变化时需要逐一扫描文件，仓库文件越多，速度越慢。如果等待时间超过几分钟，仓库体积可能已经超出了移动端的舒适范围。在插件设置中打开「Auto commit only staged files」，手动 stage 你这次修改的少数文件后再提交，比全量扫描快得多。对于非常大的仓库，移动端 Git 的体验可能难以接受，这是技术层面的限制，并非配置问题。

### 15.5 GitHub Desktop 提示「Authentication failed」

通常是 GitHub 账号的授权令牌失效。在 GitHub Desktop 中，点击顶部菜单「File → Options」（macOS 是「GitHub Desktop → Preferences」），在「Accounts」标签页中先「Sign out」退出，再重新「Sign in」登录一次 GitHub 账号，重新走一遍 OAuth 授权流程即可。

### 15.6 两台电脑之间产生合并冲突

当两台电脑各自在离线状态下修改了同一个文件，且都提交了，再尝试推送/拉取时就会产生冲突。GitHub Desktop 会提示冲突并要求解决，可以选择「Accept Current Change」（保留本地版本）、「Accept Incoming Change」（保留远端版本）或手动合并两个版本的内容。解决冲突后再做一次提交，才能完成这轮同步。

> 💡 **说明**
> 对于笔记库来说，大多数冲突都发生在 `.obsidian` 文件夹下的配置文件，而非笔记内容本身。如果配置文件频繁冲突，可以考虑将整个 `.obsidian` 文件夹加入 `.gitignore`，各设备独立管理配置，只同步笔记内容。

### 15.7 移动端仓库与电脑端不一致

如果通过数据线复制仓库的时间距今已久，手机上的内容可能落后于电脑。在移动端 Git 插件设置好 Token 之后，手动触发一次「Pull」操作，将远端 GitHub 的最新内容拉取到手机端，使两端重新对齐。在移动端 Obsidian 中，可以通过命令面板（点击左侧边栏的命令图标，或下拉 Obsidian 界面）搜索「Git: Pull」执行拉取操作。

---

## 16. 进阶用法与调整建议

### 16.1 精细化 .gitignore 配置

本文步骤记录中的 `.gitignore` 只排除了两个最容易造成冲突的文件。根据你的实际情况，可以进一步扩展。以下是常见的补充规则：

```
# 不追踪 Obsidian 的缓存和更新检查文件
.obsidian/cache
.obsidian/updates.json

# 不追踪 Obsidian 的软删除回收站
.trash/

# macOS 系统生成的元数据文件
.DS_Store

# Windows 系统缩略图缓存
Thumbs.db
```

> 💡 **说明（特定设置）**
> 以上是可选的扩展配置，并非步骤记录中的必须操作。这些规则都是社区实践中常见的推荐做法，可以根据你的使用习惯选择性添加。如果你希望在多台电脑之间同步 Obsidian 插件配置，不要把整个 `.obsidian` 文件夹加入忽略；如果插件配置在不同电脑间差异较大，才考虑用更激进的策略排除整个 `.obsidian`。

### 16.2 利用 GitHub 网页端查看笔记历史

GitHub 网页端提供了对每个文件完整历史记录的可视化展示。在仓库页面，点击任意一个 `.md` 笔记文件，再点击文件右上角的「History」（历史），可以看到这个文件历次被修改的提交列表，点击某次提交可以看到具体的修改内容（绿色为新增，红色为删除）。如果需要找回某个历史版本，点击「Raw」查看或复制历史内容。

### 16.3 GitHub 仓库作为笔记公开展示平台

如果你愿意公开部分笔记内容，可以将仓库可见性设为「Public」。GitHub 会自动渲染 Markdown 文件，直接在网页上呈现格式化的笔记内容，形成一个简单的在线知识库。对于想要展示读书笔记、技术文章或研究整理的用户，这是免费的发布渠道。

> ⚠️ **注意**
> 将仓库改为 Public 后，互联网上任何人都能访问你的所有笔记内容，包括历史提交记录。改为公开前，建议仔细检查是否有不希望公开的内容，包括隐私信息、草稿等。也可以建立两个仓库：一个私有仓库存放全部笔记，另一个公开仓库只存放愿意公开的部分，分别管理。

### 16.4 在桌面端使用 Git 插件实现自动化

本文桌面端的步骤是以 GitHub Desktop 为主，但同样可以在桌面端 Obsidian 中安装 Git 插件，实现自动定时提交和推送，不需要每次手动打开 GitHub Desktop 操作。桌面端的 Git 插件使用 SimpleGit 后端，功能比移动端更完整，也更稳定。安装步骤与移动端相同，桌面端的认证通常由操作系统的 Git 凭证管理器处理，不需要手动填写 Token。

桌面端 Git 插件与 GitHub Desktop 可以并用，也可以二选一，取决于你更喜欢图形界面还是自动化。

### 16.5 与 Remotely Save 或 Syncthing 结合使用

GitHub 方案和其他同步方案并不互斥。一种常见的组合是：用 [Syncthing](Obsidian%20同步方案之%20Syncthing.md) 在设备间做日常快速同步（实时、无需手动操作），同时定期用 Git 提交推送到 GitHub，作为长期版本历史存档与异地备份。两者各司其职：[Syncthing](Obsidian%20同步方案之%20Syncthing.md) 解决即时同步，GitHub 解决版本追溯。只需注意在 `.stignore`（[Syncthing](Obsidian%20同步方案之%20Syncthing.md) 的忽略文件）中排除 `.git` 文件夹，避免 [Syncthing](Obsidian%20同步方案之%20Syncthing.md) 把 Git 的内部数据库也同步来同步去，造成不必要的数据传输和潜在损坏。

---

## 17. 平台支持与注意事项

**桌面端（Windows / macOS）。** GitHub Desktop 官方支持 Windows 和 macOS，整体体验稳定。Git 插件在桌面端使用系统安装的 Git，功能最完整，也最稳定，所有 Git 操作与标准 Git 行为一致。

**Android 移动端。** Git 插件在 Android 上可用，使用 isomorphic-git 实现，插件本身将其标注为「不稳定/实验性」。主要限制在于性能——仓库文件越多，各种操作越慢，大型仓库在移动端的实用性有限。基础操作（pull、commit、push）通常可以正常完成，适合仓库体积中等、操作频率不高的场景。

**iOS 移动端。** Git 插件同样可在 iOS 上运行，同样使用 isomorphic-git，受到相同的性能限制。iOS 的沙盒机制对文件访问有额外限制，如果遇到无法访问仓库路径的问题，需要确认 Obsidian 仓库存放在 Obsidian 可访问的目录下。

> 💡 **iOS 替代方案：Working Copy**
> 对于对移动端 Git 体验要求更高的 iOS 用户，**Working Copy**（`workingcopy.app`）是社区中广受认可的第三方 Git 客户端，提供原生 iOS 体验，性能和稳定性均优于 isomorphic-git 方案。Working Copy 本身为收费应用（推送功能需购买 Pro 版），可以通过 iOS 的「文件」App 将克隆的仓库文件夹提供给 Obsidian 使用。具体配置步骤涉及 iOS 文件共享机制，可参考 Obsidian 论坛的 Working Copy 专题讨论（见参考资源）。

> ⚠️ **注意**
> 官方插件页面明确提示：移动端的 Git 实现存在已知问题且修复难度较大，如果你的仓库很大（包含大量文件），移动端 Git 插件可能无法满足你的需求。这不是配置问题，是底层技术限制。如果对移动端同步的稳定性有更高要求，[Syncthing](Obsidian%20同步方案之%20Syncthing.md) 或 [Remotely Save](Obsidian%20同步方案之%20Remotely%20Save.md) 在移动端的表现会更可靠。

**GitHub 存储限制。** GitHub 的免费私有仓库没有明确的文件数量限制，但单文件大小不能超过 100 MB，整个仓库建议不超过 1 GB（超过后 GitHub 会发出警告，超过 5 GB 会影响正常使用）。对于主要存放 Markdown 文本和少量图片的笔记库，几乎不会触及这些上限，但如果笔记库包含大量 PDF、音频或视频文件，需要留意。

---

## 18. 附录 1：命令行初始化仓库（Git CLI 方案）

> 💡 **说明**
> 本附录是对主流程（GitHub Desktop 图形化方案）的**补充路径**，面向已经熟悉 Git 命令行、或者使用 Linux 桌面系统（GitHub Desktop 不支持 Linux）的用户。如果你使用 GitHub Desktop 已经完成了第 3–5 节的操作，可以直接跳过本附录。

对于习惯命令行操作的用户，不依赖 GitHub Desktop、全程使用 Git CLI 完成仓库初始化和同步的完整流程如下。

### 18.1 前提

电脑上已安装 Git（可在终端运行 `git --version` 确认）。如果尚未安装：

- **macOS**：运行 `xcode-select --install` 安装 Xcode Command Line Tools（含 Git），或通过 Homebrew：`brew install git`。
- **Windows**：下载安装 [git-scm.com](https://git-scm.com/download/win)，安装后通过 Git Bash 执行以下命令。
- **Linux**：通过包管理器安装，如 `sudo apt install git`（Debian/Ubuntu）或 `sudo dnf install git`（Fedora）。

### 18.2 第一次配置 Git 身份

```bash
git config --global user.name "你的 GitHub 用户名"
git config --global user.email "你的 GitHub 注册邮箱"
```

### 18.3 方式一：从空 GitHub 仓库开始（推荐）

适合还没有本地笔记库、或准备把现有笔记迁移进来的情况。

**1. 克隆远端仓库到本地：**

```bash
git clone https://github.com/你的用户名/仓库名.git
```

执行后，当前目录下会出现一个与仓库同名的文件夹，进入它：

```bash
cd 仓库名
```

**2. 将现有笔记内容复制进来（如有）：**

把你已有的 Obsidian 笔记文件夹内的所有内容复制到这个目录中。注意不要把旧目录的 `.git` 文件夹（如果有的话）覆盖进来，只复制笔记内容文件即可。

**3. 创建 `.gitignore` 并写入忽略规则：**

```bash
# macOS / Linux
cat > .gitignore << 'EOF'
.obsidian/workspace.json
.obsidian/workspace-mobile.json
.obsidian/plugins/obsidian-git/data.json
conflict-files-obsidian-git.md
.DS_Store
Thumbs.db
EOF

# Windows（Git Bash 中同样适用上面的命令）
```

**4. 暂存、提交并推送：**

```bash
git add .
git commit -m "Initial vault setup"
git branch -M main
git push -u origin main
```

推送时会要求身份验证，输入 GitHub 用户名，密码处填入第 11 节生成的 Personal Access Token（不是 GitHub 账号密码）。

### 18.4 方式二：将现有本地文件夹初始化为 Git 仓库

适合已有本地笔记库，想将其纳入 Git 管理的情况。

```bash
# 进入你的 Obsidian 仓库所在文件夹
cd /path/to/your/obsidian-vault

# 初始化 Git 仓库
git init

# 关联远端 GitHub 仓库
git remote add origin https://github.com/你的用户名/仓库名.git

# 创建 .gitignore（内容同上）
# ... 写入忽略规则 ...

# 首次提交并推送
git add .
git commit -m "Initial vault setup"
git branch -M main
git push -u origin main
```

### 18.5 日常命令行工作流

初始化完成后，日常的提交和同步操作只需要三个命令：

```bash
git add .                          # 暂存所有变动（或替换 . 为具体文件名）
git commit -m "这次提交的说明"      # 创建提交
git push                           # 推送到 GitHub
```

从另一台设备拉取：

```bash
git pull                           # 拉取远端最新内容并合并
```

> 💡 **说明**
> 命令行方案与 GitHub Desktop 方案并不冲突。即便主要使用 GitHub Desktop，偶尔也可以在终端执行 `git pull` 或 `git rm --cached` 等图形界面未涵盖的操作，两者共用同一个仓库，互不干扰。

---

## 19. 附录 2：搭配 Version History Diff 插件增强历史对比体验

> 💡 **说明**
> 本附录介绍一个可选的搭配插件，用于增强 Git 版本历史的阅读体验。它不是同步功能的必要组成部分，适合希望在 Obsidian 内部直接对比版本差异、而不必切换到 GitHub Desktop 或 GitHub 网页端的用户。

### 19.1 插件简介

**Version History Diff**（作者 kometenstaub，社区插件市场可直接搜索安装）是 Git 插件官方 README 中明确推荐的搭配插件。它的核心功能是：在 Obsidian 内部，以**渲染后的 Markdown 格式**（而不是原始文本 diff）直观呈现两个历史版本之间的内容差异，新增内容以绿色高亮，删除内容以红色高亮，对比效果更接近 Word 的修订模式，比 Git 插件自带的 raw diff 视图更易读。

### 19.2 安装方式

在 Obsidian 的「设置 → 第三方插件 → 社区插件市场」中，搜索「Version History Diff」，找到对应插件后点击安装并启用。

### 19.3 使用方式

安装并启用后，在 Git 插件的「History View」（历史视图）中，点击某个提交记录，再点击具体文件，界面中会出现调用 Version History Diff 的选项，点击即可以 Markdown 渲染格式查看该文件在这次提交前后的内容对比。

相比于直接在 GitHub Desktop 或 GitHub 网页端查看 diff，这种方式的优势在于：

- 无需离开 Obsidian 界面；
- Markdown 的格式化内容（标题、加粗、列表等）以渲染形式呈现，而不是显示原始符号，更容易判断内容的实际变化；
- 对于长篇笔记的局部修改，渲染格式下的对比定位更直观。

> ⚠️ **注意**
> Version History Diff 依赖 Git 插件的历史记录功能，需要 Git 插件已安装并正常运行。该插件主要在桌面端使用，移动端的兼容性取决于 isomorphic-git 对历史记录的支持程度。

---

## 20. 参考资源

- **obsidian-git 插件 GitHub 仓库**（`github.com/Vinzent03/obsidian-git`）：插件源码、README、已知问题 Issue 追踪，移动端限制说明也在此处。
- **obsidian-git 官方文档**（`publish.obsidian.md/git-doc`）：插件官方发布的完整文档站点，包含所有设置项的详细说明、移动端配置指南、SSH 认证指南及常见问题解答，是最权威的参考来源。
- **GitHub Desktop 官方文档**（`docs.github.com/en/desktop`）：GitHub Desktop 的完整操作说明，包含克隆、提交、推送、冲突解决等内容的图文教程。
- **GitHub 官方文档：Personal Access Tokens**（`docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens`）：Token 的创建、权限配置和安全管理说明，包含 Classic Token 与 Fine-grained Token 的对比和最佳实践建议。
- **Obsidian 论坛 Git 相关讨论**（`forum.obsidian.md`，搜索「obsidian git」）：大量社区用户的真实配置经验，常见问题的解决方案和不同方案的对比讨论。
- **iOS + Working Copy 配置教程**（`forum.obsidian.md`，搜索「Working Copy iOS」）：Obsidian 论坛中关于在 iOS 上使用 Working Copy 配合 Obsidian 实现 Git 同步的社区讨论和教程汇总。
- **isomorphic-git 项目页**（`isomorphic-git.org`）：移动端 Git 插件底层实现的官方文档，了解其设计原理和已知限制。
- **Git 官方文档**（`git-scm.com/doc`）：Git 命令行的权威参考，附录 A 所涉及的 `git init`、`git add`、`git commit`、`git push`、`git pull`、`git rm` 等命令均可在此查阅完整说明。
