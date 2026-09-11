---
name: it-software-installer
display_name: IT常用软件一键安装
display_name_en: IT Software One-Click Installer
description: "IT常用软件一键安装：批量安装Chrome、Node.js、Git、Visual Studio Code、MySQL Server、Watt Toolkit、TraeCode、TraeWork等常用工具，自动获取最新版本并检测已安装软件，支持并发安装。当用户需要批量安装常用软件或配置开发环境时调用。"
description_zh: "IT常用软件一键安装：自动获取最新版本，检测已装软件，批量安装Chrome、Node.js、Git、Visual Studio Code、MySQL Server、Watt Toolkit、TraeCode、TraeWork等常用工具，支持并发安装，快速完成开发环境配置。"
description_en: "One-click installer for common IT software. Automatically fetches the latest versions, detects already-installed programs, and batch-installs Chrome, Node.js, Git, Visual Studio Code, MySQL Server, Watt Toolkit, TraeCode and TraeWork with concurrent downloads. Use when users need to install common software in bulk or set up a development environment."
version: 1.8.3
author: 雨轻霄
category: 工具
platforms: [WorkBuddy, TraeWork]
---

# IT常用软件一键安装

一键批量安装 IT 从业者常用的 Windows 软件，**执行时自动获取各软件的最新推荐版本**，自动检测已安装软件并跳过，支持并发安装，节省环境配置时间。

> ⚠️ **平台说明**：本技能及其安装流程**仅经过 Windows（Windows 10 / 11 x64）测试并验证**；其他系统（如 Linux、macOS）**未测试，不保证可用**。

> 版本更新记录见 [CHANGELOG.md](./meta/CHANGELOG.md)

> **💡 MySQL 配置指引**：安装完 MySQL 后需手动运行 MySQL Configurator 完成实例配置，新手请对照 [《MySQL 配置向导小白指南》](./docs/mysql-config-guide.md) 逐步操作。

> **💡 Watt Toolkit 安装指引**：Watt Toolkit 官方安装器不支持静默安装，技能会启动图形向导，请对照 [《Watt Toolkit 安装指引》](./docs/watt-toolkit-install-guide.md) 手动完成。

## 设计原则

**默认系统安装版**：技能中配置的软件优先使用系统安装版（System Installer），统一安装到 `C:\Program Files` 下，路径规范、便于管理。仅当软件本身只提供用户安装版时（如部分 Electron 应用），才使用用户安装版并明确标注。

## 包含的软件

当前预置以下 9 款常用软件（可在配置文件中自定义增删）：

| 软件 | 说明 | 版本获取方式 | 安装方式 | 安装路径 | 发行渠道 |
|------|------|-------------|----------|----------|----------|
| **Google Chrome** | 谷歌浏览器 | 自动获取（企业版MSI） | 系统安装版 | `C:\Program Files\Google\Chrome` | - |
| **Node.js (LTS)** | JavaScript 运行时 | 自动获取（官方API） | 系统安装版 | `C:\Program Files\nodejs` | - |
| **Git for Windows** | 版本控制工具 | 自动获取（GitHub Releases） | 系统安装版 | `C:\Program Files\Git` | - |
| **Visual Studio Code** | 代码编辑器 | 自动获取（官方重定向） | 系统安装版 | `C:\Program Files\Microsoft VS Code` | - |
| **MySQL Server (LTS)** | 关系型数据库 | LTS 固定版（官方CDN直链） | 系统安装版 | `C:\Program Files\MySQL\MySQL Server 9.7` | - |
| **TraeCode** | AI 编程助手 | 自动获取（官方重定向） | 用户安装版 | `%LOCALAPPDATA%\Programs\Trae CN` | 国内版 / 国际版 |
| **TraeWork** | AI 工作助手 | 自动获取（官方重定向） | 用户安装版 | `%LOCALAPPDATA%\Programs\TRAE SOLO CN` | 国内版 / 国际版 |
| **Everything** | 文件秒搜工具（voidtools） | 自动获取（官网页面解析） | 系统安装版 | `C:\Program Files\Everything` | - |
| **Watt Toolkit** | 网络加速工具箱（原 Steam++） | 固定稳定版 3.1.0（GitHub latest 接口返回候选版） | 系统安装版 | `C:\Program Files\Steam++`（可自定义） | - |

> Trae 系列为 Electron 应用，官方仅提供用户安装版（安装到用户目录），支持静默自动更新，使用体验更佳。

## 何时调用

