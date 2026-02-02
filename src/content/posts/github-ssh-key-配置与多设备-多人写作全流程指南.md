---
title: GitHub SSH Key 配置与多设备 / 多人写作全流程指南
published: 2026-02-02T09:21:00.000+08:00
updated: 2026-02-02T09:28:00.000+08:00
description: |-
  这份文档适合长期保存，用于在任何电脑上快速恢复你的写作环境。  
  内容涵盖 SSH 配置、仓库克隆、写作流程、自动部署、多设备协作、多账号协作、国内环境技巧等。
tags:
  - 远程协作
category: 博客写作
draft: false
---
# 📌 目录

1. [为什么使用 SSH](#为什么使用-ssh)  
2. [生成 SSH Key](#生成-ssh-key)  
3. [将公钥添加到 GitHub](#将公钥添加到-github)  
4. [测试 SSH 连接](#测试-ssh-连接)  
5. [克隆主仓库](#克隆主仓库)  
6. [写文章](#写文章)  
7. [提交并推送](#提交并推送)  
8. [验证部署结果](#验证部署结果)  
9. [多人协作写作流程](#多人协作写作流程)  
10. [多设备写作建议](#多设备写作建议)  
11. [多账号协作建议](#多账号协作建议)  
12. [国内环境使用技巧](#国内环境使用技巧)  
13. [SSH 常见问题排查](#ssh-常见问题排查)  
14. [仓库结构说明](#仓库结构说明)  
15. [自动化部署说明](#自动化部署说明)  
16. [安全注意事项](#安全注意事项)

---

# 🌟 为什么使用 SSH

SSH 是 GitHub 官方推荐的方式，因为：

- 一次配置，永久免密  
- 国内访问更稳定  
- 不依赖 GitHub 网页  
- 多设备写作更方便  
- 安全性更高  

---

# 🌟 生成 SSH Key

在新电脑执行：

```bash
ssh-keygen -t ed25519 -C "你的邮箱"
```

一路回车即可。

生成的文件：

```
~/.ssh/id_ed25519        # 私钥（不能泄露）
~/.ssh/id_ed25519.pub    # 公钥（要添加到 GitHub）
```

---

# 🌟 将公钥添加到 GitHub

在手机或任意能打开 GitHub 的设备：

1. GitHub → **Settings**
2. 左侧：**SSH and GPG keys**
3. 点击 **New SSH key**
4. 粘贴 `id_ed25519.pub` 内容
5. 保存

---

# 🌟 测试 SSH 连接

回到电脑终端：

```bash
ssh -T git@github.com
```

看到：

```
Hi acuherb! You've successfully authenticated
```

表示成功。

用下面命令告诉git你是谁，要不然推送不过去
git config --global user.name "acuherb"
git config --global user.email "209729051+acuherb@users.noreply.github.com"


---

# 🌟 克隆主仓库

你要克隆的是写作仓库：

```bash
git clone git@github.com:acuherb/acuherb-blog.git
```

⚠ **必须使用 SSH 地址，不要用 HTTPS。**

克隆后目录结构示例：

```
acuherb/
  content/
  layouts/
  assets/
  hugo.toml
  .github/workflows/
```

---

# 🌟 写文章

文章放在：

```
content/posts/
```

你可以：

- 新建 `.md`  
- 修改已有文章  
- 使用 `publishDate` 写未来文章  
- 使用 Hugo frontmatter 设置标签、分类、封面等  

示例 frontmatter：

```yaml
---
title: "文章标题"
date: 2025-01-01
publishDate: 2025-02-01
tags: ["tag1", "tag2"]
categories: ["category"]
---
```

---

# 🌟 提交并推送

```bash
git add .
git commit -m "add new post"
git push
```

push 成功后：

- GitHub Actions 自动构建  
- 自动部署到 `acuherb.github.io`  
- 未来文章按 `publishDate` 自动上线  

---

# 🌟 验证部署结果

打开：

https://acuherb.xyz

- `publishDate` 是过去 → 立即上线  
- `publishDate` 是未来 → 定时构建时自动上线  

---

# 🌟 多人协作写作流程

多人写作时的黄金法则：

## ⭐ **永远先 pull，再 push。**

---

## ✔ 标准协作流程

### ① 开始写作前：拉取最新内容

```bash
git pull --rebase
```

### ② 写文章 / 修改内容  
按需编辑。

### ③ 提交修改

```bash
git add .
git commit -m "更新了文章：xxx"
```

### ④ 推送到 GitHub

```bash
git push
```

---

## ✔ 如果 push 被拒绝（常见）

Git 会提示：

```
Updates were rejected because the remote contains work that you do not have locally.
```

解决：

```bash
git pull --rebase
git push
```

---

## ✔ 协作最佳实践

- 每次写作前都 pull  
- 每次写作后立即 push  
- 尽量避免多人同时修改同一篇文章  
- 冲突时按提示修改即可  

---

# 🌟 多设备写作建议

- 每台电脑生成自己的 SSH key  
- 每台电脑都添加 `.pub` 到 GitHub  
- 每台电脑都用 SSH 地址克隆仓库  
- 设置统一 Git 信息：

```bash
git config --global user.name "acuherb"
git config --global user.email "你的主邮箱"
```

---

# 🌟 多账号协作建议

你有两个账号（主账号 + 副账号），建议：

| 用途 | 账号 | 权限 |
|------|------|------|
| 主写作、管理仓库 | acuherb | Owner |
| 多设备写作 | vigdraw | Collaborator（Write） |

这样：

- 两个账号都能 push  
- 不会混淆权限  
- 不影响安全性  

---

# 🌟 国内环境使用技巧

国内访问 GitHub 网页不稳定，但 SSH 通常能用。

如果 SSH 22 端口不通，可以改用 443：

编辑：

```
~/.ssh/config
```

添加：

```
Host github.com
  Hostname ssh.github.com
  Port 443
  User git
```

几乎 100% 能连。

---

# 🌟 SSH 常见问题排查

### ❌ `Permission denied (publickey)`
原因：公钥没添加到 GitHub  
解决：添加 `.pub` 到 GitHub → Settings → SSH keys

---

### ❌ 第一次连接提示 fingerprint  
正常，输入 `yes` 即可。

---

### ❌ push 时提示权限不足

检查是否克隆了正确的仓库：

```bash
git remote -v
```

必须是：

```
git@github.com:acuherb/acuherb.git
```

---

# 🌟 仓库结构说明

你的系统采用双仓库结构：

| 仓库 | 用途 | 是否可写 |
|------|------|----------|
| `acuherb/acuherb` | 主仓库（写文章） | ✔ 多人可写 |
| `acuherb/acuherb.github.io` | 部署仓库（自动生成） | ❌ 不可手动写 |

你只需要操作主仓库。

---

# 🌟 自动化部署说明

GitHub Actions 会：

- 监听 push  
- 自动构建 Hugo  
- 自动推送到 `acuherb.github.io`  
- 定时构建（未来文章自动上线）  

你不需要手动部署。

---

# 🌟 安全注意事项

- 不要泄露私钥 `id_ed25519`  
- 不要把别人加入部署仓库  
- 不要手动修改 `acuherb.github.io`  
- 不要用 HTTPS 克隆仓库  
- 不要把私钥复制到多台电脑  

---
