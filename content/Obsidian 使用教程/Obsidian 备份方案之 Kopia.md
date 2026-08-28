---
tags:
  - Obsidian
  - 备份
  - Kopia
  - WebDAV
---

# Obsidian 备份方案之 Kopia

Obsidian 的笔记库本质上是一组普通文件，因此除了同步之外，**备份同样是长期使用过程中必须考虑的问题**。同步解决的是「不同设备之间保持一致」，而备份解决的是「即使当前文件被删除、覆盖或损坏，也能够找回过去的版本」。尤其是当开始借助 AI 相关工具对笔记库进行自动化管理和批量修改时，备份的重要性更加凸显——AI 工具的介入增加了自动化操作的不确定性，一次误操作可能波及大量文件，而一份可靠的历史备份，正是应对这类情况最为直接的兜底手段。

本文以 Windows 下的 KopiaUI 图形界面为主线，详细介绍两种 Obsidian 备份方式：

- **备份到本地 Repository**：将快照保存到本地磁盘，适合作为高频、快速的本地备份。
- **备份到 InfiniCLOUD WebDAV Repository**：将 Kopia 加密后的 Repository 保存到云端，作为本地备份之外的远程备份。

同时说明 KopiaUI 的快照保留策略、自动备份、开机启动、文件恢复以及常见问题。

## 目录