- 用户说"安装常用软件"、"一键装软件"、"配置开发环境"、"装IT必备软件"等
- 用户需要一次性安装多个工具软件
- 用户提到"使用 it-software-installer 技能"

## 自动版本检测

技能执行时会先自动查询每个软件的最新推荐版本，确保安装的是当前最新稳定版。

### 版本检测策略

| 策略类型 | 说明 | 适用软件 |
|----------|------|----------|
| `fixed-url` | 下载链接本身就是最新版地址（重定向或固定最新包） | Chrome、Visual Studio Code、TraeCode、TraeWork |
| `nodejs-api` | 通过 Node.js 官方 API 查询最新 LTS 版本 | Node.js |
| `github-release` | 通过 GitHub Releases API 查询最新发布版本 | Git for Windows |
| `page-regex` | 解析官网下载页，正则提取最新版本号后拼接下载地址 | Everything |

> **MySQL 特别说明**：MySQL 8.0 后官网停止提供 MySQL Installer，本技能使用官方独立的 **MySQL Community Server MSI** 直链（固定官方最新 LTS 9.7 版，因 CDN 主版本目录无法由完整版本号推导，采用手动更新而非自动检测）。MSI 只安装 Server 本体，**不会自动启动服务**，安装完成后需手动运行配套的 MySQL Configurator 完成实例初始化（设置 root 密码、注册 Windows 服务、配置端口）。

### Node.js LTS 版本检测

调用 `https://nodejs.org/dist/index.json`，遍历返回数组，找到第一个 `lts` 字段非空的版本（即最新 LTS），提取版本号后拼接到下载地址模板中。

### GitHub Release 版本检测

调用 `https://api.github.com/repos/{repo}/releases/latest`，在返回的 `assets` 数组中按 `assetPattern` 通配符匹配安装包文件名，直接使用匹配资产的 `browser_download_url` 作为下载地址，并从 `tag_name` 中提取版本号用于展示。

> **重要**：不要用模板拼接下载地址。release 的 tag 常带可变后缀（如 Git 的 `v2.55.0.windows.5`），硬编码 `.windows.1` 之类后缀会拼出 404 地址，触发回退到旧版 `downloadUrl`，导致"看似自动更新、实际装回旧版"。

> **注意**：GitHub API 匿名调用有速率限制（每小时 60 次），正常使用完全足够。如遇限制可回退到配置中的默认 `downloadUrl`。

## 配置文件

配置文件路径：`.trae/skills/it-software-installer/software-config.json`

配置结构：
```json
{
  "maxConcurrent": 3,        // 最大并发安装数（默认3）
  "networkMode": "auto",     // auto | fast | slow （slow时逐个安装）
  "software": [...]          // 软件列表
}
```

### 软件项字段说明

| 字段 | 类型 | 说明 |
|------|------|------|
| id | string | 唯一标识 |
| name | string | 软件显示名称 |
| version | string | 版本策略："latest" / "auto-lts" / "auto-latest" / 具体版本号 |
| versionSource | object | 版本来源配置（见下方说明） |
| hasChannels | boolean | 是否有多个发行渠道（如国内版/国际版），可选 |
| defaultChannel | string | 默认渠道标识，hasChannels 为 true 时必填 |
| channels | object | 各渠道配置，hasChannels 为 true 时必填（见下方说明） |
| downloadUrl | string | 默认下载地址（版本检测失败时的回退） |
| installerType | string | msi / exe |
| installArgs | array | 静默安装参数 |
| checkPaths | array | 检测是否已安装的路径（支持环境变量如 %LOCALAPPDATA%） |
| versionCommand | string | 获取版本的命令，{path} 会替换为实际路径 |
| requiresAdmin | boolean | 是否需要管理员权限 |
| installScope | string | 安装方式：`system`（系统安装版，安装到 C:\Program Files）/ `user`（用户安装版，安装到用户目录） |
| installScopeNote | string | 可选，安装方式说明（如为什么使用用户安装版） |
| installNotes | array | 可选，安装前需要向用户展示的注意事项（每一项为一行提示） |
| desktopShortcut | object | 可选，桌面快捷方式配置，安装完成后自动检查并创建 |

### versionSource 配置

**fixed-url 类型**（下载地址始终为最新版）：
```json
{
  "type": "fixed-url",
  "note": "说明文字"
}
```

**page-regex 类型**（解析官网下载页获取最新版本）：
```json
{
  "type": "page-regex",
  "pageUrl": "https://example.com/downloads/",
  "pattern": "App-([0-9.]+)\\.exe",
  "downloadUrlTemplate": "https://example.com/App-{version}.exe"
}
```

