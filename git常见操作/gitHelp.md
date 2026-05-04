
---

# 📘 一、常用 Git 命令（适合做笔记）

你可以直接复制到 `.md` 文件👇

---

## 🧱 1. 初始化仓库

```bash
git init
```

👉 作用：在当前目录创建 Git 仓库

---

## 📦 2. 添加文件到暂存区

```bash
git add 文件名
git add .
```

👉 作用：把修改加入“暂存区”（准备提交）

---

## 💾 3. 提交到本地仓库

```bash
git commit -m "提交说明"
```

👉 作用：保存一次版本

---

## 🔍 4. 查看状态

```bash
git status
```

👉 作用：查看哪些文件被修改 / 是否已暂存

---

## 📜 5. 查看历史记录

```bash
git log
```

👉 作用：查看提交历史

---

## 🔗 6. 连接远程仓库

```bash
git remote add origin git@github.com:用户名/仓库名.git
```

---

## 🚀 7. 推送到远程

```bash
git push
```

首次：

```bash
git push -u origin main
```

---

## ⬇️ 8. 拉取代码

```bash
git pull
```

---

## 🌿 9. 分支操作

```bash
git branch          # 查看分支
git branch dev      # 创建分支
git checkout dev    # 切换分支
git checkout -b dev # 创建并切换
```

---

## 🧹 10. 忽略文件

`.gitignore` 示例：

```gitignore
.DS_Store
node_modules/
.obsidian/
```

---

# 🧪 二、练习（很重要）

建议你**自己新建一个测试目录练习一遍**

---

## 🟢 练习 1：完整提交流程

👉 目标：熟悉最基本流程

1. 新建文件 `test.txt`
    
2. 写点内容
    
3. 执行：
    

```bash
git add .
git commit -m "add test"
git push
```

---

## 🟡 练习 2：修改文件

👉 目标：理解“修改 → 提交”

1. 修改 `test.txt`
    
2. 查看状态：
    

```bash
git status
```

3. 再提交一次
    

---

## 🔵 练习 3：查看历史

```bash
git log
```

👉 思考：

- 每次 commit 有什么信息？
    
- commit id 是什么？
    

---

## 🟣 练习 4：.gitignore

👉 目标：验证忽略规则

1. 创建 `.DS_Store` 或 `.obsidian/`
    
2. 写入 `.gitignore`
    
3. 执行：
    

```bash
git status
```

👉 看是否被忽略

---

## 🔴 练习 5（关键）：修正已提交文件

👉 模拟你刚才的问题

1. 提交一个不该提交的文件
    
2. 然后：
    

```bash
git rm --cached 文件名
git commit -m "fix"
```

---

# 🧠 三、理解比记命令更重要

记住这个流程👇

```text
工作区 → 暂存区 → 本地仓库 → 远程仓库
```

对应命令：

```text
修改文件
  ↓
git add
  ↓
git commit
  ↓
git push
```

---

# 🚀 如果你想继续进阶

下一步可以学：

- 分支（非常重要 ⭐）
    
- 回滚（git reset / git revert）
    
- 冲突解决（团队开发必备）