1. [方案定位与适用场景](Obsidian%20备份方案之%20Kopia.md#1.%20方案定位与适用场景)
2. [Kopia 的核心概念](Obsidian%20备份方案之%20Kopia.md#2.%20Kopia%20的核心概念)
3. [前提条件](Obsidian%20备份方案之%20Kopia.md#3.%20前提条件)
4. [安装 KopiaUI](Obsidian%20备份方案之%20Kopia.md#4.%20安装%20KopiaUI)
5. [KopiaUI 基础界面与仓库切换](Obsidian%20备份方案之%20Kopia.md#5.%20KopiaUI%20基础界面与仓库切换)
6. [设置 Kopia 开机启动](Obsidian%20备份方案之%20Kopia.md#6.%20设置%20Kopia%20开机启动)
7. [备份 Obsidian 到本地 Repository](Obsidian%20备份方案之%20Kopia.md#7.%20备份%20Obsidian%20到本地%20Repository)
8. [从本地快照恢复 Obsidian 文件](Obsidian%20备份方案之%20Kopia.md#8.%20从本地快照恢复%20Obsidian%20文件)
9. [备份 Obsidian 到 WebDAV 云端 Repository](Obsidian%20备份方案之%20Kopia.md#9.%20备份%20Obsidian%20到%20WebDAV%20云端%20Repository)
10. [从云端快照恢复文件](Obsidian%20备份方案之%20Kopia.md#10.%20从云端快照恢复文件)
11. [本地备份与云端备份如何组合](Obsidian%20备份方案之%20Kopia.md#11.%20本地备份与云端备份如何组合)
12. [常见问题与排查](Obsidian%20备份方案之%20Kopia.md#12.%20常见问题与排查)
13. [进阶用法与调整建议](Obsidian%20备份方案之%20Kopia.md#13.%20进阶用法与调整建议)
14. [参考资源](Obsidian%20备份方案之%20Kopia.md#14.%20参考资源)

## 1. 方案定位与适用场景

在开始具体操作之前，需要先回答一个问题：**为什么在已经使用 Syncthing 或 GitHub 的情况下，还需要 Kopia？**

对于 Obsidian 而言，**Syncthing、GitHub 与 Kopia 的定位并不相同**，它们各自解决不同层面的问题，理解彼此的边界，才能在不同场景下做出合适的选择。

**Syncthing** 解决的是「不同设备之间即时同步」。它通过设备间直连的 P2P 方式，让文件变动实时或近乎实时地在多台设备之间保持一致。但 Syncthing 是同步工具而非备份工具——它的设计目标是「让多台设备上的文件保持一致」，而非「保存文件的历史版本」。虽然 Syncthing 提供了「文件版本控制」功能，可以在文件被删除或被新版本覆盖时归档旧版本，但仅能应对「文件被删除或覆盖」的场景。对于**普通的内容修改——比如在笔记中改了一个段落、删了一句话——Syncthing 不会保存修改前的版本**，修改后的内容会直接覆盖同步到所有设备，无法回溯。此外，Syncthing 本身不提供云端存储，所有数据仅在设备间流转，如果所有设备同时损坏，数据将无法恢复。

**GitHub 方案** 将笔记库转换为 Git 仓库，每次提交都是一个历史节点，每个版本都可回溯。它兼顾了同步与远程版本保存——每一次 `git commit` 都是一次有记录的快照，可以回到任意历史节点。GitHub 在传输层使用 TLS 加密，在服务器端对数据进行 AES-256 加密存储。但需要留意的是，**加密密钥由 GitHub 托管**，而非用户自己持有。这意味着从「数据主权」的角度看，笔记内容对服务商是理论可见的。此外，GitHub 服务器位于境外，国内部分地区访问速度可能不稳定；通过 `obsidian-git` 插件实现的自动提交也依赖 Obsidian 处于运行状态，无法作为独立的系统级备份服务。

**Kopia** 则定位为**独立的自动化备份层**。它是一个快速、安全的开源备份与恢复工具，支持 Windows、macOS 和 Linux 全平台。Kopia 对指定文件夹创建快照（Snapshot），快照采用**增量式**存储机制——只有被修改过的文件才会重新上传，未变动的文件直接从仓库中复用，因此后续备份速度极快。Kopia 支持将加密后的快照保存到多种存储位置，包括本地文件系统、NAS 网络存储，以及 WebDAV、S3、SFTP 等云端或远程存储。部署于本地磁盘的仓库，可完全不受网络环境影响，不存在访问速度不稳定的问题；部署于云端的仓库，则可提供异地冗余，在本地设备损毁等极端情况下仍可恢复数据。通过调度策略（Policy）配置，Kopia 支持最短每 10 分钟执行一次自动快照，且作为独立于 Obsidian 运行的系统级服务，不依赖 Obsidian 是否打开，可靠性更高。在安全性方面，**所有 Kopia 仓库默认启用加密**，无论备份目的地是本地磁盘还是云端。加密在**本地完成**——数据在写入仓库之前即被加密，Kopia 采用端到端加密（**End-to-End Encryption**）设计。仓库密码由用户设定，且**不会发送到任何服务器**，仅在本地用于派生和加解密主密钥。Kopia 默认使用 AES256-GCM-HMAC-SHA256 加密算法，也支持选择 CHACHA20-POLY1305-HMAC-SHA256。这意味着即使云端存储服务商的数据泄露，攻击者看到的也只是密文，无法读取任何笔记内容；即便备份在本地磁盘，未经密码解密同样无法访问。

三者分工明确，各司其职：

```
Syncthing
→ 主要解决「不同设备之间即时同步」

GitHub
→ 主要解决「同步 + 以日、月、年为基础的远程版本保存」

Kopia
→ 主要解决「以分钟、小时为基础的本地和云端自动化历史快照与独立备份」
```

厘清三者的定位之后不难看出，Kopia 并不是 Syncthing 或 GitHub 的替代品，而是 Obsidian 整体数据保护方案中的**独立备份层**。通过本地 Repository，可以建立不依赖互联网的高频备份；通过云端 Repository，则可以进一步增加一份远程的加密历史备份。两者可以并存：本地备份应对日常的快速恢复需求，云端备份应对设备损毁等极端情况，互为补充。

## 2. Kopia 的核心概念

理解 Kopia，最重要的是先区分三个概念：

```text
Repository（仓库）
Snapshot（快照）
Policy（策略）
```

### 2.1 Repository（仓库）

Repository 是**保存 Kopia 备份数据的存储位置**。

逻辑关系：

```text
Obsidian Vault
      ↓
   Snapshot
      ↓
 Repository
```

Repository 决定备份数据最终存放在哪里。例如本地 Repository 可以放在：

```text
D:\Backup\Kopia
```

也可以使用 WebDAV 服务器作为远程 Repository。

Kopia 官方支持本地目录、NAS、WebDAV、S3、SFTP 等多种存储类型。

> ⚠️ **注意（特定设置）**
>
> `D:\Backup\Kopia` 是本文的示例路径，并非 Kopia 的默认路径。你可以替换为自己电脑上的任意专用备份目录。
>
> | 项目 | 示例特定值 | 通用写法/替换建议 |
> |------|-----------|------------------|
> | 本地 Repository 路径 | `D:\Backup\Kopia` | 替换为自己的专用备份目录 |
> | 备份磁盘 | `D:` | 替换为自己的备份磁盘盘符 |
> | 目录结构 | `Backup\Kopia` | 按自己的目录习惯命名，保持独立即可 |

### 2.2 Snapshot（快照）

Snapshot 是**某一个时间点的备份快照**。

例如 `2026-08-19 10:00` 的快照代表了该时间点 Vault 中所有文件的完整状态。如果之后某个文件被删除或修改，就可以寻找删除发生之前的 Snapshot 进行恢复。

Kopia 并不是每次都将整个 Vault 完整复制一遍。后续 Snapshot 会复用 Repository 中已经存在的数据，只有发生变化的部分才会新增存储，因此重复内容不会被无意义地重复保存。

### 2.3 Policy（策略）

Policy 是**决定 Snapshot 如何创建和保留的策略**。它可以控制：

- 多久创建一次 Snapshot（Snapshot Frequency）。
- 保留多少个最新 Snapshot（Latest Snapshots）。
- 每小时、每天、每周、每月、每年分别保留多少个 Snapshot。
- 是否保存完全相同的 Snapshot（Ignore Identical Snapshots）。
- 哪些文件需要忽略。
- 是否启用压缩。

可以简单理解为：

```text
Repository = 备份存在哪里
Snapshot   = 某一个时间点保存了什么
Policy     = 什么时候备份、保留多久、忽略什么
```

## 3. 前提条件

### 3.1 操作系统与 KopiaUI

本文主要针对：

- Windows 10+ 64 位系统
- KopiaUI 图形界面

Kopia 官方提供 Windows GUI 安装包，macOS 和 Linux 用户也可使用 KopiaUI，操作逻辑基本一致，仅在文件路径格式和系统托盘入口位置有所不同。

### 3.2 Obsidian Vault

准备一个正常使用的 Obsidian Vault。本文使用公开教程示例：

```text
C:\Obsidian\MyVault
```

> ⚠️ **注意（特定设置）**
>
> `C:\Obsidian\MyVault` 仅为本文示例，并非 Obsidian 或 Kopia 的固定路径。
>
> | 项目 | 示例特定值 | 通用写法/替换建议 |
> |------|-----------|------------------|
> | Obsidian Vault 路径 | `C:\Obsidian\MyVault` | 替换为自己的 Vault 根目录 |
> | 备份源 | 整个 `MyVault` 文件夹 | 选择需要完整备份的 Vault |
>
> **建议选择 Vault 根目录**，以便在需要时能够恢复整个 Vault。

### 3.3 本地备份位置（使用本地 Repository 时）

如果使用本地 Repository，建议准备一个专门的备份目录：

```text
D:\Backup\Kopia
```

> ⚠️ **注意（特定设置）**
>
> | 项目 | 示例特定值 | 通用写法/替换建议 |
> |------|-----------|------------------|
> | Repository 路径 | `D:\Backup\Kopia` | 替换为自己的专用备份目录 |
> | 推荐做法 | 使用独立目录如 `Backup\Kopia` | 保持 Repository 与普通文件分离 |
> | 不推荐 | 直接使用 `D:\` 根目录 | 不要把整个磁盘根目录作为 Repository |

### 3.4 WebDAV 云端存储（使用云端 Repository 时）

如果希望增加远程备份，需要准备一个支持 WebDAV 协议的云存储服务。

本文以 InfiniCLOUD 为例。使用 InfiniCLOUD 时，需要从其账户页面的 **Apps Connection** 区域获取以下三项信息：

- **WebDAV Connection URL**：WebDAV 服务器地址。
- **Connection ID**：WebDAV 认证用户名。
- **Apps Password**：WebDAV 认证密码。

> ⚠️ **警告**
>
> WebDAV 服务的登录信息与 Kopia Repository Password 是两个不同概念。
>
> - WebDAV 用户名和密码：用于访问云端存储。
> - Kopia Repository Password：用于加密和保护 Kopia Repository 中的备份数据。
> 
> 两者不要混淆。

## 4. 安装 KopiaUI

Kopia 官方提供 Windows GUI 安装程序。

**第一步**：访问 [Kopia 官网下载页面](https://kopia.io/docs/installation/)，找到 Windows 平台的安装选项：

- **KopiaUI-Setup-{版本号}.exe**：标准安装包，推荐大多数用户使用。
- **KopiaUI-{版本号}-win.zip**：便携版，解压即可运行，无需安装。

**第二步**：下载并运行安装包，按照 Windows 安装向导完成安装。

**第三步**：安装完成后启动 KopiaUI。程序会在系统托盘（Windows 右下角任务栏的隐藏图标区域）生成一个图标，同时打开主窗口。

> 💡 **说明**
>
> KopiaUI 启动后默认驻留在系统托盘中，关闭主窗口后程序仍在后台运行。这是正常行为——后台进程负责执行定时自动备份任务。

截至本文整理时，Kopia 官方 GitHub Releases 显示 `v0.23.1` 为最新稳定版本。如果安装时看到更新的稳定版本，应优先使用当时的最新稳定版。

## 5. KopiaUI 基础界面与仓库切换

启动 KopiaUI 后，Windows 右下角通知区域的隐藏图标中会出现 Kopia 图标。

右键点击该图标，可以看到以下主要选项：

```text
My Repository
Connect To Another Repository
Check For Updates Now
Launch At Startup
Notifications
Quit
```

### 5.1 My Repository

用于进入当前已经连接的 Repository。

### 5.2 Connect To Another Repository

用于连接其他 Repository 或创建新仓库。本文同时介绍本地 Repository 和 WebDAV Repository，当需要在两者之间切换时，使用此入口即可，无需重新创建已存在的仓库。

### 5.3 Launch At Startup

设置 KopiaUI 随 Windows 启动。如希望使用自动 Snapshot，建议开启。

### 5.4 Check For Updates Now

立即检查 Kopia 是否有新版本。

### 5.5 Quit

退出 KopiaUI。

## 6. 设置 Kopia 开机启动

这是自动备份中非常关键的一步——如果 KopiaUI 没有运行，定时快照任务就不会执行。

### 6.1 推荐方式：通过 Launch At Startup

右键点击 Windows 右下角隐藏图标中的 Kopia 图标，选择 **「Launch At Startup」**。启用后，KopiaUI 会自动加入 Windows 启动项。这是最简单的方式。

### 6.2 备选方式：手动添加到 Startup 文件夹

如果系统托盘菜单中没有该选项，或该选项无法正常工作，可以手动添加：

1. 按下 `Win + R`，输入 `shell:startup`，按回车。
2. 这会打开当前用户的 Startup 文件夹。
3. 将 KopiaUI 的快捷方式（通常在开始菜单中）复制或拖拽到该文件夹中。

### 6.3 为什么开机启动很重要

推荐形成以下链条：

```text
Windows 启动
    ↓
KopiaUI 自动启动
    ↓
连接 Repository
    ↓
Policy 自动运行
    ↓
按照 Snapshot Frequency 创建快照
```

如果 KopiaUI 没有运行，即使设置了 `every 10 minutes`，自动快照也不会执行。

## 7. 备份 Obsidian 到本地 Repository

本地 Repository 是最容易开始的方案——备份数据直接存放在电脑硬盘的指定文件夹中。

### 7.1 创建本地 Repository

**第一步**：打开 KopiaUI，进入 **「Repository」** 标签页，选择 **「Local Directory or NAS」**。

**第二步**：输入存储路径（示例）：

```text
D:\Backup\Kopia
```

> ⚠️ **注意（特定设置）**
>
> 以上为示例路径，请替换为自己的实际路径。

**第三步**：设置 **Repository Password**。

> ⚠️ **重要**
>
> 此密码不是 Windows 登录密码，也不是云存储密码，而是**用于加密 Kopia Repository 中所有备份数据的加密密码**。Kopia 使用端到端加密，密码永远不会离开你的电脑。
>
> **忘记此密码将无法恢复该仓库中的任何数据。** 请将密码妥善保存在密码管理器中。

**第四步**：点击 **「Create Repository」** 完成创建。

### 7.2 创建第一次 Snapshot

**第一步**：进入 **「Snapshots」** 标签页，点击 **「New Snapshot」**。

**第二步**：选择需要备份的 Obsidian Vault 路径（示例）：

```text
C:\Obsidian\MyVault
```

> ⚠️ **注意（特定设置）**
>
> 以上为示例 Vault 路径，请替换为自己的实际路径。

**第三步**：点击 **「Snapshot Now」**，开始第一次手动备份。

首次快照的时间取决于 Vault 的大小——以 Markdown 文本为主的 Vault 通常只需几秒到几分钟。

### 7.3 配置本地自动备份策略

**第一步**：进入 **「Policies」** 标签页，找到备份源路径（如 `C:\Obsidian\MyVault`），点击右侧的 **「Edit」**。

#### Snapshot Retention（快照保留策略）

- **Latest Snapshots**：设置为 `20`（保留最新 20 个 Snapshot）。
- **Hourly**：默认 `48`，保留最近 48 小时内的每小时快照。
- **Daily**：默认 `7`，保留最近 7 天的每日快照。
- **Weekly**：默认 `4`，保留最近 4 周的每周快照。
- **Monthly**：默认 `24`，保留最近 24 个月的每月快照。
- **Annual**：默认 `3`，保留最近 3 年的每年快照。

> 💡 **说明**
>
> 保留策略的生效逻辑是：**满足任意一条保留规则的快照都会被保留**。因此实际保留数量可能多于单一规则的设定值。

- **Ignore Identical Snapshots**：将 `inherit from parent` 修改为 `yes`。启用后，当文件没有变化时不会生成新的空快照，可减少不必要的快照数量。

#### Scheduling（日程安排）

- **Snapshot Frequency**：选择 `every 10 minutes`（每 10 分钟一次）。

> ⚠️ **注意（特定设置）**
>
> 以下配置是本文针对本地高频备份的示例方案，并非通用默认值。
>
> | 项目 | 示例特定值 | 通用写法/替换建议 |
> |------|-----------|------------------|
> | Latest Snapshots | `20` | 根据希望保留的近期版本数量调整（10-30） |
> | Ignore Identical Snapshots | `yes` | 希望减少无变化空快照时可启用 |
> | Snapshot Frequency（本地） | `every 10 minutes` | 根据修改频率选择 10m、30m、1h |

#### 保存策略

点击页面底部的 **「Save Policy」** 保存配置。此后 Kopia 会按照设定的频率自动执行快照。

## 8. 从本地快照恢复 Obsidian 文件

假设 `note.md` 被误删除，需要从历史快照中恢复。

**第一步**：进入 **「Snapshots」** 标签页。

**第二步**：找到对应备份源（如 `C:\Obsidian\MyVault`），浏览历史快照列表。

**第三步**：选择**误操作发生之前**的时间点。例如：

```text
14:00  正常
14:10  正常
14:20  误删除
14:30  自动备份（已包含删除状态）
```

此时应选择 `14:10` 的快照，而非最新的 `14:30`。

**第四步**：在快照内容树中找到 `note.md`，选中后点击 **「Restore」**。

**第五步**：选择恢复目标路径——可以恢复到原始位置（覆盖当前文件），也可以恢复到其他文件夹（先确认内容正确）。

> ✅ **推荐做法**
>
> 如果不确定恢复操作是否会覆盖重要文件，建议**先恢复到临时文件夹**（如桌面新文件夹），确认内容正确后再手动复制到最终位置。

## 9. 备份 Obsidian 到 WebDAV 云端 Repository

如果希望备份与电脑本地物理分离，可以使用 WebDAV Repository。Kopia 官方支持通过 WebDAV 创建或连接 Repository，需要 WebDAV URL、用户名和密码。

本文以 InfiniCLOUD 为例。

### 9.1 准备 WebDAV 凭证

以 InfiniCLOUD 为例，进入账户管理页面的 **Apps Connection**，获取：

- **WebDAV Connection URL**
- **Connection ID**（用户名）
- **Apps Password**（密码）

> ⚠️警告（WebDAV Server URL 填写注意事项）
>
> **错误做法**：
>
> 直接填写 InfiniCLOUD 提供的 WebDAV Connection URL（例如 `https://equity.infini-cloud.net/dav/`）。这会导致 Kopia 将仓库直接建立在 WebDAV 根目录下，所有仓库文件与文件夹散落在根目录中，造成文件结构混乱。
>
> **正确做法（按顺序操作）**：
>
> 1. **提前手动创建目标文件夹**：在 InfiniCLOUD 网页端或通过 WebDAV 客户端，在根目录下提前创建好用于存放 Kopia 仓库的文件夹（例如 `Kopia/KopiaBackup/`）。Kopia 本身不会通过 WebDAV 协议自动创建子目录，若未提前创建，后续创建仓库将报错。
> 
> 2. **填写完整的 WebDAV Server URL**：在 Kopia 的 WebDAV Server URL 输入框中，填写根地址加上目标子路径的完整地址。例如，若根地址为 `https://equity.infini-cloud.net/dav/`，目标文件夹为 `Kopia/KopiaBackup/`，则应填写 `https://equity.infini-cloud.net/dav/Kopia/KopiaBackup/`。

### 9.2 网络环境检查

实际操作中发现部分网络环境下，开启代理（Proxy）可能导致 Kopia 无法连接 WebDAV。如果遇到无法连接、超时等情况，可尝试暂时关闭代理后再连接。

### 9.3 创建 WebDAV Repository

**第一步**：右键点击系统托盘中的 Kopia 图标，选择 **「Connect To Another Repository」**。

**第二步**：在 Repository 界面中选择 **「WebDAV Server」**。

**第三步**：填写连接信息：

| 字段                    | 说明                        |
| --------------------- | ------------------------- |
| WebDAV Connection URL | 服务商提供的 WebDAV 地址          |
| Username              | Connection ID（WebDAV 用户名） |
| Password              | Apps Password（WebDAV 密码）  |

> ⚠️ **注意**
>
> 在 Repository 的「WebDAV Server」界面，需要输入的 Username 和 Password 是 WebDAV 的 Connection ID 和 Apps Password，而不是网盘账户登陆时输入的 Username 和 Password。

**第四步**：设置 **Kopia Repository Password**。

> ⚠️ **重要**
>
> 此密码必须与 WebDAV 认证密码不同。它是独立的加密密钥，用于保护该仓库中的所有备份数据。忘记则无法恢复。

**第五步**：点击确认完成创建。

### 9.4 创建第一次云端 Snapshot

连接云端 Repository 后，与本地操作相同：进入 **「Snapshots」** → **「New Snapshot」** → 选择 Vault 路径 → **「Snapshot Now」**。

### 9.5 配置云端自动备份策略

进入 **「Policies」** → 找到备份源 → 点击 **「Edit」**。

- **Latest Snapshots**：设置为 `20`。
- **Ignore Identical Snapshots**：设置为 `yes`。
- **Snapshot Frequency**：选择 `every 3 hours`（每 3 小时一次）。

> ⚠️ **注意（特定设置）**
>
> 云端备份频率低于本地，主要考虑到网络上传速度限制和存储成本。
>
> | 项目 | 本地 Repository | 云端 Repository |
> |------|----------------|-----------------|
> | Snapshot Frequency | `every 10 minutes` | `every 3 hours` |
> | 用途 | 高频快速恢复 | 低频异地冗余 |
>
> 如果网络条件较好、Vault 较小，可适当提高云端频率；如果 Vault 很大、上传较慢，可考虑 `every 6 hours` 或 `every 12 hours`。

## 10. 从云端快照恢复文件

云端恢复的基本逻辑与本地相同。

首先确认 KopiaUI 当前已连接到正确的 WebDAV Repository，然后：

**第一步**：进入 **「Snapshots」** 标签页。

**第二步**：找到对应备份源（如 `C:\Obsidian\MyVault`）。

**第三步**：选择误操作发生之前的时间点。

**第四步**：定位需要恢复的文件，点击 **「Restore」**。

**第五步**：选择恢复目标路径，完成恢复。

> 💡 **说明**
>
> 实际界面中快照分类名称可能随当前连接的 Repository 和 KopiaUI 版本有所不同。核心操作逻辑不变：
>
> ```text
> 确认连接正确 Repository
> ↓
> 找到正确备份源
> ↓
> 选择正确历史时间点
> ↓
> 恢复需要的文件
> ```

## 11. 本地备份与云端备份如何组合

本地 Repository 与云端 Repository 并非二选一，而是可以组合使用，形成多层防护。

### 11.1 分工建议

| 备份目的地 | 建议频率 | 主要用途 |
|-----------|---------|----------|
| 本地 Repository | 高频（如 `every 10 minutes`） | 快速恢复日常误删/误改 |
| 云端 Repository | 中低频（如 `every 3 hours`） | 硬盘损坏、电脑丢失等极端情况的异地冗余 |

### 11.2 组合结构

``````````text
Obsidian Vault
    │
    ├─ 本地 Kopia Repository（每 10 分钟）
    │      └─ 快速恢复
    └─ WebDAV Kopia Repository（每 3 小时）
           └─ 异地冗余
``````````

### 11.3 两个 Repository 是独立的

需要特别注意：**两个 Repository 不会自动互相复制**。

不要认为「本地 Repository → 自动复制 → WebDAV Repository」会自动发生。两个仓库是独立的备份体系，各自维护自己的快照历史。如果希望两个 Repository 都拥有 Obsidian Vault 的备份，需要分别配置对应的备份策略。

## 12. 常见问题与排查

### 12.1 Repository 数量如何选择

在配置 Kopia 之前，首先需要理解 Repository 的划分逻辑。这是整个备份方案的基础架构决策，直接影响到后续所有配置的合理性。

Kopia 中，一个 Repository 对应一个固定的存储后端（本地磁盘、NAS、WebDAV、S3 等）。在同一个 Repository 内，可以管理多个 Snapshot Source（备份源路径），例如：

```text
一个本地 Repository
├── Obsidian Vault
├── Documents
└── Software
```

这些目录由 Kopia 在同一个 Repository 内统一管理：

```text
Snapshot
Policy
Retention
Deduplication
Encryption
```

#### 同一个存储后端推荐使用一个 Repository

如果多个备份目录位于同一个存储后端，使用一个 Repository 通常更加合理。主要原因：

**1. 最大化利用 Repository 级别去重**

Kopia 的去重范围是 Repository 级别。同一个 Repository 中，相同内容的文件只保存一次。多个 Repository 之间无法共享去重结果，相同文件会被分别保存，降低存储效率。

**2. 统一管理 Snapshot 和维护任务**

一个 Repository 可以统一管理所有备份源的 Policy、Retention 和 Maintenance。不同目录仍然可以设置不同的备份频率——例如 Obsidian Vault 每 10 分钟一次，Documents 每日一次——不需要为此创建多个 Repository。

**3. 简化管理和恢复流程**

一个 Repository 只需维护一套密码和配置，恢复时一个入口即可查看全部 Snapshot。多个 Repository 则需要分别连接、分别维护、分别检查，管理复杂度成倍增加。

#### 不推荐同一个存储后端创建多个 Repository

例如在同一个 NAS 中分别建立 Repository A（Obsidian）、Repository B（Documents）、Repository C（Projects），虽然技术上可行，但通常不是最佳方案：

- **降低去重效率**：不同 Repository 之间无法共享去重，相同文件会被重复存储。
- **增加维护成本**：每个 Repository 需要独立管理密码、Policy、Maintenance 和恢复测试。
- **增加恢复复杂度**：恢复文件时需要先判断数据属于哪个 Repository，再分别连接查找。

#### 什么时候需要同一个存储后端使用多个 Repository

虽然一个 Repository 通常更简单，但以下情况适合拆分：

**1. 需要完全不同的安全隔离**

Repository 使用独立密码保护。如果不同数据需要不同密码、不同访问权限，可以拆分。例如 Private Repository（私人资料）与 Public Repository（共享资料）分开，避免共享同一个加密密钥。

**2. 数据生命周期完全不同**

例如工作项目（短期高频备份）与长期归档照片（低频长期保存），两者保留周期和备份策略完全不同，拆分后更容易独立管理。

**3. 不同用户或不同设备需要隔离**

例如家庭成员各自使用独立的 Repository，或工作电脑与个人电脑分开备份，避免数据混杂。

**4. 数据规模巨大，需要独立维护**

例如小型文档与数 TB 视频素材，大型数据集可能需要独立的维护策略和恢复流程。

#### 推荐判断原则

```text
同一个存储后端
+
数据安全级别相同
+
生命周期相近
↓
推荐：一个 Repository + 多个 Snapshot Source
```

```text
不同安全级别
或
不同密码需求
或
不同生命周期
或
不同用户隔离
↓
考虑：创建多个 Repository
```

#### 针对个人 Obsidian 使用场景的建议

对于大多数个人用户，推荐：

```text
一个存储后端
+
一个 Repository
+
多个 Snapshot Source
+
不同 Policy
```

例如：

```text
Local Repository
├── Obsidian Vault（every 10 minutes）
├── Documents（daily）
└── Projects（weekly）
```

如果同时需要本地快速恢复和云端异地保护，则使用两个不同存储后端的 Repository：

```text
Local Repository（高频本地恢复）
+
WebDAV Repository（低频云端异地备份）
```

两者是独立的 Repository，各自保存一份加密备份，互不影响。两个 Repository 密码可以相同也可以不同，按安全需求自行决定。

总结：

```text
同一个存储后端 → 优先一个 Repository + 多个 Source
不同存储后端 → 按需创建多个 Repository
不同安全/生命周期需求 → 可拆分多个 Repository
```

### 12.2 Kopia 开机启动设置

本文使用 Windows KopiaUI 后台运行方案。

如果：

```text
电脑启动
↓
KopiaUI 没有启动
```

那么：

```text
Policy
↓
自动 Snapshot
```

可能无法持续执行。

推荐：

```text
Windows 开机
↓
KopiaUI 启动
↓
连接 Repository
↓
Policy 自动执行
```

### 12.3 自动备份没有创建 Snapshot

如果已经设置了自动备份策略，但 Kopia 没有生成新的 Snapshot，首先需要确认 KopiaUI 是否正在运行。

本文方案使用 Windows 桌面版 KopiaUI 执行自动备份任务。如果 KopiaUI 没有启动，即使已经配置了 Snapshot Frequency，也不会执行自动备份。

首先检查：

```text
KopiaUI 是否正在运行
```

如果 KopiaUI 未运行：

```text
Windows 启动
↓
KopiaUI 未启动
↓
Policy 无法执行
↓
不会自动创建 Snapshot
```

然后检查 Policy 配置。

进入：

```text
Policies
↓
目标 Obsidian Vault
↓
Snapshot Frequency
```

确认 Snapshot Frequency 已设置。

如果显示：

```text
(none)
```

表示没有设置自动创建时间。

同时检查是否启用了：

```text
Manual Snapshots Only
```

如果开启该选项，Kopia 只允许手动创建 Snapshot：

```text
Snapshots
↓
Snapshot Now
```

不会按照计划自动执行。

最后检查：

```text
Launch At Startup
```

是否开启。

推荐保持：

```text
Windows 启动
↓
KopiaUI 自动启动
↓
连接 Repository
↓
Policy 自动执行
↓
创建 Snapshot
```

> 💡说明
>
> 自动备份是否执行，取决于 KopiaUI 是否运行，以及 Policy 中是否设置 Snapshot Frequency。仅创建 Repository 或设置保留策略，并不会自动产生 Snapshot。

### 12.4 没有文件变化时没有新的 Snapshot

如果开启：

```text
Ignore Identical Snapshots = yes
```

那么当 Obsidian Vault 没有发生变化时，Kopia 不会创建完全相同的新 Snapshot。

这是正常行为，并不代表自动备份失败。

实际逻辑：

```text
达到计划时间
↓
检查备份源
↓
检测文件变化
↓
存在变化
↓
创建新的 Snapshot

没有变化
↓
跳过相同 Snapshot
```

因此：

```text
Snapshot Frequency = every 10 minutes
```

并不表示：

```text
每10分钟一定产生一个 Snapshot
```

更准确的理解是：

```text
每10分钟执行一次检查
```

只有检测到需要保存的新内容时，才会创建新的历史版本。

### 12.5 设置了 every 10 minutes，但没有每十分钟看到 Snapshot

需要区分：

```text
计划运行
```

和：

```text
实际生成 Snapshot
```

例如：

```text
10:00
执行检查

10:10
执行检查

10:20
执行检查
```

如果三个时间点之间：

```text
Obsidian Vault 没有任何变化
```

那么：

```text
不会产生新的 Snapshot
```

Kopia 使用增量式备份机制。

后续 Snapshot 会复用 Repository 中已经存在的数据，只保存发生变化的内容，因此不会重复保存完全相同的数据。详细原因参见节 12.4。

### 12.6 WebDAV Repository 无法连接

如果创建 WebDAV Repository 时连接失败，可以按照以下顺序排查。

首先检查网络代理环境：

```text
Proxy
VPN
Clash
V2Ray
sing-box
系统代理
```

部分代理环境可能影响 Kopia 与 WebDAV 服务之间的通信。

可以尝试：

```text
关闭代理
↓
重新连接 WebDAV Repository
```

然后确认 WebDAV 参数：

```text
WebDAV Connection URL
Connection ID
Apps Password
```

是否填写正确。

如果使用 InfiniCLOUD，需要确认使用：

```text
Apps Connection
```

页面提供的信息。

不要使用：

```text
普通网页登录密码
```

因为：

```text
WebDAV 登录凭证
≠
Kopia Repository Password
```

两者用途不同。

### 12.7 Repository Password 忘记了

Repository Password 是 Kopia Repository 的核心加密凭据。

它不是：

```text
Windows 登录密码
```

也不是：

```text
WebDAV 密码
```

而是：

```text
用于解密 Kopia Repository 数据
```

如果忘记：

```text
Repository Password
```

则无法正常恢复该 Repository 中保存的数据。

因此创建 Repository 后，应立即保存密码。

推荐：

```text
密码管理器
+
离线备份记录
```

不要只把密码保存在正在备份的 Obsidian Vault 中。

> ⚠️警告
>
> Repository Password 丢失后，没有额外恢复方式。创建 Repository 时必须妥善保存密码。

### 12.8 不能直接删除 Repository 里的文件

Kopia Repository 不是普通备份文件夹。

例如：

```text
D:\Backup\Kopia
```

看起来像普通目录，但实际上：

```text
Repository
↓
由 Kopia 管理的数据结构
```

其中包含：

```text
数据块
索引
元数据
加密信息
快照关系
```

因此不要直接：

```text
进入 Repository 文件夹
↓
删除文件
↓
移动文件
↓
修改文件名称
```

否则可能破坏 Repository。

旧 Snapshot 清理应该通过：

```text
Policy
↓
Retention
↓
Maintenance
```

由 Kopia 自动管理。

### 12.9 Repository 建议放在独立目录

推荐：

```text
D:\Backup\Kopia\ObsidianVault
```

不要：

```text
D:\
```

原因：

- 目录结构更加清晰。
- 方便迁移。
- 降低误操作风险。
- 容易区分普通文件和 Repository 数据。

> ⚠️注意（特定设置）
>
> `D:\Backup\Kopia\ObsidianVault` 是本文示例路径，并非 Kopia 默认路径。
>
> 如果实际备份盘为 E 盘，可以调整为：
>
> ```text
> E:\Backup\Kopia\ObsidianVault
> ```
>
> 只需要保持 Repository 使用独立目录即可。

### 12.10 确认备份真正有效

不要只确认：

```text
Snapshot 创建成功
```

还应该测试：

```text
恢复是否成功
```

建议首次完成配置后进行一次小规模恢复测试。

例如：

```text
创建测试笔记
↓
等待 Snapshot
↓
删除测试笔记
↓
选择历史 Snapshot
↓
执行 Restore
↓
确认文件内容正确
```

只有：

```text
备份存在
+
恢复成功
```

才代表备份方案真正可靠。

## 13. 进阶用法与调整建议

### 13.1 根据 Obsidian 使用频率调整 Snapshot Frequency

本文方案采用：

```text
本地 Repository
every 10 minutes

云端 WebDAV Repository
every 3 hours
```

这是针对个人知识库高频编辑场景设计的示例配置，并不是固定要求。

实际使用时，可以根据 Obsidian 使用频率和数据重要程度调整。

例如：

| 使用场景 | 推荐 Snapshot Frequency |
| --- | --- |
| 每天大量编辑笔记、整理资料 | every 10 minutes |
| 普通日常记录 | every hour |
| 偶尔维护笔记 | every few hours |
| 云端 Repository | every 3 hours 或 every 6 hours |

如果 Obsidian Vault 修改频率较高：

```text
every 10 minutes
```

可以提供更细的恢复粒度。

如果主要用于长期归档：

```text
every hour
```

通常已经足够。

对于 WebDAV 等云端 Repository：

```text
every 3 hours
或
every 6 hours
```

可以降低：

```text
网络传输压力
↓
云端存储请求次数
↓
Repository 维护成本
```

> 💡说明（按需调整）
>
> Snapshot Frequency 越高，并不代表备份一定越好。
>
> 实际需要平衡：
>
> ```text
> 恢复粒度
>        ↕
> Snapshot 数量
>        ↕
> 存储空间与维护成本
> ```
>
> 对于 Markdown 为主的 Obsidian Vault，本地高频 Snapshot 通常不会产生过大的存储压力；云端 Repository 可以根据网络条件适当降低频率。

### 13.2 根据重要程度调整 Snapshot 保留数量

本文设置：

```text
Latest Snapshots = 20
```

表示保留最近一定数量的 Snapshot。

如果希望近期版本更加密集，可以增加：

```text
Latest Snapshots = 30
```

如果 Repository 空间有限，可以降低：

```text
Latest Snapshots = 10
```

但是需要注意：

```text
Latest Snapshots
```

不是唯一的保留规则。

Kopia Policy 还包括：

```text
Hourly
Daily
Weekly
Monthly
Annual
```

这些规则共同决定最终保留哪些历史版本。

例如：

```text
最近20次修改
+
每天一个版本
+
每月一个长期版本
```

可以同时存在。

因此不要只根据 Latest Snapshots 判断最终占用空间。

### 13.3 不要把同步和备份混为一谈

同步和备份解决的是两个不同的问题。

推荐结构：

```text
Obsidian Vault
↓
同步工具
↓
多设备同步
```

例如：

```text
电脑A
↓
电脑B
↓
电脑C
```

保持多个设备上的文件一致。

而备份：

```text
Obsidian Vault
↓
Kopia
↓
历史 Snapshot
```

用于：

```text
误删除恢复
误修改恢复
文件损坏恢复
历史版本恢复
```

两者职责不同：

```text
同步
=
保持当前状态一致

备份
=
保存过去状态
```

因此：

```text
同步工具
≠
备份工具
```

不要使用同步工具替代真正的备份。

### 13.4 不要让多个同步机制直接修改 Kopia Repository

不要设计：

```text
Kopia Repository
↓
OneDrive
↓
Syncthing
↓
其他同步工具
```

让多个同步程序同时操作 Kopia Repository 数据目录。

原因：

Kopia Repository 不是普通文件夹。

它内部包含：

```text
数据块
索引
元数据
加密信息
Repository 状态
```

正确结构应该是：

```text
Obsidian Vault
        │
        ├── 同步工具
        │       ↓
        │   多设备同步
        │
        └── Kopia
                ↓
            创建备份
```

原则：

```text
同步工具同步源数据
Kopia 管理 Repository
```

不要把 Repository 本身当作同步目录。

### 13.5 本地 Repository 与云端 Repository 分工

对于重要 Obsidian Vault，可以采用两层备份结构：

```text
本地 Repository
↓
every 10 minutes
```

用于：

```text
快速恢复
误操作恢复
最近版本恢复
```

同时：

```text
WebDAV Repository
↓
every 3 hours
```

用于：

```text
电脑损坏
硬盘故障
异地恢复
```

整体结构：

```text
Obsidian Vault
        │
        ├── Local Repository
        │       ↓
        │   高频备份
        │
        └── WebDAV Repository
                ↓
            云端副本
```

本地 Repository 提供速度。

云端 Repository 提供：

```text
物理隔离
异地保护
```

两者结合，可以降低单一存储故障风险。

### 13.6 恢复文件时，不要只选择最新 Snapshot

恢复数据时，最重要的问题不是：

```text
哪个 Snapshot 最新？
```

而是：

```text
哪个 Snapshot 是错误发生之前的正确状态？
```

例如：

```text
20:40
文件正常

20:50
文件正常

21:00
误删除文件

21:10
自动创建 Snapshot
```

此时：

应该选择：

```text
20:50
```

而不是：

```text
21:10
```

因为：

```text
21:10
```

可能已经包含：

```text
误删除后的状态
```

因此恢复前需要先确认：

```text
问题发生时间
↓
选择之前的 Snapshot
↓
执行 Restore
```

### 13.7 不要只测试备份成功，还要测试恢复成功

很多备份方案的问题不是：

```text
没有创建备份
```

而是：

```text
真正需要恢复时，不知道如何恢复
```

因此建议完成配置后进行一次恢复测试。

测试流程：

```text
创建测试笔记
↓
等待 Snapshot 创建
↓
删除测试笔记
↓
打开历史 Snapshot
↓
执行 Restore
↓
确认文件恢复
```

确认：

```text
Snapshot 可以创建
+
Snapshot 可以恢复
```

整个流程正常后，再用于重要 Vault。

### 13.8 Repository 管理最佳实践

长期使用 Kopia 时，应遵循：

#### 不直接修改 Repository 文件

不要：

```text
进入 Repository 目录
↓
删除文件
↓
移动文件
```

应该使用：

```text
KopiaUI
↓
Snapshot
↓
Restore
↓
Policy
↓
Maintenance
```

进行管理。

#### 定期维护 Repository

建议关注：

```text
Repository 状态
维护任务
恢复测试
```

确保：

```text
备份存在
+
数据可恢复
```

### 13.9 推荐的最终备份结构

对于长期使用 Obsidian 的个人用户，推荐：

```text
Obsidian Vault
        │
        ├── 同步工具
        │       ↓
        │   多设备同步
        │
        └── Kopia
                │
                ├── Local Repository
                │       ↓
                │   高频快速恢复
                │
                └── WebDAV Repository
                        ↓
                    异地灾难恢复
```

最终形成：

```text
同步
+
历史备份
+
本地恢复
+
云端副本
+
加密保护
```

的完整数据保护方案。

对于个人 Obsidian Vault：

```text
1个 Repository
+
合理设置 Policy
+
本地高速备份
+
云端异地备份
```

通常比创建大量分散 Repository 更容易维护。

只有在：

```text
安全隔离
不同密码需求
不同存储位置
不同生命周期
```

情况下，再考虑拆分多个 Repository。

## 14. 参考资源

- [Kopia 官方网站](https://kopia.io/)
- [Kopia 下载与安装文档](https://kopia.io/docs/installation/)
- [Kopia 入门指南](https://kopia.io/docs/getting-started/)
- [Kopia 功能特性](https://kopia.io/docs/features/)
- [Kopia WebDAV Repository 文档](https://kopia.io/docs/reference/command-line/common/repository-create-webdav/)
- [Kopia Policy 文档](https://kopia.io/docs/reference/command-line/common/policy-set/)
- [Kopia GitHub Releases](https://github.com/kopia/kopia/releases)