> 获取 pageUrl 页面源码，取第一个匹配 `pattern` 的捕获组作为版本号，填入 `downloadUrlTemplate` 的 `{version}` 占位符。适用于官方无 API、无"最新版"重定向链接、但下载页展示最新版链接的软件；使用前需确认页面首个版本区块是稳定版（Beta/Alpha 通常排在其后）。

**nodejs-api 类型**：
```json
{
  "type": "nodejs-api",
  "apiUrl": "https://nodejs.org/dist/index.json",
  "channel": "lts",
  "downloadUrlTemplate": "https://nodejs.org/dist/v{version}/node-v{version}-x64.msi"
}
```

**github-release 类型**：
```json
{
  "type": "github-release",
  "repo": "owner/repo",
  "assetPattern": "匹配安装包文件名的通配符",
  "useAssetUrl": true
}
```

> `useAssetUrl: true` 表示直接使用 release 资产的 `browser_download_url`，不拼接模板。

### channels 多渠道配置

当 `hasChannels` 为 `true` 时，通过 `channels` 配置多个发行渠道（如国内版/国际版）：

```json
{
  "hasChannels": true,
  "defaultChannel": "cn",
  "channels": {
    "cn": {
      "label": "国内版",
      "downloadUrl": "https://example-cn.com/latest/Setup.exe",
      "checkPaths": [
        "%LOCALAPPDATA%\\Programs\\app\\App.exe"
      ]
    },
    "global": {
      "label": "国际版",
      "downloadUrl": "https://example.com/download/win/latest",
      "checkPaths": [
        "%LOCALAPPDATA%\\Programs\\app\\App.exe"
      ]
    }
  }
}
```

| 字段 | 类型 | 说明 |
|------|------|------|
| label | string | 渠道显示名称（如"国内版"、"国际版"） |
| downloadUrl | string | 该渠道的下载地址 |
| checkPaths | array | 该渠道的安装检测路径 |

用户选择渠道后，使用对应渠道的 `downloadUrl` 和 `checkPaths` 覆盖软件的默认配置。

### desktopShortcut 桌面快捷方式配置

配置了 `desktopShortcut` 的软件，安装完成后会自动检查桌面快捷方式是否存在，不存在则自动创建。

> **重要**：必须同时检查**用户桌面**（`%USERPROFILE%\Desktop`）和**公共桌面**（`[Environment]::GetFolderPath('CommonDesktopDirectory')`，系统安装版的安装器通常把快捷方式建在这里）。**任一处存在即视为已有快捷方式，直接跳过创建**，否则桌面上会出现两个相同图标。

```json
{
  "desktopShortcut": {
    "name": "Visual Studio Code.lnk",
    "target": "Code.exe"
  }
}
```

| 字段 | 类型 | 说明 |
|------|------|------|
| name | string | 快捷方式文件名（含 .lnk 后缀） |
| target | string | 目标可执行文件名（相对于 checkPaths 中检测到的安装目录） |

创建方法：使用 `$WScriptShell = New-Object -ComObject WScript.Shell` 创建快捷方式，目标路径为检测到的已安装路径所在目录 + target 文件名。

### installNotes 安装注意事项

配置了 `installNotes` 的软件，在安装前会向用户展示注意事项：

```json
{
  "installNotes": [
    "安装前提示1",
    "安装前提示2"
  ]
}
```

展示方式：在确认安装列表后、开始安装前，逐条列出有 `installNotes` 的软件的注意事项。

## 使用流程

### 1. 读取配置并列出软件

读取 `software-config.json`，获取软件列表和安装策略。

向用户展示当前技能配置中的全部软件，格式如下：

| 序号 | 软件 | 默认版本策略 | 发行渠道 | 说明 |
|------|------|-------------|----------|------|
| 1 | Google Chrome | 最新版（自动获取） | - | 谷歌浏览器 |
| 2 | Node.js (LTS) | 最新LTS版（自动获取） | - | JavaScript 运行时 |
| 5 | TraeCode | 最新版（自动获取） | 国内版（默认）/ 国际版 | AI 编程助手 |
| ... | ... | ... | ... | ... |

### 2. 选项式询问版本偏好与发行渠道

用选项式的方式向用户确认安装配置，方便用户快速选择：

> "以上是配置中的全部软件，默认会安装各软件的最新推荐版本。请选择：
>
> **A. 全部按默认配置安装**（推荐，最新版 + 国内版）
> **B. 我要调整个别软件的版本或渠道**
> **C. 我要选择部分软件安装**
> **D. 取消安装**"

