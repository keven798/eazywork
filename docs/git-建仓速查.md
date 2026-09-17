# Git 建仓与日常操作速查

本机环境已配置完成,以下命令在 Git Bash / CMD / PowerShell 中均可直接使用。

## 0. 本机环境事实

| 项目 | 值 |
| --- | --- |
| git | `C:\Users\shihan.li\AppData\Local\Programs\Git`(2.55.0.windows.5) |
| Git Bash | 同目录下 `git-bash.exe` |
| SSH 私钥 | `C:\Users\shihan.li\.ssh\id_ed25519`(ed25519, 无口令) |
| SSH 配置 | `C:\Users\shihan.li\.ssh\config` |
| GitHub 账号 | `keven798` |
| 提交身份 | `云柯 <3067242378@qq.com>` |
| 远程仓库 | `git@github.com:keven798/eazywork.git` |

环境自检(三条都通即无问题):

```bash
git --version
ssh -T git@github.com      # 期望输出: Hi keven798! ...
git ls-remote origin       # 期望输出: 列出远程分支的 SHA
```

## 1. 创建新仓库 - 完整流程

### 第 1 步 建目录并初始化

```bash
mkdir -p /d/workbuddymemory/新项目
cd /d/workbuddymemory/新项目
git init -b main
```

`-b main` 让初始分支直接叫 `main`;不加会得到 `master`,推上去还得改一次名。

### 第 2 步 放三个基础文件

`.gitignore`(决定什么不进库)、`.gitattributes`(换行符规则)、`README.md`(说明)。
本仓库根目录的三个文件可直接复制当模板:

```bash
cp /d/workbuddymemory/商品计划专员agent/.gitignore .
cp /d/workbuddymemory/商品计划专员agent/.gitattributes .
```

### 第 3 步 首次提交

```bash
git add -A
git commit -m "chore: 初始化仓库"
```

### 第 4 步 在 GitHub 建空仓库

网页右上角 `+` → `New repository`:

- **Repository name**:仓库名
- **可见性**:Private / Public 自选
- **不要勾** `Add a README file` / `Add .gitignore` / `Choose a license`

勾了会生成一条与本地无关的历史,推送会被拒,处理办法见第 5 节。

### 第 5 步 关联远程并推送

```bash
git remote add origin git@github.com:keven798/仓库名.git
git push -u origin main
```

`-u` 把本地 `main` 与 `origin/main` 绑定,之后 `git push` / `git pull` 不用带参数。

### 第 6 步 验证

```bash
git remote -v          # origin 应为 git@ 开头
git status             # 期望: ## main...origin/main
git log --oneline
```

## 2. 日常操作

```bash
git status                 # 永远先看状态
git add -A                 # 暂存全部改动(含新增与删除)
git commit -m "feat: 说明"  # 提交
git push                   # 推送
git pull                   # 拉取
git log --oneline          # 提交历史
git diff                   # 看未暂存的改动
```

## 3. 克隆已有仓库

```bash
git clone git@github.com:keven798/仓库名.git
```

本机已配置 HTTPS → SSH 自动改写,所以下面这条同样能通(底层走 SSH):

```bash
git clone https://github.com/keven798/仓库名.git
```

## 4. 分支

```bash
git switch -c feat/xxx        # 新建并切到分支
git switch main               # 切回主分支
git branch                    # 列出本地分支
git merge feat/xxx            # 在 main 上合并
git push -u origin feat/xxx   # 推送新分支到远程
```

## 5. 建仓库时多勾了 README 怎么办

**现象**:`git push` 被拒,提示 `rejected ... fetch first` 或 `non-fast-forward`。
**原因**:远程已有一条 `Initial commit`,与本地是两条互不相关(各自都是根提交)的历史。

两种处理,任选其一:

A. 保留远程提交,把自己的提交接到它后面(**推荐**,不改写远程历史)

```bash
git fetch origin
git rebase origin/main
git push -u origin main
```

B. 用合并方式接上

```bash
git pull --rebase origin main --allow-unrelated-histories
git push -u origin main
```

不要用 `git push -f` 强推覆盖,会抹掉远程上已有的提交。

## 6. 报错速查

| 报错 | 原因与处理 |
| --- | --- |
| `Permission denied (publickey)` | 密钥没被用上。先 `ssh -T git@github.com` 自测,再检查 `~/.ssh/config` 中 `IdentityFile` 路径 |
| `Please tell me who you are` | `user.name` / `user.email` 未配置 |
| `src refspec main does not match any` | 还没有 commit,或分支名不叫 main(`git branch -M main`) |
| `failed to push some refs` / `non-fast-forward` | 远程有新提交,先 `git pull --rebase` 再推;见第 5 节 |
| `Host key verification failed` | 服务器指纹不在 `known_hosts`,执行 `ssh-keyscan github.com >> ~/.ssh/known_hosts` |
| `warning: LF will be replaced by CRLF` | 无害提示,`.gitattributes` 已统一规则 |
| `## main...origin/main [gone]` | 本地缺远程分支引用,执行 `git fetch` 即可 |
| 连接 22 端口超时 | 端口被网络封禁,改用备用通道,见第 7 节 |

## 7. 备用通道(网络封 22 端口时)

`~/.ssh/config` 中已配好 `github-443` 别名,把地址里的 `github.com` 换成 `github-443` 即可:

```bash
ssh -T git@github-443                                          # 测通道
git clone git@github-443:keven798/仓库名.git                    # 克隆
git remote set-url origin git@github-443:keven798/仓库名.git     # 已有仓库改走备用通道
```

需要全局切换时:

```bash
git config --global url."git@github-443:".insteadOf "git@github.com:"
```

恢复默认:

```bash
git config --global --unset url."git@github-443:".insteadOf
```

## 8. 在命令行直接创建远程仓库(可选)

`git` 只能管本地,创建远程仓库要么走网页,要么装 GitHub CLI(`gh`):

```bash
gh auth login
gh repo create 仓库名 --private --source . --push
```

本机目前未装 `gh`,需要时再安装。

## 9. 配置都放在哪

| 文件 | 作用 |
| --- | --- |
| `C:\Users\shihan.li\.gitconfig` | git 全局配置(身份、换行符、URL 改写) |
| `C:\Users\shihan.li\.ssh\config` | SSH 通道与密钥配置 |
| `C:\Users\shihan.li\.ssh\known_hosts` | 已信任的服务器指纹 |
| `仓库/.gitignore` | 本仓库忽略规则 |
| `仓库/.gitattributes` | 本仓库换行符与二进制规则 |

查看当前全部全局配置:

```bash
git config --global --list
```
