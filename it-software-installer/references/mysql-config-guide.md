# MySQL 配置向导小白指南（MySQL Configurator）

> 适用场景：通过本技能安装 MySQL Server 后，首次运行配套的 MySQL Configurator 完成实例配置。
> 本文针对 MySQL Server 9.7 的向导界面编写，也基本适用于 8.x 系列。
> 面向不会数据库配置的新手，逐步骤说明：每个选项是什么意思、该选什么、为什么。

## 快速一览：最终目标

| 项目 | 建议值 |
|------|--------|
| 配置类型 | Development Computer（开发电脑） |
| 端口 | 3306（默认，不要改） |
| root 密码 | 自己设置并**务必记牢** |
| Windows 服务名 | MySQL97（默认） |
| 示例数据库 | 不安装（保持干净） |
| 高级/日志选项 | 不勾选（跳过复杂步骤） |

---

## 向导完整步骤（共 11 步）

```
Welcome → Data Directory → Type and Networking → Accounts and Roles
→ Windows Service → Server File Permissions → (Logging / Advanced)
→ Sample Databases → Apply Configuration → Configuration Complete
```

> 说明：勾选 "Show Advanced and Logging Options" 时才会多出 Logging Options 和 Advanced Options 两步；小白不勾，即跳过。

---

## 第 1 步 Welcome（欢迎页）

纯介绍页，**不用改任何设置，直接点 `Next`**。

---

## 第 2 步 Data Directory（数据目录）

这里决定"数据库文件存在硬盘的哪个位置"。

- 默认路径：`C:\ProgramData\MySQL\MySQL Server 9.7\Data`
- **小白直接保持默认，点 `Next`**。
- 只有 C 盘空间紧张时，才改到其他盘（如 `D:\ProgramData\MySQL\...`）。
- **注意**：配置过程中不要手动移动或删改这个文件夹，否则数据库会损坏。

---

## 第 3 步 Type and Networking（配置类型和网络）

分三块：

### ① Config Type（配置类型）——决定 MySQL 占多少内存
| 选项 | 含义 | 推荐 |
|------|------|------|
| **Development Computer**（开发电脑） | 机器还要跑很多其他软件，MySQL 只用最小内存 | ✅ 小白选这个 |
| Server Computer（服务器） | 机器运行多个服务器程序，MySQL 用中等内存 | |
| Dedicated Computer（专用数据库机） | 机器只跑数据库，MySQL 用全部可用内存 | |

### ② Connectivity（连接方式）
| 选项 | 说明 | 推荐 |
|------|------|------|
| **TCP/IP** | 用网络方式连数据库的开关，绝大多数程序靠它连接 | ✅ 必须勾 |
| **Port（端口）** | 默认 `3306` | ✅ 保持 3306 不要改 |
| **Open Windows firewall port** | 自动放行防火墙端口，否则其他程序可能连不上 | ✅ 保持勾选 |
| Named Pipe | 特殊场景才用 | ❌ 不勾 |
| Shared Memory | 特殊场景才用 | ❌ 不勾 |

### ③ Show Advanced and Logging Options（显示高级和日志选项）
- **保持不勾**。勾了会多出两个复杂页面，小白不需要。

**这一页全部保持默认即可，点 `Next`。**

---

## 第 4 步 Accounts and Roles（账户和 root 密码）★★★最重要

**root 密码是真正的重点**：

- **MySQL Root Password（root 密码）**：输入两遍，必须完全一致。
  - root = 数据库的"超级管理员账号"，权限最高。
  - **务必记牢这个密码**，以后连数据库都要用它。
  - 建议用「大写 + 小写 + 数字 + 符号」的强密码，如 `Abc123456!`。
- **Add User（添加新用户）**：小白不用管，跳过。
- 认证插件（如 caching_sha2_password）：保持默认，不用动。

**设好密码后点 `Next`。**

---

## 第 5 步 Windows Service（Windows 服务）

把 MySQL 注册成"开机自动在后台运行的服务"，这样无需手动启动，开机就能用。

| 选项 | 推荐 | 说明 |
|------|------|------|
| Configure MySQL Server as a Windows Service | ✅ 勾选 | 注册为 Windows 服务 |
| Windows Service Name | 保持默认 `MySQL97` | 服务名，日后查状态用 |
| Start the MySQL Server at System Startup | ✅ 勾选 | 开机自动启动 |
| Run Windows Service as | Standard System Account（默认） | 运行账户 |

**全部保持默认，点 `Next`。**

---

## 第 6 步 Server File Permissions（数据目录文件权限）

给 MySQL 数据文件夹设置读写权限。**小白直接点 `Next`，保持默认**。

---

## 第 7-8 步 Logging Options & Advanced Options（仅勾选高级选项才出现）

因建议不勾高级选项，这两页不会出现，直接跳过。如万一勾选：
- Logging：错误日志默认开即可，一般查询 / 慢查询日志小白不开。
- Advanced：缓冲池等参数**全部不动，保持默认**。

---

## 第 9 步 Sample Databases（示例数据库）

问是否要装自带的教学示例数据（world、sakila 等）。

- **小白建议不勾**，保持干净。官方教程如提示缺示例库再回来装。
- 点 `Next`。

---

## 第 10 步 Apply Configuration（应用配置）★真正执行的步骤

1. 页面列出前面选好的各项配置。
2. 点左下角 **`Execute`** 按钮（不是 Next）。
3. 它开始执行：写配置文件 → 更新防火墙 → 调整服务 → 初始化数据库 → 设置权限 → 启动服务 → 应用安全设置 → 更新开始菜单。
4. 等进度条跑完，**所有项变绿色 ✓**，并提示 "The configuration was successful. Click **Next** to continue."
5. **此时点 `Next`**（注意：不是 Finish，Finish 在最后一步）。

> 常见错误：root 密码两遍不一致（返回第 4 步重设）、或端口 3306 被占用（如已装 MariaDB 或其他 MySQL）。

---

## 第 11 步 Configuration Complete（配置完成）

- 这一步显示安装完成提示，**点 `Finish`** 关闭窗口。
- 到这里 MySQL 配置就全部完成了。

---

## 配置完成后如何验证

关掉向导后，在 PowerShell 或 CMD 里执行：

```powershell
# 查看服务状态（应为 Running / 正在运行）
Get-Service MySQL97

# 用 root 登录测试（会提示输入密码）
mysql -u root -p
```

第一条看服务是否在运行；第二条输入第 4 步设的密码，进入 `mysql>` 提示符即成功。

---

## 常见问题（FAQ）

**Q1：提示端口 3306 被占用怎么办？**
说明已有程序或用例占用了该端口（常见的是已装 MariaDB、另一个 MySQL）。要么停掉占用方，要么在向导第 3 步换一个端口（改后要记住，连接时也要用新端口）。

**Q2：root 密码忘了怎么办？**
比较麻烦，需要以"跳过权限表"的方式重置，建议到这一步时用密码管理器把密码记下来。

**Q3：我可以装 Sample Databases 吗？**
可以，但那会往数据库里塞进一些教学示例数据。正常开发用不到，建议不装。

**Q4：装完完全是英文界面，看不懂？**
本指南对应的就是每个英文标题的具体含义，按步骤对照即可。
