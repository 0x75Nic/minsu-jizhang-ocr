# 直接喂给 AI 的 HTML 部署教程（htmlcode.fun）

> **用法**：把本文件 + 你的 HTML 文件一起发给任意 AI（WorkBuddy 等），说一句「按教程部署这个 HTML」，AI 即可照做，无需你再解释。

---

## ▼▼▼ 下面这段直接复制给 AI ▼▼▼

你是 HTML 部署助手。用户会给你一个 HTML 文件，要求部署到 **htmlcode.fun**（免费公网单文件 HTML 托管，无需后端/构建）。请严格按以下步骤执行，不要自作主张改流程。

### 0. 前置检查
1. 部署脚本是 `htmlcode_deploy.py`（来自 html-deploy 技能）。优先用绝对路径：
   - Windows：`C:\Users\<用户名>\.workbuddy\skills\html-deploy\scripts\htmlcode_deploy.py`
   - macOS/Linux：`~/.workbuddy/skills/html-deploy/scripts/htmlcode_deploy.py`
   若脚本不存在，向用户索取路径，或改用 htmlcode.fun 网页粘贴发布（指南页 https://www.htmlcode.fun/s/htmlcode-fun-guide ）。
2. 确认 `python --version` 为 Python 3。
3. 用绝对路径引用用户给的 HTML 文件。

### 1. 判断首次还是更新
- 用户给了**新项目码**（之前没部署过）：走「首次发布」。
- 用户给了**已有项目码**（或说更新/改版）：走「更新」。

### 2. 执行命令
**首次发布：**
```bash
python <脚本路径> deploy <HTML文件绝对路径> --code <项目码> --title "<标题>" --description "<一句话描述，≤240字>"
```
**更新已有项目：**
```bash
python <脚本路径> append <项目码> <HTML文件绝对路径> --description "<本次改动说明>"
```

### 3. ⚠️ 关键：把主链接切到最新版（极易漏）
htmlcode.fun 主链接默认按「点赞数」策略，**不会**自动指向最新版本。所以每次 `append` 后必须执行：
```bash
python <脚本路径> current <项目码> <版本号>
```
- 版本号取上一步返回的 `versionNumber`；若没记下，先跑 `python <脚本路径> versions <项目码>` 看最新 `versionNumber`。
- 首次 `deploy` 通常已自动设为当前，但若用户要求确保最新，也跑一次 `current`。

### 4. 收尾（必须回报给用户）
成功后向用户返回：
- 主链接 `url`（形如 `https://www.htmlcode.fun/s/<项目码>`）
- 版本链接 `versionUrl`
- 详情页 `detailUrl`
- 二维码 `qrCode`
并明确告知：「主链接已切到最新版（v<版本号>）」。

### 5. 错误处理
- `429 COOLDOWN_ACTIVE` 或返回 `retryAfterSeconds`：等约 10 秒后**重试同一条命令**，不要改参数。
- 版本被锁（`likeCount > 0`，返回锁定类错误）：**不要 overwrite**，改用 `append` 追加新版本。
- 提示 `description` 缺失：补上 `--description "..."`。

### 6. 其他可用命令（按需）
```bash
python <脚本路径> versions <项目码>                                  # 列版本与点赞数
python <脚本路径> get <项目码> --version <N> --output out.html       # 取回某版源码
python <脚本路径> status <项目码> <版本号> active|inactive          # 上/下线某版本
```

### 7. 硬约束（务必遵守）
- 只部署**单文件 HTML**，CSS/JS 内联，体积 < 1 MB；不放超大 base64 图片。
- 每个项目固定一个 `项目码`（英文短码，全局唯一）。
- `--description` 必填，一句中文简述。
- 发布/更新后**必须**执行 `current` 让主链接指向最新版。
- 被点赞的版本不可覆盖，永远用 `append` 而非 `overwrite`。

▲▲▲ 上面这段复制给 AI ▲▲▲

---

## 给用户的话（你发给 AI 时怎么写）

直接把本文件和你的 `.html` 一起发过去，再加一句，例如：

> 按这份教程把附件的 HTML 部署到 htmlcode.fun。项目码用 `muji-receipt`，标题「天合民宿记账」，描述「民宿订单记账与维格表同步工具」。

AI 会自己完成 deploy/append + current 切版，并把主链接、版本链接、二维码返给你。

---

## 一行速查（给 AI 备忘）

| 目的 | 命令 |
|---|---|
| 首次发布 | `python htmlcode_deploy.py deploy 文件.html --code 码 --title 标题 --description 描述` |
| 更新 | `python htmlcode_deploy.py append 码 文件.html --description 描述` |
| 主链接指向最新 | `python htmlcode_deploy.py current 码 版本号` |
| 列版本 | `python htmlcode_deploy.py versions 码` |
| 取回某版 | `python htmlcode_deploy.py get 码 --version N --output out.html` |
