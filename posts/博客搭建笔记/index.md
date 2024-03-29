# 博客搭建笔记


<!--more-->

本博客使用 GitHub Pages、Hugo 和 LoveIt，可以高度~~麻烦地~~定制化博客。

|      | GitHub Pages                                                 | Hugo                                   | LoveIt                   |
| ---- | ------------------------------------------------------------ | -------------------------------------- | ------------------------ |
| 优点 | 无须购买云服务，支持的功能多，玩法丰富                       | 基于 go 开发，更快及不必安装一堆依赖   | 简洁                     |
| 缺点 | 项目和网站的大小不超过 1GB，每小时不超过 10 个版本，每个月带宽使用上限为 100GB | 更年轻，功能性和稳定性欠缺，支持主题少 | 作者上次更新还是在2020年 |

## 1 准备

### 1.1 安装 Hugo

详细的安装说明请参考 [官方文档](https://gohugo.io/getting-started/installing/)。注意安装 extended 版本。

macOS 下可使用包管理工具 [Homebrew](https://brew.sh) 快速安装：

```bash
brew install hugo
```

Windows 下可使用包管理工具 [chocolatey](https://chocolatey.org/) 快速安装。以管理员权限打开 `cmd.exe`：~~没运行过，不保证这段代码的可行性~~

```bash
# 安装 chocolatey
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"

# 安装 Hugo
choco install hugo-extended -y
```

验证 Hugo 是否安装成功：

```bash
hugo version
```

### 1.2 安装 Git

从 [官方网站](https://git-scm.com) 下载，或使用命令行：

```bash
brew install git
```

```bash
choco install git -y
```

### 1.3 创建 GitHub 仓库

注册一个 [GitHub](https://github.com) 账号。

注意：用户名与随后创建的博客网址强相关。此用户名记作 `<user_name>`（这里 `<>` 指代参数，下同）。

在个人主界面里选择 “Create a repository”。在 “Repository name” 的位置填写仓库名称，称为 `<repo_name>`，格式为 `<user_name>.github.io`。仓库权限为 “public”。

{{< admonition tip "提示">}}
当 `<repo_name>` 为 `<user_name>.github.io` 时，最后的博客网址为  `<user_name>.github.io`；

否则，网址为  `<user_name>.github.io/<repo_name>`。
{{< /admonition >}}



## 2 快速搭建

### 2.1 托管到 GitHub

打开命令行，输入下列命令在本地生成博客项目文件夹，该文件夹是所有操作执行的根目录：

```bash
# 希望博客项目放在哪个文件夹下，命令行就处于哪个文件夹下，如 D:\ 或 ～
hugo new site <folder_name> # 目录名任取
cd <folder_name> # 进入文件夹
```

将项目文件夹初始化为 GitHub 仓库：

```bash
git init # 生成 .git 文件夹
```

{{<admonition tip "巧用 .gitignore" false>}}

输入 `git status`，macOS 用户可能会看到 `.DS_Store` 文件。这是存储文件夹显示属性的文件，但我们并不想将其纳入 Git 的管理范围。在项目根目录里创建 `.gitignore` 文件并按 [glob 模式](https://www.gulpjs.com.cn/docs/getting-started/explaining-globs/) 匹配不想要的文件即可处理这种情况。若已暂存了不想要的文件，可以用 ``git rm -r --cached .`` 撤销文件暂存。

考虑到每个项目都不需要 `.DS_Store`，采用全局配置的方法。在用户文件夹（即 `Macintosh HD/Users/<user_name>`）下创建 `.gitglobalignore` 文件，在文件中输入 `**/.DS_Store`；在命令行中输入：

```bash
git config --global core.excludesfile ~/.gitglobalignore
```

{{</admonition>}}

与远程仓库建立连接需要 `<token>`。登陆 GitHub > 点击头像 > “Settings” > “Developer settings” > “Personal access tokens” > “Generate new token” 得到 `<token>`。“Expiration” 选择 “no expiration”，“Select scopes” 项全部勾选。

将项目文件推送到远程仓库：

```bash
git add . # 添加所有文件到暂存区
git commit -m "Initial commit" # 提交暂存区到仓库区
git remote add origin https://<token>@github.com/<user_name>/<repo_name>.git
git push -u origin master # -u 将本地 master 分支与远程 master 分支关联
```

### 2.2 安装主题

主题通常是单独的 Github 仓库，将其作为项目的子模块进行管理：

```bash
git submodule add https://github.com/dillonzq/LoveIt.git themes/LoveIt
```

{{<admonition tip "加快下载速度" false>}}

从 github.com 下载可能过慢，可以从 GitHub 的镜像网站下载：

```bash
git submodule add https://hub.fastgit.org/dillonzq/LoveIt.git themes/LoveIt
```

但这会导致后面步骤的 GitHub Actions 报错。

解决办法为：子模块加载完毕后，打开文件 `/.gitmodules`，修改

```bash
[submodule "themes/LoveIt"]
	path = themes/LoveIt
	url = https://hub.fastgit.org/dillonzq/LoveIt.git
```

为

```bash
[submodule "themes/LoveIt"]
	path = themes/LoveIt
	url = https://github.com/dillonzq/LoveIt.git
```

打开 `/.git/config`，修改

```bash
[submodule "themes/LoveIt"]
	url = https://hub.fastgit.org/dillonzq/LoveIt.git
```

为

```bash
[submodule "themes/LoveIt"]
	url = https://github.com/dillonzq/LoveIt.git
```

打开命令行，输入：

```bash
git submodule sync
```

{{</admonition>}}

以下是 [LoveIt](https://hugoloveit.com/zh-cn/) 主题的基础配置。使其全部代替根目录中 `config.toml`中的代码：

```toml
baseURL = "http://example.org/"
# [en, zh-cn, fr, ...] 设置默认的语言
defaultContentLanguage = "zh-cn"
# 网站语言, 仅在这里 CN 大写
languageCode = "zh-CN"
# 是否包括中日韩文字
hasCJKLanguage = true
# 网站标题
title = "我的全新 Hugo 网站"

# 更改使用 Hugo 构建网站时使用的默认主题
theme = "LoveIt"

[params]
  # LoveIt 主题版本
  version = "0.2.X"

[menu]
  [[menu.main]]
    identifier = "posts"
    # 你可以在名称 (允许 HTML 格式) 之前添加其他信息, 例如图标
    pre = ""
    # 你可以在名称 (允许 HTML 格式) 之后添加其他信息, 例如图标
    post = ""
    name = "文章"
    url = "/posts/"
    # 当你将鼠标悬停在此菜单链接上时, 将显示的标题
    title = ""
    weight = 1
  [[menu.main]]
    identifier = "tags"
    pre = ""
    post = ""
    name = "标签"
    url = "/tags/"
    title = ""
    weight = 2
  [[menu.main]]
    identifier = "categories"
    pre = ""
    post = ""
    name = "分类"
    url = "/categories/"
    title = ""
    weight = 3

# Hugo 解析文档的配置
[markup]
  # 语法高亮设置 (https://gohugo.io/content-management/syntax-highlighting)
  [markup.highlight]
    # false 是必要的设置 (https://github.com/dillonzq/LoveIt/issues/158)
    noClasses = false
```

创建第一篇文章：

```bash
hugo new posts/first_post.md
```

生成文件在 `/content/posts` 下。参考 [Markdown 基本语法](https://hugoloveit.com/zh-cn/basic-markdown-syntax/#5-内联-html-元素)，随意编辑文章。

注意：把前置参数 `draft: false` 改为 `draft: true`，且文件内容不能为空。

在本地渲染网站：

```bash
hugo serve
```

到 `http://localhost:1313` 查看你的博客！

### 2.3 部署网站

利用 [Github Actions](https://docs.github.com/cn/actions) 可以帮助我们一键部署博客到网站上。

#### 2.3.1 配置 GitHub Actions

创建 `blog` 分支：

```bash
# 新建并切换到 blog 分支
git checkout -b blog 
# 在远程创建 blog 分支，并将本地 blog 分支与远程 blog 分支关联
git push --set-upstream origin blog
```

在网页端进入仓库 > “Settings” > “Branches”，切换默认分支为 `blog` 分支。

{{< admonition note "验证是否跟踪成功" false>}}

检查分支跟踪关系：

```bash
git branch -vv
```

可以看到类似于 `* blog c63526c [origin/blog] Initial commit` 的字样。

输入 `:q` 退出。

{{< /admonition >}}

生成公私钥供 Github Action 使用：

```bash
ssh-keygen -t rsa -b 4096 -C "<your_email>@<example.com>" -f blog -N ""
# 在此命令运行的目录下得到两个文件：
#   blog.pub （公钥）
#   blog     （私钥）
```

进入仓库，点击 “Settings”，在 “Deploy Keys” 中添加公钥，勾选 “Allow write access”；在 “Secrets” 中添加私钥，私钥名设置为 `ACTIONS_DEPLOY_KEY`。

新建若干文件夹与文件使得根目录包含：`/.github/workflows/main.yml`。写入 `main.yml`内容：

```yaml
name: hugo push to github pages

on:
  push:
    branches:
    - blog

jobs:
  build-deploy:
    runs-on: ubuntu-18.04
    steps:
    - uses: actions/checkout@v1
      with:
        submodules: true

    - name: Setup Hugo
      uses: peaceiris/actions-hugo@v2
      with:
        hugo-version: '0.88.1' # 更改为当前所用版本
        extended: true

    - name: Build
      run: hugo --minify

    - name: Deploy
      uses: peaceiris/actions-gh-pages@v2
      env:
        ACTIONS_DEPLOY_KEY: ${{ secrets.ACTIONS_DEPLOY_KEY }}
        PUBLISH_BRANCH: master
        PUBLISH_DIR: ./public
```

在命令行中输入：

```bash
git add .
git commit -m "<description>" # 对本次提交的描述
git push
```

就可以在 `https://username.github.io` 中看到你的博客网站了！

#### 2.3.2 思路

按照 Hugo 的生成规则，执行 `hugo` 命令后，网站静态文件将会生成在 `/public` 文件夹。但由于我们使用 Github Pages 托管博客网站，该功能启用后 Github 仓库只会从 `master branch` 或 `master branch /docs folder` 读取网站源码。

我们解决这一问题的方法是新建 `blog` 分支将博客源码放在该分支下，利用 Github Actions 将 `/public` 目录下的网站文件推送到 `master` 分支。

Github Actions 使用一种模块化的思路，即将很多持续集成的操作写成独立的脚本文件，放到代码仓库，让其它开发者使用。因此进行持续集成时，可以直接引用别人写好的 actions，整个持续集成的过程，就是一个 actions 组合的过程。GitHub 做了一个 [官方市场](https://github.com/marketplace?type=actions)，可以搜索到他人提交的 actions。另外，还有一个 [awesome actions](https://github.com/sdras/awesome-actions) 的仓库，也可以找到不少 actions。

我们的基本思路如下：

1. 整个流程在 blog 分支 `push` 时触发；
2. 只有一个 `job`，运行在 ubuntu-18.04 环境下；
3. 使用 [official action: checkout](https://github.com/actions/checkout) 获取仓库源码，`clone` 主题子模块；
4. 使用 [peaceiris/actions-hugo: GitHub Actions for Hugo](https://github.com/peaceiris/actions-hugo) 部署 hugo 环境，注意使用 extentded 版本（主题要求）；
5. 直接执行 `hugo` 命令；
6. 使用 [peaceiris/actions-gh-pages](https://github.com/peaceiris/actions-gh-pages) 将当前分支 `/public` 目录下的内容部署到 master 分支。

可以到仓库 > “Actions” 中查看 `workflow` 是否运行成功或者排查错误。

## 3 详细配置

对网站进行更加个性化的定制。

### 3.1 网站配置

使下列代码全部代替 `config.toml`中的代码：

```toml
baseURL = "https://<user_name>.github.io"

# [en, zh-cn, fr, ...] 设置默认的语言
defaultContentLanguage = "zh-cn"

# 网站语言, 仅在这里 CN 大写
languageCode = "zh-CN"

# 是否包括中日韩文字
hasCJKLanguage = true

# 网站标题
title = "<title of website>"

# 更改使用 Hugo 构建网站时使用的默认主题
theme = "LoveIt"

# whether to use emoji code
enableEmoji = true

# whether to use git commit log to generate lastmod record
#enableGitInfo = false

# whether to use robots.txt
#enableRobotsTXT = false

# copyright description used only for seo schema
#copyright = "This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License."

# 作者配置
[author]
  name = "<name>"
  link = "https://<user_name>.github.io/about/"

# Permalinks Info (https://gohugo.io/content-management/urls/#permalinks)
[Permalinks]
	# posts = ":year/:month/:filename"
	# or
	# posts = ":year/:month/:slug"
  posts = "posts/:filename"



[menu]
  [[menu.main]]
    identifier = "posts"
    # 你可以在名称 (允许 HTML 格式) 之前添加其他信息, 例如图标
    pre = ""
    # 你可以在名称 (允许 HTML 格式) 之后添加其他信息, 例如图标
    post = ""
    name = "文章"
    url = "/posts/"
    # 当你将鼠标悬停在此菜单链接上时, 将显示的标题
    title = ""
    weight = 1
  [[menu.main]]
    identifier = "tags"
    pre = ""
    post = ""
    name = "标签"
    url = "/tags/"
    title = ""
    weight = 2
  [[menu.main]]
    identifier = "categories"
    pre = ""
    post = ""
    name = "专栏"
    url = "/categories/"
    title = ""
    weight = 3
   [[menu.main]]
    identifier = "about"
    name = "关于"
    url = "about"
    weight = 4

[params]
  # LoveIt 主题版本
  version = "0.2.X"
  # 网站描述
  description = "Null"
  # 网站关键词
  keywords = ["Blog"]
  # 网站默认主题样式 ("light", "dark", "auto")
  defaultTheme = "auto"
  # 公共 git 仓库路径，仅在 enableGitInfo 设为 true 时有效
  gitRepo = ""
  #  哪种哈希函数用来 SRI, 为空时表示不使用 SRI
  # ("sha256", "sha384", "sha512", "md5")
  fingerprint = ""
  #  日期格式
  dateFormat = "2006-01-02"
  # 网站图片, 用于 Open Graph 和 Twitter Cards
  images = ["/logo.png"]

  # 主页配置
  [params.home]
    #  RSS 文章数目
    rss = 10
    # 主页个人信息
    [params.home.profile]
      enable = true
      # Gravatar 邮箱，用于优先在主页显示的头像
      #gravatarEmail = ""
      # 主页显示头像的 URL
      avatarURL = "/images/avatar.png"
      #  主页显示的网站标题 (支持 HTML 格式)
      title = "<title of blog>"
      # 主页显示的网站副标题
      subtitle = "<subtitle of blog>"
      # 是否为副标题显示打字机动画
      typeit = false
      # 是否显示社交账号
      social = true
      #  免责声明 (支持 HTML 格式)
      disclaimer = ""
    # 主页文章列表
    [params.home.posts]
      enable = true
      # 主页每页显示文章数量
      paginate = 6
      #  被 params.page 中的 hiddenFromHomePage 替代
      # 当你没有在文章前置参数中设置 "hiddenFromHomePage" 时的默认行为
      defaultHiddenFromHomePage = false

  # 页面头部导航栏配置
  [params.header]
    # 桌面端导航栏模式 ("fixed", "normal", "auto")
    desktopMode = "fixed"
    # 移动端导航栏模式 ("fixed", "normal", "auto")
    mobileMode = "auto"
    #  页面头部导航栏标题配置
    [params.header.title]
      # LOGO 的 URL
      logo = "/images/logo.png"
      # 标题名称
      name = "<title of header>"
      # 你可以在名称 (允许 HTML 格式) 之前添加其他信息, 例如图标
      pre = ""
      # 你可以在名称 (允许 HTML 格式) 之后添加其他信息, 例如图标
      post = ""
      #  是否为标题显示打字机动画
      typeit = false

  # 页面底部信息配置
  [params.footer]
    enable = true
    #  自定义内容 (支持 HTML 格式)
    custom = ''
    #  是否显示 Hugo 和主题信息
    hugo = false
    #  是否显示版权信息
    copyright = true
    #  是否显示作者
    author = true
    # 网站创立年份
    since = 2021
    # ICP 备案信息，仅在中国使用 (支持 HTML 格式)
    icp = ""
    # 许可协议信息 (支持 HTML 格式)
    license = '<a rel="license external nofollow noopener noreffer" href="https://creativecommons.org/licenses/by-nc/4.0/" target="_blank">CC BY-NC 4.0</a>'

	  # 作者的社交信息设置
  [params.social]
    [params.social.Douban]
   		# 排列图标时的权重
      weight = 0
      # 你的社交 ID
      id = "" 
      # 你的社交链接的前缀
      prefix = "https://www.douban.com/people/"
      # 当鼠标停留在图标上时的提示内容
      title = "豆瓣"
      # 最终生成链接为 "https://www.douban.com/people/id"
      # 可据此获得 id
      
    [params.social.GitHub]
      weight = 1
      id = ""
      prefix = "https://github.com/"
      title = "GitHub"
      
    #Email = "xxxx@xxxx.com"
    #RSS = true
    #Googlescholar = ""
    #Zhihu = ""
    #Bilibili = ""
    #Mastodon = ""
    #Steam = ""
    #Paypal = ""
    #Spotify = ""   
    #Linkedin = ""
    #Twitter = "xxxx"
    #Instagram = "xxxx"
    #Facebook = "xxxx"
    #Telegram = "xxxx"
    #Medium = ""
    #Gitlab = ""
    #Youtubelegacy = ""
    #Youtubecustom = ""
    #Youtubechannel = ""
    #Tumblr = ""
    #Quora = ""
    #Keybase = ""
    #Pinterest = ""
    #Reddit = ""
    #Codepen = ""
    #FreeCodeCamp = ""
    #Bitbucket = ""
    #Stackoverflow = ""
    #Weibo = ""
    #Odnoklassniki = ""
    #VK = ""
    #Flickr = ""
    #Xing = ""
    #Snapchat = ""
    #Soundcloud = ""
    #Bandcamp = ""
    #Fivehundredpx = ""
    #Mix = ""
    #Goodreads = ""
    #Lastfm = ""
    #Foursquare = ""
    #Hackernews = ""
    #Kickstarter = ""
    #Patreon = ""
    #Twitch = ""
    #Strava = ""
    #Skype = ""
    #Whatsapp = ""
    #Angellist = ""
    #Slidershare = ""
    #Jsfiddle = ""
    #Deviantart = ""
    #Behance = ""
    #Dribbble = ""
    #Wordpress = ""
    #Vine = ""
    #Researchgate = ""
    #Thingiverse = ""
    #Devto = ""
    #Gitea = ""
    #XMPP = ""
    #Matrix = ""

  #  文章页面配置
  [params.page]

    # 是否使用 lightgallery
    lightgallery = false
    # 是否在文章页面显示原始 Markdown 文档链接
    linkToMarkdown = false

    #  是否在主页隐藏一篇文章
    hiddenFromHomePage = false
    #  是否在搜索结果中隐藏一篇文章
    hiddenFromSearch = false
    #  目录配置
    [params.page.toc]
      # 是否使用目录
      enable = true
      #  是否保持使用文章前面的静态目录
      keepStatic = false
      # 是否使侧边目录自动折叠展开
      auto = false
    #  是否使用 twemoji
    twemoji = false
    #  是否使用 ruby 扩展语法
    ruby = true
    #  是否使用 fraction 扩展语法
    fraction = true
    #  是否使用 fontawesome 扩展语法
    fontawesome = true
    #  是否在 RSS 中显示全文内容
    rssFullText = false
    #  代码配置
    [params.page.code]
      # 是否显示代码块的复制按钮
      copy = true
      # 默认展开显示的代码行数
      maxShownLines = 10
    #  KaTeX 数学公式
    [params.page.math]
      enable = true
      # 默认块定界符是 $$ ... $$ 和 \\[ ... \\]
      blockLeftDelimiter = ""
      blockRightDelimiter = ""
      # 默认行内定界符是 $ ... $ 和 \\( ... \\)
      inlineLeftDelimiter = ""
      inlineRightDelimiter = ""
      # KaTeX 插件 copy_tex
      copyTex = true
      # KaTeX 插件 mhchem
      mhchem = true
    #  Mapbox GL JS 配置
    [params.page.mapbox]
      # Mapbox GL JS 的 access token
      accessToken = ""
      # 浅色主题的地图样式
      lightStyle = "mapbox://styles/mapbox/light-v9"
      # 深色主题的地图样式
      darkStyle = "mapbox://styles/mapbox/dark-v9"
      # 是否添加 NavigationControl
      navigation = true
      # 是否添加 GeolocateControl
      geolocate = true
      # 是否添加 ScaleControl
      scale = true
      # 是否添加 FullscreenControl
      fullscreen = true
    #  文章页面的分享信息设置
    [params.page.share]
      enable = false
      Evernote = true
      Weibo = true

      Renren = false
      Line = false
      Twitter = false
      Facebook = false
      Linkedin = false
      Whatsapp = false
      Pinterest = false
      Tumblr = false
      HackerNews = false
      Reddit = false
      VK = false
      Buffer = false
      Xing = false
      Instapaper = false
      Pocket = false
      Digg = false
      Stumbleupon = false
      Flipboard = false
      Myspace = false
      Blogger = false
      Baidu = false
      Odnoklassniki = false
      Skype = false
      Trello = false
      Mix = false
    #  评论系统设置
    [params.page.comment]
      enable = false
      # Disqus 评论系统设置
      [params.page.comment.disqus]
        # 
        enable = false
        # Disqus 的 shortname，用来在文章中启用 Disqus 评论系统
        shortname = ""
      # Gitalk 评论系统设置
      [params.page.comment.gitalk]
        # 
        enable = false
        owner = ""
        repo = ""
        clientId = ""
        clientSecret = ""
      # Valine 评论系统设置
      [params.page.comment.valine]
        enable = false
        appId = ""
        appKey = ""
        placeholder = ""
        avatar = "mp"
        meta= ""
        pageSize = 10
        lang = ""
        visitor = true
        recordIP = true
        highlight = true
        enableQQ = false
        serverURLs = ""
        #  emoji 数据文件名称, 默认是 "google.yml"
        # ("apple.yml", "google.yml", "facebook.yml", "twitter.yml")
        # 位于 "themes/LoveIt/assets/data/emoji/" 目录
        # 可以在你的项目下相同路径存放你自己的数据文件:
        # "assets/data/emoji/"
        emoji = ""
      # Facebook 评论系统设置
      [params.page.comment.facebook]
        enable = false
        width = "100%"
        numPosts = 10
        appId = ""
        languageCode = "zh_CN"
      #  Telegram Comments 评论系统设置
      [params.page.comment.telegram]
        enable = false
        siteID = ""
        limit = 5
        height = ""
        color = ""
        colorful = true
        dislikes = false
        outlined = false
      #  Commento 评论系统设置
      [params.page.comment.commento]
        enable = false
      #  Utterances 评论系统设置
      [params.page.comment.utterances]
        enable = false
        # owner/repo
        repo = ""
        issueTerm = "pathname"
        label = ""
        lightTheme = "github-light"
        darkTheme = "github-dark"

  #  Section (所有文章) 页面配置
  [params.section]
    # section 页面每页显示文章数量
    paginate = 20
    # 日期格式 (月和日)
    dateFormat = "01-02"
    # RSS 文章数目
    rss = 10

  #  List (目录或标签) 页面配置
  [params.list]
    # list 页面每页显示文章数量
    paginate = 20
    # 日期格式 (月和日)
    dateFormat = "01-02"
    # RSS 文章数目
    rss = 10

  #  搜索配置
  [params.search]
    enable = true
    # 搜索引擎的类型 ("lunr", "algolia")
    type = "lunr"
    # 文章内容最长索引长度
    contentLength = 4000
    # 搜索框的占位提示语
    placeholder = ""
    #  最大结果数目
    maxResultLength = 10
    #  结果内容片段长度
    snippetLength = 50
    #  搜索结果中高亮部分的 HTML 标签
    highlightTag = "em"
    #  是否在搜索索引中使用基于 baseURL 的绝对路径
    absoluteURL = false
    [params.search.algolia]
      index = ""
      appID = ""
      searchKey = ""

  #  第三方库文件的 CDN 设置
  [params.cdn]
    # CDN 数据文件名称, 默认不启用
    # ("jsdelivr.yml")
    # 位于 "themes/LoveIt/assets/data/cdn/" 目录
    # 可以在你的项目下相同路径存放你自己的数据文件:
    # "assets/data/cdn/"
    data = ""

    #  第三方库配置
    [params.page.library]
      [params.page.library.css]
        # someCSS = "some.css"
        # 位于 "assets/"
        # 或者
        # someCSS = "https://cdn.example.com/some.css"
      [params.page.library.js]
        # someJavascript = "some.js"
        # 位于 "assets/"
        # 或者
        # someJavascript = "https://cdn.example.com/some.js"
    #  页面 SEO 配置（搜索引擎优化）
    [params.page.seo]
      # 图片 URL
      images = []
      # 出版者信息
      [params.page.seo.publisher]
        name = ""
        logoUrl = ""

  #  TypeIt 配置
  [params.typeit]
    # 每一步的打字速度 (单位是毫秒)
    speed = 100
    # 光标的闪烁速度 (单位是毫秒)
    cursorSpeed = 1000
    # 光标的字符 (支持 HTML 格式)
    cursorChar = "|"
    # 打字结束之后光标的持续时间 (单位是毫秒, "-1" 代表无限大)
    duration = -1

  # 网站验证代码，用于 Google/Bing/Yandex/Pinterest/Baidu
  [params.verification]
    google = ""
    bing = ""
    yandex = ""
    pinterest = ""
    baidu = ""

  #  兼容性设置
  [params.compatibility]
    # 是否使用 Polyfill.io 来兼容旧式浏览器
    polyfill = false
    # 是否使用 object-fit-images 来兼容旧式浏览器
    objectFit = false

  #  应用图标配置
  [params.app]
    # 当添加到 iOS 主屏幕或者 Android 启动器时的标题, 覆盖默认标题
    title = "LoveIt"
    # 是否隐藏网站图标资源链接
    noFavicon = false
    # 更现代的 SVG 网站图标, 可替代旧的 .png 和 .ico 文件
    #svgFavicon = ""
    # Android 浏览器主题色
    themeColor = "#ffffff"
    # Safari 图标颜色
    iconColor = "#5bbad5"
    # Windows v8-10磁贴颜色
    tileColor = "#da532c"

  #  网站 SEO 配置
  [params.seo]
    # 图片 URL
    image = ""
    # 缩略图 URL
    thumbnailUrl = ""

  #  网站分析配置
  [params.analytics]
    enable = false
    # Google Analytics
    [params.analytics.google]
      id = ""
      # 是否匿名化用户 IP
      anonymizeIP = true
    # Fathom Analytics
    [params.analytics.fathom]
      id = ""
      # 自行托管追踪器时的主机路径
      server = ""

  #  Cookie 许可配置
  [params.cookieconsent]
    enable = false
    # 用于 Cookie 许可横幅的文本字符串
    [params.cookieconsent.content]
      message = ""
      dismiss = ""
      link = ""

# Hugo 解析文档的配置
[markup]
  # 语法高亮设置
  [markup.highlight]
    codeFences = true
    guessSyntax = true

    # 代码行数设置
    lineNos = true
    lineNoStart = 1
    lineNumbersInTable = true
    tabWidth = 4
    
    #style = "monokai"

    # (https://github.com/dillonzq/LoveIt/issues/158)
    noClasses = false

  # Goldmark 是 Hugo 0.60 以来的默认 Markdown 解析库
  [markup.goldmark]
    [markup.goldmark.extensions]
      definitionList = true
      footnote = true
      linkify = true
      strikethrough = true
      table = true
      taskList = true
      typographer = true
    [markup.goldmark.renderer]
      # 是否在文档中直接使用 HTML 标签
      unsafe = true
  # 目录设置
  [markup.tableOfContents]
    startLevel = 2
    endLevel = 6

# 隐私信息配置(https://gohugo.io/about/hugo-and-gdpr/)
[privacy]
  #  Google Analytics 相关隐私 (被 params.analytics.google 替代)
  [privacy.googleAnalytics]
    # ...
  [privacy.twitter]
    enableDNT = true
  [privacy.youtube]
    privacyEnhanced = true

# 用于输出 Markdown 格式文档的设置
[mediaTypes]
  [mediaTypes."text/plain"]
    suffixes = ["md"]

# 用于输出 Markdown 格式文档的设置
[outputFormats.MarkDown]
  mediaType = "text/plain"
  isPlainText = true
  isHTML = false

# 网站地图配置
[sitemap]
  changefreq = "weekly"
  filename = "sitemap.xml"
  priority = 0.5

# 用于 Hugo 输出文档的设置
[outputs]
  home = ["HTML", "RSS", "JSON"]
  page = ["HTML", "MarkDown"]
  section = ["HTML", "RSS"]
  taxonomy = ["HTML", "RSS"]
  taxonomyTerm = ["HTML"]
```

更改 `baseURL`、`title`、	`[author]`、`[params.home.profile]`、`[params.social]`、`[params.header]` 为自己喜欢的内容。

{{<admonition tip "关于图片的设置">}}

将主页头像 `avatar.png`、导航栏图片 `logo.png` 放置于 `/static/images` 目录。

{{</admonition>}}

### 3.2 网页图标

为了使各种设备都能显示网页图标，需要生成不同类型的 [favicon](https://baike.baidu.com/item/Favicon/3410211)。

访问 https://www.favicon-generator.org 或其他各类 favicon 生成网站，将得到的图片、`browserconfig.xml` 和 `site.webmanifest` 放置于 `/static` 目录。

## 4 文章发布

总结发布文章的流程如下。

使用 Hugo 新建文件：

```bash
hugo new posts/title.md
```

修改 [前置参数](https://hugoloveit.com/zh-cn/theme-documentation-content/#front-matter)。利用 [LoveIt](https://hugoloveit.com/zh-cn/posts/) 提供的各种样式撰写文章。

在本地渲染网站无误后：

```bash
hugo serve # 在 http://localhost:1313 查看
```

 部署到 GitHub：

```bash
# 注意当前要处于根目录和 blog 分支
git add .
git commit -m "<description>" # 对本次提交的描述
git push
```

等待数分钟，GitHub Actions 的 `workflow` 完成后，到 `https://<user_name>.github.io` 查看博客。

## 附录 参考链接

[1]：[LoveIt 官方文档](https://hugoloveit.com/zh-cn/posts/)

[2]：[书葬的博客](https://shuzang.github.io/tags/hugo/)（特别致谢！）

[3]：[Hugo 官方文档](https://gohugo.io/documentation/)