根据用户选择的选项处理：

**选项 A — 全部按默认**：
- 所有软件使用默认版本策略和默认渠道
- 直接进入第 3 步

**选项 B — 调整版本/渠道**：
- 进一步询问："你要调整哪个软件？（输入序号或名称）"
- 用户指定软件后，询问具体调整：
  - 版本："你要安装哪个版本？（输入版本号，或直接回车用最新版）"
  - 渠道（仅多渠道软件）："国内版还是国际版？（国内版/国际版）"
- 调整完一个后，问"还要调整其他软件吗？"
- 全部调整完后进入第 3 步

**选项 C — 选择部分软件**：
- 进一步询问："请输入你要安装的软件序号，用空格或逗号分隔（如：1 3 5）"
- 解析用户输入，只保留选中的软件
- 然后问"版本和渠道需要调整吗？" → 需要则走选项 B 的流程，不需要则进入第 3 步

**选项 D — 取消**：
- 结束技能，不执行安装

最终确定待安装软件列表、各软件的版本策略和发行渠道。

**多渠道软件说明：**
- 配置中 `hasChannels: true` 的软件支持多个发行渠道
- `defaultChannel` 指定默认渠道
- `channels` 对象中每个渠道包含：`label`（显示名称）、`downloadUrl`（下载地址）、`checkPaths`（安装检测路径）
- 用户选择渠道后，使用对应渠道的配置覆盖默认的 `downloadUrl` 和 `checkPaths`

### 3. 获取对应版本

根据确认后的版本策略，查询每个软件的实际下载版本和地址：
- 策略为"最新版" / "auto-lts" / "auto-latest" → 自动查询最新版本
  - `fixed-url`：直接使用配置中的下载地址
  - `nodejs-api`：调用 Node.js 官方 API 获取对应 LTS 版本号，生成下载地址
  - `github-release`：调用 GitHub API 获取最新 release，按 `assetPattern` 匹配资产并直接使用其 `browser_download_url`
  - `page-regex`：获取 pageUrl 页面源码，按 `pattern` 正则提取版本号，填入 `downloadUrlTemplate` 生成下载地址
- 策略为用户指定的具体版本号 → 用指定版本号替换下载地址模板中的 `{version}`
- 版本检测失败 → 回退使用配置中的 `downloadUrl` 作为默认值

向用户展示最终的安装版本列表。

### 4. 检测已安装软件

对每个待安装的软件，遍历 `checkPaths` 检查文件是否存在。**只要有一个路径存在即视为已安装**。

> **重要**：`checkPaths` 应尽可能多地覆盖可能的安装路径（不同版本、不同渠道的安装目录可能不同），按优先级从高到低排列。例如 Trae 系列软件国内版可能安装在 `Trae CN` 或 `TRAE SOLO CN` 等不同目录下。

环境变量展开：使用 PowerShell 的 `[Environment]::ExpandEnvironmentVariables()` 处理路径中的 `%VAR%`。

**对于已安装的软件：**
- 如果配置了 `versionCommand`，执行该命令获取当前安装的版本号（将 `{path}` 替换为检测到的实际路径）
- 如果没有配置 `versionCommand`，标记为"已安装"即可
- 将已安装软件从待安装列表中移除（除非用户明确要求重新安装）

**输出检测结果表格，必须包含已安装软件的版本号：**

| 软件 | 状态 | 当前版本 | 待安装版本 |
|------|------|----------|-----------|
| Google Chrome | 已安装（跳过） | 152.0.7977.83 | - |
| Node.js | 待安装 | - | v22.11.0 |
| Git | 待安装 | - | 2.47.0 |
| TraeCode | 已安装（跳过） | 已安装 | - |

### 5. 确认安装列表并展示注意事项

向用户展示最终待安装的软件列表及对应版本。

同时，检查待安装软件中是否有 `installNotes` 配置，如有则向用户展示安装注意事项：

> "以下软件安装时有一些注意事项：
> - **Visual Studio Code**：
>   - 使用系统安装版（安装到 C:\Program Files），需要管理员权限
>   - 若安装过程中出现"错误 5：拒绝访问"弹窗，一般是开始菜单快捷方式创建失败，不影响软件本身使用
>   - 安装完成后会自动检查并创建桌面快捷方式"

确认用户已知晓后，进入安装位置确认。

### 6. 确认安装位置（先询问，再安装）

