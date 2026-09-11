# 更新日志

## [1.8.3] - 2026-09-11

### 变更
- **应要求移除 GitHub Desktop**：从 `software-config.json` 移除 `github-desktop` 项，SKILL.md 同步删除对应软件行与 frontmatter 描述，内置软件数量由 10 款回退为 9 款
- **随 GitHub Desktop 一并回退 `dependsOn` 依赖机制**：该字段及"依赖顺序/串行"流程说明系为 GitHub Desktop 引入，现无任何软件使用，一并移除以保持配置精简
- frontmatter `version` 提升为 `1.8.3`

---

## [1.8.2] - 2026-09-11

### 新增
- **新增软件 GitHub Desktop**（GitHub 官方桌面客户端）：默认用户安装版（`%LOCALAPPDATA%\GitHubDesktop`），自动获取最新版（GitHub Releases / 官方 central 重定向兜底）
- 新增 `dependsOn` 字段：配置了依赖的软件按序串行安装，依赖软件完成并可用后才开始下载本软件

### 调整
- **安装顺序：Watt Toolkit 优先于 GitHub Desktop**：GitHub Desktop 配置 `dependsOn: ["watt-toolkit"]`，技能先完成 Watt Toolkit 安装并提示用户启用网络加速，再下载 GitHub Desktop，避免直连 GitHub 因网络问题下载失败
- 使用流程新增"依赖顺序（串行）"说明；包含软件数量由 9 款更新为 10 款；frontmatter `version` 提升为 `1.8.2`

---

## [1.8.1] - 2026-09-11

### 调整
- **安装前新增"安装位置确认"步骤（使用流程第 6 步）**：开始下载前必须先询问用户装默认系统盘（`C:\Program Files`）还是用户指定目录；指定非默认路径的软件按该路径安装，并记下位置用于后续验证（半自动 GUI 安装的软件提示用户在向导中手动改路径）
- **安装验证失败处理增强（使用流程第 7 步）**：安装完成后再检测未命中预期路径时，不直接判为失败，而是先询问用户——是"安装失败了"，还是"放到了其他盘/非默认目录"；后者请用户提供实际路径进行验证，并将该路径补充进 `checkPaths`，避免误判
- 使用流程重新编号：原第 6 步"执行安装"、第 7 步"输出结果"顺延为第 7、8 步

---

## [1.8.0] - 2026-09-11

### 新增
- **新增软件 Watt Toolkit**（网络加速工具箱，原 Steam++）：固定到最新稳定正式版 3.1.0，系统安装版，默认安装到 `C:\Program Files\WattToolkit`
  - 说明：GitHub 的 `releases/latest` 接口会返回候选版 3.0.0-rc.16（该版本官方注明数字签名到期、可能被杀毒软件误报），无法自动跟随稳定版，故采用固定稳定版本号，升级需手动更新 version 与 downloadUrl
  - 注意：**加速功能无需登录账号即可使用**（经实测），仅部分高级功能需登录账号；实测确认安装器（Steam++ 自研 Avalonia）不支持命令行静默安装，`/S` 等标准参数均无效**，故采用【半自动 GUI 安装】方案——技能负责下载/校验/启动安装器，安装时由用户手动完成向导（选路径 → 立即安装 → 立即体验），并配套安装指引文档
  - 安装目录实测为 **`Steam++`**（并非新手面猜想中的 WattToolkit），安装向导支持自定义到任意盘（如 `D:\Program Files\Steam++`）；checkPaths 已补充 `D:\Program Files\Steam++\Steam++.exe` 等实际路径
- **SKILL.md 顶部新增"平台说明"声明**：明确本技能及安装流程仅经过 Windows（Windows 10 / 11 x64）测试验证，其他系统未测试、不保证可用
- frontmatter 描述（description / description_zh / description_en）随软件列表同步加入 Watt Toolkit

### 调整
- frontmatter `version` 提升为 `1.8.0`
- 包含软件数量由 8 款更新为 9 款

---

## [1.7.1] - 2026-09-11

### 新增
- **新增文档《MySQL 配置向导小白指南》**（`docs/mysql-config-guide.md`）：逐步骤面向新手说明 MySQL Configurator 每个选项的含义与推荐值，覆盖 Welcome 到 Configuration Complete 全部 11 步，含常见问题解答
- SKILL.md 顶部新增 MySQL 配置指引链接，指向该指南

