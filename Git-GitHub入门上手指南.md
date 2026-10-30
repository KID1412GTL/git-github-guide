# Git / GitHub 入门上手指南

> 面向完全新手的实操手册。跟着做一遍，你就能独立「建仓库 → 写代码 → 备份到 GitHub → 参与协作」。
> 配套文件：`GitHub-Hosts-2026-10-29.txt`（解决打不开 GitHub 的问题，先看第 8 节）。

---

## 0. 先记住一句话

- **Git** = 装在你电脑上的版本控制软件（记录文件每次改动，本地就能用）。
- **GitHub** = 把 Git 仓库存到云端的网站（备份 + 协作 + 找开源项目）。
- 关系：本地用 Git 干活，用 `push` 把成果传到 GitHub，`pull` 把别人的更新拉回来。

---

## 1. 安装 Git

### Windows
- 下载安装包：https://git-scm.com/download/win （一路 Next 即可）
- 安装后会有 `Git Bash`，后面命令都在 Git Bash 里敲。

### macOS
```bash
# 已装 Homebrew 直接：
brew install git
# 或装 Xcode 命令行工具：
xcode-select --install
```

### Linux (Debian/Ubuntu)
```bash
sudo apt update && sudo apt install git -y
```

### 验证安装
```bash
git --version
```
能看到版本号（如 `git version 2.x`）就成功了。

---

## 2. 第一次配置 Git（只需一次）

告诉 Git 你是谁，提交的记录才会带上你的名字。

```bash
git config --global user.name  "你的名字"
git config --global user.email "你的邮箱@example.com"
```

查看配置：
```bash
git config --list
```

---

## 3. 注册 GitHub 账号

1. 打开 https://github.com （打不开就先看本文第 8 节配 hosts）。
2. 点 Sign up，用邮箱注册，验证邮箱。
3. 免费账号就够用，能建「公开仓库」无限量。

---

## 4. 让 GitHub 认识你的电脑（认证）

> 重要：GitHub 早已**不支持用账号密码**登录 Git 了。必须用下面两种方式之一。

### 方式 A：HTTPS + 个人访问令牌（PAT，最简单，推荐新手）
1. GitHub 右上角头像 → **Settings** → 左栏 **Developer settings** → **Personal access tokens** → **Tokens (classic)**。
2. 点 **Generate new token (classic)**，勾选 `repo` 权限，设个过期时间，生成。
3. **立刻复制那串 token**（只显示一次！）。
4. 以后 `git push` 要输密码时，粘贴这串 token 当密码即可。
   - 嫌每次输入麻烦，可用 Git 凭证管理器缓存：`git config --global credential.helper store`

### 方式 B：SSH 密钥（一劳永逸，略进阶）
```bash
# 生成密钥（一路回车）
ssh-keygen -t ed25519 -C "你的邮箱@example.com"
# 把公钥内容复制到 GitHub：头像 → Settings → SSH and GPG keys → New SSH key
cat ~/.ssh/id_ed25519.pub
```
之后用 `git@github.com:用户名/仓库名.git` 这种地址就无需再输密码。

---

## 5. 第一次：本地建仓库并推到 GitHub

**场景**：你本地有个文件夹想存到 GitHub。

```bash
# 1) 进入你的项目文件夹
cd /path/to/your/project

# 2) 初始化 Git 仓库
git init

# 3) 把文件加入「暂存区」
git add .

# 4) 提交（存档），-m 后面是这次改动的说明
git commit -m "第一次提交：项目初始化"

# 5) 在 GitHub 网页上「New repository」建一个空仓库（别勾 README）
#    建好后复制它的地址，比如 https://github.com/你的名/项目名.git

# 6) 关联远程仓库（origin 是默认远程名）
git remote add origin https://github.com/你的名/项目名.git

# 7) 推送到 GitHub（第一次需指定上游分支）
git push -u origin main
```

刷新 GitHub 页面，就能看到你的代码了。🎉

> 注意分支名：新版 Git 默认分支叫 `main`（旧版叫 `master`）。如果本地是 `master`，把上面 `main` 换成 `master` 即可，或统一改名：`git branch -M main`。

---

## 6. 日常最常用的命令（背下这 6 个）

