---
title: "Gemini 挂载 GitHub MCP Server 实战指南：读取私有仓库与自动提交 PR"
date: 2026-10-14T14:55:00+08:00
draft: false
tags: ["AI", "Gemini", "GitHub", "MCP", "工具配置"]
categories: ["tech"]
summary: "通过 Custom Connected Apps 接入 GitHub 官方远程 MCP 服务，让 Gemini 网页端能够实时读取私有仓库、查看与审查 Pull Requests，并直接创建分支、修改代码与自动提交 PR。"
---

通过 **Custom Connected Apps** 接入 GitHub 官方远程 MCP（Model Context Protocol）服务，可以让 Gemini 网页端拥有直接操作 GitHub 的强大能力——不仅能实时读取私有仓库和分支代码、查询与审查 Pull Requests，还能直接在仓库中创建分支、提交修改并自动发起 PR。

---

## 一、 为什么需要挂载 Custom GitHub MCP？

在 Gemini 网页端中，默认的内置 GitHub 扩展与通过自定义 MCP 挂载的官方服务在权限和能力上有本质区别：

| 功能维度 | 内置 GitHub Connected App | Custom App (挂载 GitHub MCP Server) |
| :--- | :--- | :--- |
| **私有仓库代码读取** | 仅支持手动点击 `+` 导入静态快照 | **支持按需实时读取任意文件、目录与分支** |
| **读取 PR 列表与评论 (`/pulls`)** | 不支持 | **支持实时查询 PR 状态、Diff 与 Review 评论** |
| **创建分支与修改代码** | 不支持（纯只读） | **支持 (`create_branch` / `create_or_update_file`)** |
| **自动提交 Pull Request** | 不支持 | **支持 (`create_pull_request` 等 46 个工具)** |

---

## 二、 开启前置条件（5 项环境检查）

Google 对 Gemini 网页端的 **Custom Connected Apps** 设有严格的环境门槛，必须同时满足以下 5 项条件，设置菜单中才会出现添加入口：

1. **个人 Google 账号：** 必须使用个人账号登录（公司或学校的 Google Workspace 账号暂不开放自定义 MCP 入口）。
2. **订阅等级：** 需具备 Gemini Spark 权限（面向 Google AI Pro 或 AI Ultra 订阅用户开放）。
3. **网络与年龄：** 账号持有人满 18 周岁，且当前访问 IP 位于 **美国（US）**。
4. **界面语言：** Google 账号及 Gemini 网页语言需设置为 **English（英文）**。
5. **活动记录开启：** 必须在设置中开启 **Keep Activity（保留活动记录）**，关闭状态下无法连接任何自定义应用。

---

## 三、 手把手配置流程（四步跑通）

### 步骤 1：在 Gemini 中填入 MCP 地址并复制回调 URI

1. 在电脑浏览器打开 `https://gemini.google.com`。
2. 点击左下角 **Settings & help** → **Connected Apps**（或在 **Personal Intelligence** 下找到 **Connected Apps**）。
3. 滚动到页面底部 **Custom apps** 区域，点击 **Add a custom app**。
4. 在 **MCP Server URL** 输入框中填入 GitHub 官方远程 MCP 地址：
   ```text
   https://api.githubcopilot.com/mcp/
   ```
5. 此时界面会提示：
   > `This server doesn't support automatic registration. Enter your OAuth client ID and client secret to connect.`
6. 点击 **Additional settings** 展开高级选项，点击下方的 **Copy redirect URI** 按钮复制 Google 专属回调地址，并**保持该弹窗不要关闭**。

### 步骤 2：在 GitHub 创建 OAuth App 获取 Client ID 与 Secret