### 修复
- 纠正 MySQL 配置流程描述：第 10 步 Apply Configuration 完成后按钮为 **Next**（进入第 11 步），最后一步 Configuration Complete 才是 **Finish**；原描述误写成 Finish

---

## [1.7.0] - 2026-09-10

### 新增
- **新增软件 MySQL Server (LTS)**：官方 MySQL Community Server MSI 静默安装（`/quiet /norestart`），系统安装版装到 `C:\Program Files\MySQL\MySQL Server 9.7`，需管理员权限
- 采用官方最新 LTS 长期支持版 9.7.2，下载地址为已验证的官方 CDN 直链 `https://cdn.mysql.com/Downloads/MySQL-9.7/mysql-9.7.2-winx64.msi`
- SKILL.md 软件表同步更新（7 款 → 8 款），新增 MySQL 特别说明

### 注意
- MySQL 8.0 后官网停止提供 MySQL Installer，改由独立的 Community Server MSI 安装；因 CDN 主版本目录（`MySQL-9.7`）无法由完整版本号（`9.7.2`）直接推导，MySQL 版本采用**手动更新**而非自动检测，升级时需同步修改 `downloadUrl`
- **MSI 仅安装 Server 本体，不会自动启动服务**：安装完成后需手动运行配套的 MySQL Configurator 完成实例初始化（设置 root 密码、注册 Windows 服务、配置端口）
- 安装前需确保已安装 Microsoft Visual C++ 2019 Redistributable

---

## [1.6.0] - 2026-09-09

### 新增
- **page-regex 版本源类型**：解析软件官网下载页（pageUrl），按 pattern 正则提取最新版本号，填入 downloadUrlTemplate 生成下载地址。适用于官方无 API、无"最新版"重定向链接、但下载页展示最新版链接的软件

### 调整
- **Everything 改为自动获取官方最新稳定版**：原为固定版本 1.4.1.1032（因 voidtools 无重定向链接和公开 API），现通过解析官网下载页首个 x64-Setup 链接得到版本号（页面首个版本区块为稳定版，Beta 排在其后），自动拼出对应 x64 MSI 地址；downloadUrl 保留 1.4.1.1032 作为解析失败时的回退
- **Trae 系列软件名称统一为官方正式名**：配置和文档中的 "Trae Code" / "Trae Work" 统一为 "TraeCode" / "TraeWork"（TRAE 官方命名，TraeWork 即原 TRAE Work / TRAE SOLO 的现名）

---

## [1.5.1] - 2026-09-09

### 修复
- **桌面快捷方式重复创建**：desktopShortcut 检查逻辑只查用户桌面，而系统安装版的安装器（如 Visual Studio Code 的 desktopicon 安装任务）把快捷方式建在公共桌面（Common Desktop），技能随后又在用户桌面补建，导致桌面上出现两个相同图标。现改为同时检查用户桌面与公共桌面，任一存在即跳过创建

### 调整
- 文案统一：SKILL.md 与配置文件中的 "VS Code" 简写统一为 "Visual Studio Code"（便于他人理解）

---

## [1.5.0] - 2026-09-09

### 新增
- 新增软件 **Everything**（voidtools 文件秒搜工具）v1.4.1.1032：官方 x64 MSI 静默安装（`/quiet /norestart`），系统安装版装到 `C:\Program Files\Everything`，需管理员权限
- checkPaths 覆盖 x64 / x86 / 用户级三种安装路径
- SKILL.md 软件表同步更新（6 款 → 7 款）

### 注意
- voidtools 官方直链 URL 含版本号且无"最新版"自动重定向地址，Everything 新版本发布后需手动更新 `downloadUrl`（最新稳定版可在官方下载页查询）

---

## [1.4.2] - 2026-09-09

### 修复
- **Git 版本源模板拼接 bug**：`downloadUrlTemplate` 硬编码 `v{version}.windows.1` 后缀，当最新 release tag 为 `.windows.5` 等补丁版本时拼出的下载地址 404，回退到默认 `downloadUrl` 导致装回 2.47.0 旧版。改为 `useAssetUrl: true`，按 `assetPattern` 匹配 release 资产后直接使用其 `browser_download_url`，不再拼接模板
- SKILL.md 同步更新 github-release 策略说明（含"不要模板拼接"的警示）