| 命令 | 作用 |
|------|------|
| `git status` | 看当前改了哪些文件、处于什么状态 |
| `git add 文件名` 或 `git add .` | 把改动放进暂存区（`.` 表示全部） |
| `git commit -m "说明"` | 把暂存区存档成一个版本 |
| `git push` | 把本地提交推到 GitHub |
| `git pull` | 把 GitHub 上的更新拉到本地 |
| `git clone 仓库地址` | 把别人的仓库完整下载到本地 |

**典型一天的工作流：**
```bash
git pull                 # 早上先拉最新
# ... 写代码 / 改文件 ...
git add .
git commit -m "修复登录bug"
git push                 # 晚上推上去
```

---

## 7. 多人协作：分支与 Pull Request

想给别人的项目贡献代码，或自己开发新功能不想搞乱主线：

```bash
# 1) 先 fork 别人的仓库（网页上点 Fork），然后 clone 你自己的副本
git clone https://github.com/你的名/别人的项目.git

# 2) 建一个功能分支
git checkout -b feature-login

# 3) 改完提交并推送
git add . && git commit -m "实现登录功能"
git push -u origin feature-login

# 4) 回到 GitHub 网页，点「New Pull Request」
#    对方审核通过后，你的代码就被合并进主项目了。
```

自己用也可以这么玩：`main` 放稳定版，新功能开分支，测好再合并。

---

## 8. 打不开 GitHub？按这个顺序解决

### 方案 1：改 hosts（绕过 DNS 污染，解决「打不开 / 头像图片裂图」）
1. 打开本目录的 `GitHub-Hosts-2026-10-29.txt`。
2. 把 `# GitHub Hosts Start` 到 `# GitHub Hosts End` 之间的内容，复制到系统 hosts 文件末尾：
   - Windows：`C:\Windows\System32\drivers\etc\hosts`（用记事本**管理员身份**打开）
   - macOS / Linux：`/etc/hosts`（终端 `sudo vi /etc/hosts`）
3. 刷新 DNS：
   - Windows（CMD）：`ipconfig /flushdns`
   - macOS：`sudo killall -HUP mDNSResponder`
   - Linux：`sudo systemctl restart NetworkManager`
4. 重开浏览器访问 github.com。

> ⚠️ hosts 只解决「域名被解析到错误 IP」这类问题。如果 IP 本身被网络层阻断或整体限速，hosts 无效——那是方案 3 的事。

### 方案 2：用自动同步工具，别手改
IP 经常变，手动维护很烦。推荐：
- **fetch-github-hosts**（桌面端/命令行，自动同步）：https://hosts.gitcdn.top/
- **SwitchHosts** 软件：添加远程规则，URL 填 `https://raw.githubusercontent.com/maxiaof/github-hosts/master/hosts`，设每小时更新。

### 方案 3：代理 / VPN
如果你重度依赖 GitHub（天天 `clone` 大项目、用 Copilot、Actions），hosts 不够用时，通过你自己的合法合规渠道使用代理通常是最稳的。

### 方案 4：国内平替 —— Gitee（码云）
https://gitee.com —— 国内版 GitHub，访问快、Git 命令完全一样。适合「只是想存代码、找国内开源项目」的需求。可以把同一个仓库同时推到 GitHub 和 Gitee 做双备份。

---

## 9. 新手最常踩的坑

1. **push 提示要密码却总错** → 你还在用账号密码。请用第 4 节的 PAT（令牌）当密码。
2. **fatal: remote origin already exists** → 已经关联过远程。先 `git remote remove origin` 再重新 `add`，或直接改地址：`git remote set-url origin 新地址`。
3. **push 被拒（non-fast-forward）** → 远程有你本地没有的提交。先 `git pull` 再 `git push`。
4. **忘了先 pull 就改了一堆** → 改之前养成 `git pull` 的习惯，能少很多冲突。
5. **不小心把密码/密钥提交了** → 立刻改密码/重置令牌，并用 `git filter-repo` 或 BFG 清理历史，别只删最新提交（历史里还有）。
6. **分支名 main / master 对不上** → 用 `git branch` 看当前分支名，push 时保持一致。

---

## 10. 下一步学什么

- `.gitignore`：告诉 Git 哪些文件别追踪（比如 `node_modules`、`.env` 密钥）。
- `git log` / `git diff`：查看历史、对比改动。
- 分支策略（Git Flow）、`rebase` vs `merge`：团队协作进阶。
- GitHub Issues / Projects / Actions：用 GitHub 做项目管理和自动化。

把这份指南和第 8 节的 hosts 文件配合用，基本就能顺畅开始你的 Git/GitHub 之旅了。
