# 商品计划专员 Agent

「商品计划专员」智能体项目的工作空间。

## 目录约定

| 路径 | 说明 |
| --- | --- |
| `.workbuddy/` | WorkBuddy 运行时数据与记忆文件,已忽略,不入库 |

## Git 使用约定

### 首次使用前确认身份

提交记录会写入作者信息,首次使用请先配置一次:

```bash
git config --global user.name  "你的名字"
git config --global user.email "你的GitHub邮箱"
```

`user.email` 必须是 GitHub 账号中已验证的邮箱,否则提交不会计入贡献记录。

### 提交信息格式

采用 Conventional Commits 前缀:

| 前缀 | 用途 |
| --- | --- |
| `feat:` | 新增功能 |
| `fix:` | 修复问题 |
| `docs:` | 文档变更 |
| `refactor:` | 重构 |
| `chore:` | 杂项(依赖、配置、初始化) |

示例:`feat: 支持按专供编码匹配生产需求`

### 换行符

仓库内统一为 LF;Windows 批处理与 PowerShell 脚本保留 CRLF。规则写在 `.gitattributes` 中,跟随仓库而非跟随个人配置,因此不同操作系统的协作者结果一致。

### 业务数据

Excel 等二进制业务数据默认不入库,原因与例外写法见 `.gitignore` 内注释。含客户或订单明细的原始数据请勿提交。

## 远程仓库

```bash
# 关联远程(首次)
git remote add origin git@github.com:用户名/仓库名.git

# 推送并绑定上游
git push -u origin main
```