### 根因说明
- 昨日（2026-09-08）安装 Node.js 20.18.0 / Git 2.47.0 时执行的是部署目录的 v1.0 旧配置（固定下载地址），v1.1.0 加入的自动版本获取功能当时只存在于开发副本未同步，导致安装的不是官方推荐最新版

---

## [1.4.1] - 2026-09-09

### 修复
- 部署目录（`~\.trae-cn\skills\it-software-installer\`）此前停留在旧版配置，Trae Code / Trae Work 国内版检测路径缺失导致已安装软件被误判为"待安装"，本次重新应用 v1.2.0 的修复并验证通过（Trae CN 3.3.98、TRAE SOLO CN 0.1.63 均正确识别）

### 变更
- 开发版完整同步到部署目录（SKILL.md、software-config.json、CHANGELOG.md），消除两份副本版本不一致问题
- 同步时确认：国内版实际安装名为 "TraeCode CN (User)"（目录 `Trae CN`）和 "TraeWork CN (User)"（目录 `TRAE SOLO CN`），注册表卸载项可作辅助检测依据

---

## [1.4.0] - 2026-09-08

### 新增
- 明确"默认系统安装版"设计原则，软件统一安装到 `C:\Program Files`
- 配置新增 `installScope` 字段：`system`（系统安装版）/ `user`（用户安装版）
- 配置新增 `installScopeNote` 字段：安装方式说明
- 配置根节点新增 `installScopeDefault: "system"`，明确默认策略
- 软件列表增加"安装方式"和"安装路径"列，一目了然

### 调整
- **VS Code**：从用户安装版（User Installer）改为系统安装版（System Installer），安装到 `C:\Program Files\Microsoft VS Code`
- **Chrome、Node.js、Git**：确认使用系统安装版，安装路径统一在 `C:\Program Files` 下
- **Trae Code、Trae Work**：标注为用户安装版并说明原因（Electron 应用官方仅提供用户安装版，支持自动更新）

---

## [1.3.0] - 2026-09-08

### 新增
- VS Code 新增 `installNotes` 安装前提示，告知用户可能出现的"错误 5：拒绝访问"问题及影响
- VS Code 新增 `desktopShortcut` 配置，安装完成后自动检查并创建桌面快捷方式
- 技能新增 `CHANGELOG.md` 更新日志文件

### 修复
- VS Code 从用户安装版（user installer）改为系统安装版（system installer），安装到 `C:\Program Files`，需要管理员权限，避免 TRAE 沙箱对 `AppData` 目录的写入限制导致的"错误 5：拒绝访问"问题
- VS Code 安装参数增加 `desktopicon`，确保安装时创建桌面图标
- VS Code checkPaths 调整优先级，系统安装路径在前

### 配置变更
- `software-config.json` 软件项新增可选字段：`installNotes`（安装前提示）、`desktopShortcut`（桌面快捷方式配置）

---

## [1.2.0] - 2026-09-08

### 新增
- 询问方式改为选项式（A/B/C/D 四个选项），用户操作更直观
- 技能说明中增加 `checkPaths` 多路径覆盖的重要性说明

### 修复
- Trae Code 国内版检测路径新增 `%LOCALAPPDATA%\Programs\Trae CN\Trae CN.exe`
- Trae Work 国内版检测路径新增 `%LOCALAPPDATA%\Programs\TRAE SOLO CN\TRAE SOLO CN.exe`
- 修正了国内版 Trae 系列软件检测不到的问题（实际安装目录名与之前假设不同）

---

## [1.1.0] - 2026-09-08

### 新增
- 支持自动获取最新版本：Node.js 通过官方 API 查询最新 LTS，Git 通过 GitHub Releases 查询最新版
- 支持多发行渠道：Trae Code 和 Trae Work 支持国内版/国际版选择
- 检测已安装软件时输出版本号
- 用户可自定义版本和发行渠道偏好

### 配置变更
- `software-config.json` 新增 `versionSource` 字段，支持 `fixed-url`、`nodejs-api`、`github-release` 三种版本获取方式
- 新增 `hasChannels`、`defaultChannel`、`channels` 多渠道配置

---

## [1.0.0] - 2026-09-08

### 新增
- 技能初始版本，支持 6 款常用软件的批量安装
- 预置软件：Google Chrome、Node.js (LTS)、Git for Windows、Visual Studio Code、Trae Code、Trae Work
- 自动检测已安装软件并跳过
- 支持最多 3 个软件并发安装
- 支持通过配置文件自定义软件列表
