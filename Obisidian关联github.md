安装github，安装obisidian不再叙述这里。

## 两种方式关联github
### 第一种
已经在Obisidian里面创建了一系列笔记。
找到创建的文件夹。

打开github，创建一个仓库，仓库名随便取。

然后在上面的文件夹里执行git命令

git init
git add.
git remote add origin https://github.com/你的用户名/仓库名.git
git push -u origin master (master可以换成任意分支)
这时候会让你输入账户密码
Username for 'https://github.com': 你的账户名
Password for 'https://metleslia@github.com':
密码现在是采用key的形式，需要生成key在github里面

进入设置：
- 在 GitHub 页面右上角，点击你的头像，选择 Settings。
- 向下滚动到左侧菜单，点击 Developer settings。
创建令牌：
- 点击 Personal access tokens > Fine-grained tokens（推荐，权限更精细）或 Tokens (classic)。
- 点击 Generate new token。
- 如果选择 Fine-grained token：
- 命名你的令牌，例如 “Obsidian Sync”。
- 设置过期时间（建议 90 天或自定义）。
- 在 Repository access 中，选择 Only select repositories，然后选择刚创建的私有仓库。
- 在 Permissions 中，启用 Contents（读写权限）。
- 如果选择 Classic token：
- 勾选 repo 权限（包含所有仓库读写权限）。
点击 Generate token

输入生成的个人令牌这时候就关联到分支

在Obisidian里面找到第三方工具（安全模式关掉），搜索GIT，找到第一个安装且应用
设置这几个基本选项就可以，找不到的话可以试着搜索一下
- Vault backup interval：设置为自动提交的间隔，例如 5 分钟（单位：分钟）。
- Commit message：自定义提交信息，例如 Auto commit from Obsidian。
- Auto push：启用，以便每次提交后自动推送到 GitHub。
- Pull updates on startup：启用，确保启动 Obsidian 时自动拉取最新更改。

### 第二种
就是直接在仓库创建文件，然后直接clone到本地，在再这个文件夹下面创建你的obisidian