## GitHub 关联方式一：本地已有 Obsidian 笔记，推送到 GitHub

适用于你已经在 Obsidian 中写了许多笔记，想将其备份到 GitHub。

### ✅ 步骤如下：

1. 打开 GitHub，**创建一个新仓库**（仓库名可自定义）。
    
2. 找到你 Obsidian 的笔记文件夹，打开终端（Terminal）或命令行工具，执行以下命令：
    
    `git init git add . git commit -m "first commit" git remote add origin https://github.com/你的用户名/仓库名.git git push -u origin master  # 或 main`
    
3. Git 会提示你输入用户名和密码：
    
    `Username for 'https://github.com': your-username Password for 'https://your-username@github.com':`
    
    ✅ **注意：现在 GitHub 不再接受密码登录，需要使用 Personal Access Token（PAT）代替密码**
    

---

### 🔐 生成 GitHub Token（令牌）：

1. 打开 GitHub 网站，点击右上角头像 → `Settings` → 左侧菜单 `Developer settings`
    
2. 点击 `Personal access tokens` >  
    选择 `Fine-grained tokens`（推荐） 或 `Tokens (classic)`
    
3. 点击 `Generate new token`，填写以下内容：
    
    - **Token name**：如 `Obsidian Sync`
        
    - **过期时间**：90 天或自定义
        
    - **Repository access**：选择 `Only select repositories`，并勾选你刚创建的仓库
        
    - **Permissions**：开启 `Contents` 的读写权限
        
4. 点击 `Generate token`，复制生成的 token（只显示一次）
    
5. 回到 Git 提示输入密码的地方，把 **token 粘贴进去**，就完成认证。
    

---

### ⚙️ 在 Obsidian 中安装 Git 插件（实现自动提交和同步）

1. 打开 Obsidian，进入：  
    `设置` → `社区插件` → 关闭“安全模式” → 搜索 `Git` 插件
    
2. 安装并启用第一个 `Obsidian Git` 插件
    
3. 插件设置建议如下：
    
    - `Vault backup interval`：如 5（每 5 分钟自动提交）
        
    - `Commit message`：自定义提交信息，如 `Auto commit from Obsidian`
        
    - `Auto push`：✅ 启用（每次提交自动推送）
        
    - `Pull updates on startup`：✅ 启用（启动时自动拉取最新更改）
        

---

## 🔗 GitHub 关联方式二：先创建远程仓库，再 Clone 到本地

适用于你还没有开始写 Obsidian 笔记，想从 GitHub 同步下来再使用。

### ✅ 步骤如下：

1. 在 GitHub 上创建仓库
    
2. 在本地执行：
    
    `git clone https://github.com/你的用户名/仓库名.git`
    
3. 将 Obsidian 的笔记创建在这个仓库文件夹中
    
4. 然后如同方式一一样，安装 Git 插件，启用同步功能即可
    

---

## ✅ 总结：两种方式对比

|场景|方法|本地已有笔记？|操作方式|
|---|---|---|---|
|本地已有笔记|方式一|✅ 是|`git init` → 推送到 GitHub|
|新开始写笔记|方式二|❌ 否|GitHub clone 仓库后写笔记|