1. 新开浏览器标签页，访问 GitHub 开发者设置：[https://github.com/settings/developers](https://github.com/settings/developers)。
2. 选择左侧 **OAuth Apps** → 点击右上角 **New OAuth App**，进入 *Register a new OAuth app* 页面。
3. 按以下说明填写表单：
   - **Application name：** 自定义名称（例如 `Gemini-GitHub-MCP`）。
   - **Homepage URL：** 填写 `https://gemini.google.com`。
   - **Redirect URI：** 粘贴你在第 1 步点击 *Copy redirect URI* 复制出来的地址。
   - **Expire user access tokens（可选建议）：** 建议**取消勾选**，避免访问令牌定期过期导致频繁重连。
4. 点击底部的绿色按钮 **Register application**。
5. 在跳转后的应用详情页中，复制 **Client ID**，并点击 **Generate a new client secret** 生成并复制 **Client secret**。

### 步骤 3：回填凭证并完成仓库与组织权限授权

1. 切回 Gemini 的 **Connect to an MCP server** 弹窗，将复制好的 **Client ID** 和 **Client secret** 分别粘贴到对应输入框中，点击右下角 **Next**。
2. 在弹出的 GitHub OAuth 授权确认页（*Authorize ...*）中检查权限：
   - **Repositories (Public and private)：** 默认已包含完整的公开与私有仓库读写权限（即 `repo` scope），支持读取私有代码、创建分支、修改文件及提交 PR。
   - **Organization access（按需授权）：** 如果目标仓库在个人账号下，底部组织列表无需操作；如果希望 Gemini 操作某个组织（Organization）下的私有仓库，请点击对应组织右侧的 **Grant** 按钮。
3. 滚动到页面底部，点击绿色 **Authorize** 按钮完成绑定。

### 步骤 4：验证 46 个官方 Action 是否同步成功

授权跳回 Gemini 后，在 **Connected Apps** 列表中点击刚创建的 **GitHub MCP Server**，确认以下状态：

- 右上角按钮显示为蓝色的 **Disconnect**，且底部 **Last synced** 显示最新同步时间戳。
- **Available actions** 显示 **See all (46)**，列表内包含带勾选标记的：
  `add_comment_to_pending_review`、`add_issue_comment`、`add_reply_to_pull_request_comment`、`create_branch`、`create_or_update_file`、`create_pull_request` 等核心工具。

---

## 四、 常见避坑指南：为什么显示已连接却提示“无法直接连接”？

> **典型现象**  
> 明明设置页里已经同步了 46 个工具，但在聊天框发送 `@GitHub MCP Server 帮我查看 xxx 当前的 PR 列表` 时，Gemini 却回复：“当前环境无法直接连接 @GitHub MCP Server 或实时访问 GitHub 接口”。

### 坑点 1：直接粘贴了纯文本而非选中应用标签

由于 Handle 名称 `@GitHub MCP Server` 中间带有空格，直接复制粘贴这串字符只会被当成普通文本，不会挂载底层工具。  
**正确做法：** 在输入框敲入 `@` 或 `@GitHub`，用鼠标或回车**从弹出菜单中点击选中**，使其变成高亮的胶囊标签（如 `@GitHub MCP Server`）。

### 坑点 2：未刷新页面或仍在旧对话中提问

刚完成连接时，已打开的旧标签页尚未加载新工具列表；且旧对话中如果之前已经有过“无法访问 GitHub”的回复，模型会受上下文惯性影响继续拒绝。  
**正确做法：** 按 `Cmd + R` / `F5` 刷新网页，并点击 **New chat** 开启全新对话。

---

## 五、 常用实战指令模板（读取代码 / 审查 PR / 自动提 PR）

由于底层 46 个工具的名称与参数描述均为英文（如 `owner`、`repo`、`create_branch`、`create_pull_request`），在选中 `@GitHub MCP Server` 标签后，明确写出仓库的 `owner` 和 `repo` 能让工具路由更加精准：

### 场景 1：读取私有仓库当前的 PR 列表

```text
@GitHub MCP Server 请调用工具查看 owner 为 "your-username"、repo 为 "your-repo" 的开启状态 Pull Requests 列表，并用中文汇总每个 PR 的标题、编号和作者。
```

### 场景 2：审查指定编号的 PR 代码改动并发表评论

```text
@GitHub MCP Server 请读取仓库 your-username/your-repo 中第 #1 号 PR 的详细文件改动（Diff），帮我做一次 Code Review；如果发现潜在问题，请调用 add_issue_comment 将审查建议评论到该 PR 下。
```

### 场景 3：自动创建分支、修改仓库代码并提交 Pull Request

```text
@GitHub MCP Server 请对私有仓库 owner="your-username", repo="your-repo" 执行以下操作：
1. 调用 create_branch 基于 main 分支创建一个新分支 "docs/update-readme"；
2. 读取仓库根目录下的 README.md 文件内容，在末尾补充一段“开发环境配置说明”，并调用 create_or_update_file 提交到 "docs/update-readme" 分支；
3. 调用 create_pull_request 将 "docs/update-readme" 向 "main" 分支发起 Pull Request，标题为 "docs: 补充开发环境配置说明"。
```

> **安全确认提示**  
> 首次调用外部 MCP 工具执行读取或写入操作时，Gemini 聊天窗口会弹出权限确认卡片，询问是否允许将数据发送至 `GitHub MCP Server`，点击 **Allow / Confirm** 即可自动执行。
