# HTML 一键部署教程（htmlcode.fun）

> 适用场景：把**单个自包含 HTML 文件**（小工具、落地页、报表、活动页等，无需后端/构建）发布成公网链接，方便团队分享。
> 本文档基于已部署的「天合民宿记账」项目（`code = muji-receipt`）整理，团队同事可直接套用。

---

## 一、当前项目部署在哪里

| 用途 | 地址 |
|---|---|
| **主链接（对外分享用）** | https://www.htmlcode.fun/s/muji-receipt |
| 指定版本链接（例：v51） | https://www.htmlcode.fun/s/muji-receipt/v/51 |
| 详情/版本历史页 | 部署命令返回 `detailUrl` |

主链接是稳定的，刷新即最新版（需按下方「关键一步」操作）。

---

## 二、htmlcode.fun 是什么

- 免费的公网 **单文件 HTML 托管**：把一份 `.html` 传上去就得到一个可访问链接，无需服务器、无需构建。
- 每次部署是一个「版本」，保留历史，可回滚。
- 适合：小工具、落地页、报表、演示页、二维码活动页。
- **不适合**：React/Vue 等多文件工程、需要数据库/登录/密钥的后端应用、大型生产站点（那种请用 Vercel/Netlify/GitHub Pages）。

---

## 三、准备工作

**方式 A：脚本部署（推荐，可追溯、可批量）**
1. 安装 Python 3（已装可跳过）。
2. 拿到部署脚本 `htmlcode_deploy.py`：
   - 它随 WorkBuddy 的「HTML Deploy」技能一起提供，路径在  
     `C:\Users\1\.workbuddy\skills\html-deploy\scripts\htmlcode_deploy.py`（Mac/Linux 类似 `~/.workbuddy/skills/html-deploy/scripts/`）。
   - 团队同事也可**只复制这一个 .py 文件**到任意文件夹即可用，不必装整个技能。

**方式 B：网页直接发布（适合不装环境的同事）**
- 打开 https://www.htmlcode.fun/s/htmlcode-fun-guide ，按站内指引把 HTML 粘贴/上传即可发布，无需安装。脚本方式更利于版本管理，按需选择。

---

## 四、脚本部署命令（核心）

> 约定：`<文件>` = 你的 HTML 路径；`<code>` = 你自定义的英文短码（每个项目一个，全局唯一，如 `muji-receipt`、`team-tools`）。

### 1) 首次部署（新建一个项目）
```bash
python htmlcode_deploy.py deploy <文件>.html --code <code> --title "项目标题" --description "一句话描述，最多240字"
```
返回 `url`（主链接）、`versionUrl`、`detailUrl`、`qrCode`。

### 2) 后续更新（追加新版本，保留历史）
```bash
python htmlcode_deploy.py append <code> <文件>.html --description "本次改了什么"
```

### 3) ⚠️ 关键一步：把主链接切到最新版
htmlcode.fun 的主链接默认按「点赞数」策略，**不会自动指向最新版本**。所以每次 `append` 后，主链接可能还停在旧版——必须手动切：
```bash
python htmlcode_deploy.py current <code> <版本号>
```
例：`python htmlcode_deploy.py current muji-receipt 51`
> 不确定版本号时，先 `python htmlcode_deploy.py versions <code>` 看最新 `versionNumber`。

### 4) 查看 / 回滚
```bash
python htmlcode_deploy.py versions <code>            # 列出版本与点赞数
python htmlcode_deploy.py get <code> --version 3 --output v3.html   # 取回某版源码
python htmlcode_deploy.py status <code> 2 inactive   # 下线某版本
python htmlcode_deploy.py status <code> 2 active     # 重新上线
```

---

## 五、团队常见坑（务必看）

1. **主链接不自动更新（最高频）**：`append` 后记得跑 `current <code> <版本号>`，否则同事打开主链接看到的是旧版。（本项目 v46→v47、v50→v51 都踩过这个。）
2. **10 秒冷却**：连续部署会返回 `429 COOLDOWN_ACTIVE`，等约 10 秒再重试即可（命令返回 `retryAfterSeconds`）。
3. **别覆盖被点赞的版本**：有人点赞过的版本会被锁定，不能覆盖/删除。需要改动时**追加新版本**（`append`），不要 `overwrite`。
4. **description 必填**：每次部署都要带 `--description`，一句中文简述，≤240 字。
5. **单文件限制**：HTML 尽量内联 CSS/JS；别放超大 base64 图片；单文件建议 < 1 MB。
6. **稳定的 code**：给每个项目固定一个 `code`（如 `muji-receipt`），别每天换新码，方便团队记忆和回滚。

---

## 六、一行速查

| 目的 | 命令 |
|---|---|
| 首次发布 | `python htmlcode_deploy.py deploy 文件.html --code 项目码 --title 标题 --description 描述` |
| 更新 | `python htmlcode_deploy.py append 项目码 文件.html --description 描述` |
| 主链接指向最新 | `python htmlcode_deploy.py current 项目码 版本号` |
| 看版本列表 | `python htmlcode_deploy.py versions 项目码` |
| 取回某版 | `python htmlcode_deploy.py get 项目码 --version N --output out.html` |

---

## 七、给团队的话

- 每个人部署**自己的** HTML，用**各自的项目码**，互不干扰。
- 发布后把主链接 + `current` 切版这两步都做了，再发给同事，避免大家看到旧版。
- 需要版本可追溯、可回滚的，用脚本；只想快速发个页子的，用网页粘贴即可。