在开始下载安装前，**必须先询问用户**：待安装软件装到默认系统盘（`C:\Program Files`），还是用户指定的位置。

- **A 全部按默认安装时**：统一询问一次"默认装到系统盘（`C:\Program Files`），需要改到其他盘或指定目录吗？"，如需指定位置请用户提供目标目录。
- **B/C 选择部分软件时**：一并询问"这些软件装默认盘，还是你指定位置？"。支持自定义路径的软件（如 Watt Toolkit、Everything、Trae 系列）可让用户填写目标目录（如 `D:\xxx`）。
- 记录用户确认的安装位置；指定了非默认目录的软件，安装时按该目录执行（配置安装参数，或在安装器向导中手动指定），并在后续验证时优先使用该实际路径。
- **无法用命令行指定路径的软件**（如 Watt Toolkit 的半自动 GUI 安装）：提示用户在安装向导的路径选择处手动改到目标目录，并记下该位置用于验证。

### 7. 执行安装

**并发策略：**
- `networkMode: "fast"` 或 `"auto"` 且下载速度正常 → 最多同时下载 `maxConcurrent` 个
- `networkMode: "slow"` 或检测到下载速度 < 500KB/s → 逐个下载安装
- 最多同时运行 3 个安装程序（避免系统卡死）

**安装步骤（每个软件）：**
1. 使用 `curl.exe -L -o <temp_path> <url>` 下载安装包到 `$env:TEMP`
2. 下载完成后，根据 `requiresAdmin` 决定是否以管理员权限运行：
   - 需要管理员：`Start-Process <installer> -ArgumentList <args> -Verb RunAs -Wait`
   - 不需要管理员：`Start-Process <installer> -ArgumentList <args> -Wait`
3. 安装完成后，再次检测 `checkPaths`（若用户指定了非默认位置，优先按实际路径验证）确认安装成功。**若在预期位置没检测到安装文件，不要直接判定为安装失败**，先询问用户属于哪种情况：
   - **安装失败了**（安装器报错 / UAC 未确认 / 被中断等）→ 根据原因重新安装；
   - **放到了其他盘或非默认目录** → 请用户提供可执行文件的确实路径，按该实际路径验证是否就位，并将该路径**补充进 `checkPaths`**（或作为本次验证的生效路径），避免后续重复误判。
4. **桌面快捷方式检查与创建**（仅配置了 `desktopShortcut` 的软件）：
   - 检查两个桌面位置是否已有该快捷方式（任一存在即跳过，避免重复创建）：
     - 用户桌面：`Test-Path "$env:USERPROFILE\Desktop\<name>"`
     - 公共桌面：`Test-Path "$([Environment]::GetFolderPath('CommonDesktopDirectory'))\<name>"`（系统安装版的安装器通常建在这里）
   - 两处都不存在时，才使用 WScript.Shell 在用户桌面创建快捷方式：
     ```powershell
     $WScriptShell = New-Object -ComObject WScript.Shell
     $shortcut = $WScriptShell.CreateShortcut("$env:USERPROFILE\Desktop\<name>")
     $shortcut.TargetPath = "<install_dir>\<target>"
     $shortcut.Save()
     ```
   - 其中 `<install_dir>` 是 checkPaths 中检测到的已安装文件所在目录
5. 清理安装包文件

### 8. 输出结果

安装完成后，输出汇总表格：

| 软件 | 安装版本 | 状态 |
|------|----------|------|
| Google Chrome | 152.0.7977.83 | 已跳过（已安装） |
| Node.js | v20.18.0 | 安装成功 |
| Git | 2.47.0 | 安装失败 |

## 自定义软件

### 添加软件

在 `software-config.json` 的 `software` 数组中添加新对象，包含所有必填字段。确保：
- `id` 唯一
- 配置合适的 `versionSource` 类型
- `downloadUrl` 作为版本检测失败时的回退地址
- `checkPaths` 覆盖默认安装路径
- 静默安装参数正确

### 删除软件

从 `software` 数组中移除对应项即可。

## 注意事项

- 安装 MSI 使用 `msiexec /i <file> <args>`
- 安装 EXE 直接运行安装程序加参数
- 管理员权限安装会弹出 UAC 提示，需告知用户点击"是"
- 下载文件名建议使用 `<id>-setup.<ext>` 避免冲突
- 所有路径使用绝对路径，环境变量用 `%VAR%` 格式
- PowerShell 执行策略限制不影响 `curl.exe` 和 `msiexec` 等外部命令
- GitHub API 匿名调用有速率限制，版本检测失败时自动回退到默认下载地址
