# IT常用软件一键安装

> IT Software One-Click Installer — WorkBuddy / TraeWork 技能

一键批量安装 IT 从业者常用的 Windows 软件。**执行时自动获取各软件的最新推荐版本**，自动检测已安装软件并跳过，支持并发安装，快速完成开发环境配置。

## 功能特性

- ⚡ **自动获取最新推荐版本**：每个软件安装前自动查询最新稳定版（LTS / 最新 Release）
- ✅ **自动检测已安装软件**：已安装则跳过，不重复安装
- 🚀 **支持并发安装**：默认最多并行安装 3 个软件，节省时间
- 📦 **默认系统安装版**：优先安装到 `C:\Program Files`，路径规范、便于管理
- 🧩 **高度可配置**：`software-config.json` 可增删软件、自定义版本源与安装参数
- 🔁 **可复用**：在 WorkBuddy、TraeWork 中一句话即可触发

## 包含的软件

当前预置以下 9 款常用软件（可在配置文件中自定义增删）：

| 软件 | 说明 | 版本获取方式 | 安装方式 | 安装路径 |
|------|------|-------------|----------|----------|
| **Google Chrome** | 谷歌浏览器 | 自动获取（企业版 MSI） | 系统安装版 | `C:\Program Files\Google\Chrome` |
| **Node.js (LTS)** | JavaScript 运行时 | 自动获取（官方 API） | 系统安装版 | `C:\Program Files\nodejs` |
| **Git for Windows** | 版本控制工具 | 自动获取（GitHub Releases） | 系统安装版 | `C:\Program Files\Git` |
| **Visual Studio Code** | 代码编辑器 | 自动获取（官方重定向） | 系统安装版 | `C:\Program Files\Microsoft VS Code` |
| **MySQL Server (LTS)** | 关系型数据库 | LTS 固定版（官方 CDN 直链） | 系统安装版 | `C:\Program Files\MySQL\MySQL Server 9.7` |
| **TraeCode** | AI 编程助手 | 自动获取（官方重定向） | 用户安装版 | `%LOCALAPPDATA%\Programs\Trae CN` |
| **TraeWork** | AI 工作助手 | 自动获取（官方重定向） | 用户安装版 | `%LOCALAPPDATA%\Programs\TRAE SOLO CN` |
| **Everything** | 文件秒搜工具（voidtools） | 自动获取（官网页面解析） | 系统安装版 | `C:\Program Files\Everything` |
| **Watt Toolkit** | 网络加速工具箱（原 Steam++） | 固定稳定版 3.1.0 | 系统安装版 | `C:\Program Files\Steam++`（可自定义） |

> Trae 系列为 Electron 应用，官方仅提供用户安装版（安装到用户目录），支持静默自动更新。

## 环境要求

- **操作系统**：仅经过 Windows 10 / 11 (x64) 测试并验证；其他系统未测试、不保证可用
- **运行平台**：WorkBuddy、TraeWork（TRAE 产品内的技能机制）

## 使用方法

在 WorkBuddy / TraeWork 中向 AI 描述你的需求即可自动调用本技能，例如：

- "安装常用软件"
- "一键安装 Chrome、Node.js、Git"
- "帮我配置开发环境"

技能会按以下流程执行：

1. **扫描已安装软件**，列出待装清单
2. **询问安装位置**（默认系统盘 `C:\Program Files` 或你指定的目录）
3. **自动获取最新版本**并下载
4. **安装**（系统安装版静默安装；不支持静默的软件启动图形向导，需手动下一步）
5. **验证安装结果**，输出报告

### 安装后的特殊步骤

- **MySQL**：MSI 仅安装 Server 本体，需手动运行配套 **MySQL Configurator** 完成实例配置。新手请对照 [《MySQL 配置向导小白指南》](./docs/mysql-config-guide.md) 操作。
- **Watt Toolkit**：官方安装器不支持静默安装，技能会启动图形向导，请对照 [《Watt Toolkit 安装指引》](./docs/watt-toolkit-install-guide.md) 手动完成。网络加速功能无需登录即可使用（部分高级功能才需登录）。

## 自动版本检测策略

| 策略类型 | 说明 | 适用软件 |
|----------|------|----------|
| `fixed-url` | 下载链接本身就是最新版地址（重定向或固定最新包） | Chrome、Visual Studio Code、TraeCode、TraeWork |
| `nodejs-api` | 通过 Node.js 官方 API 查询最新 LTS 版本 | Node.js |
| `github-release` | 通过 GitHub Releases API 查询最新发布版本 | Git for Windows |
| `page-regex` | 解析官网下载页，正则提取最新版本号后拼接下载地址 | Everything |

> **MySQL 特别说明**：MySQL 8.0 后官网停止提供 MySQL Installer，本技能使用官方独立的 MySQL Community Server MSI 直链（固定官方最新 LTS 9.7 版；因 CDN 主版本目录无法由完整版本号推导，采用**手动更新**而非自动检测）。

## 目录结构

```
it-software-installer/
├── SKILL.md                      # 技能定义与使用说明
├── software-config.json          # 软件列表与安装配置（可自定义）
├── CHANGELOG.md                  # 更新日志
├── README.md                     # 本文件
├── LICENSE                       # 开源许可（MIT）
└── docs/
    ├── mysql-config-guide.md     # MySQL 配置向导小白指南
    └── watt-toolkit-install-guide.md  # Watt Toolkit 安装指引
```

## 配置说明

配置文件 `software-config.json` 结构：

```json
{
  "maxConcurrent": 3,
  "networkMode": "auto",
  "software": []
}
```

每个软件项（`software[].`）主要字段：

| 字段 | 类型 | 说明 |
|------|------|------|
| `id` | string | 唯一标识 |
| `name` | string | 软件显示名称 |
| `version` | string | 版本策略：`latest` / `auto-lts` / `auto-latest` / 具体版本号 |
| `versionSource` | object | 版本来源配置（见上表策略） |
| `downloadUrl` | string | 默认下载地址（版本检测失败时回退） |
| `installerType` | string | `msi` / `exe` |
| `installArgs` | array | 静默安装参数 |
| `checkPaths` | array | 检测是否已安装的路径（支持环境变量如 `%LOCALAPPDATA%`） |
| `requiresAdmin` | boolean | 是否需要管理员权限 |
| `installScope` | string | `system`（系统安装版）/ `user`（用户安装版） |

**自定义**：按上述结构在 `software[].` 中增删对象即可扩展软件列表。

## 更新日志

见 [CHANGELOG.md](./CHANGELOG.md)。

## 开源许可

本项目基于 [MIT License](./LICENSE) 开源。

## 贡献

欢迎提交 Issue 与 Pull Request。新增软件时请注意：

1. 优先使用**系统安装版**，仅当官方只提供用户安装版时才用 `user`
2. 优先配置**自动版本检测**；官方无 API/重定向时才用固定版本并注明原因
3. 如实核对 `checkPaths`（注意国内版/国际版实际安装路径差异）

## 免责声明

本技能仅负责调用各软件官方的安装包与安装流程，各软件的版权归其各自所有方。安装过程依赖网络环境，若个别软件下载失败，请检查网络（如 GitHub 相关软件可借助 Watt Toolkit 加速）。