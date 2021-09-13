# hugo

## 前提

1. 官方地址: https://github.com/gohugoio/hugo/releases
2. 保证你的git 命令能够正常从github上下载东西！

##  下载并安装命令

> 不管通过什么方法让hugo命令可用

1. 下载Windows-64bit.zip
2. 解压并创建目录 'D:\Program Files\hugo'
3. 将解压的文件拷贝到目录中
4. 在环境变量中创建HUGO_HOME
5. 将HUGO_HOME 添加到 Path中 '%HUGO_HOME%'
6. 这样就可以在任何命令中直接调用'hugo'了

> 使用 hugo--help测试是否安装成功
>
> 也可以作为文档使用它

```shell
E:\DBank>hugo --help
hugo is the main command, used to build your Hugo site.

Hugo is a Fast and Flexible Static Site Generator
built with love by spf13 and friends in Go.

Complete documentation is available at http://gohugo.io/.
```



## 创建博客

```CMD
E:\DBank>hugo new site hugoblog
Congratulations! Your new Hugo site is created in E:\DBank\hugoblog.

Just a few more steps and you're ready to go:

1. Download a theme into the same-named folder.
   Choose a theme from https://themes.gohugo.io/ or
   create your own with the "hugo new theme <THEMENAME>" command.
2. Perhaps you want to add some content. You can add single files
   with "hugo new <SECTIONNAME>\<FILENAME>.<FORMAT>".
3. Start the built-in live server via "hugo server".

Visit https://gohugo.io/ for quickstart guide and full documentation.

```

> 我们看到创建成功并且告诉我们我们可以通过以上方式去找主题。。

## 安装主题

> 去https://themes.gohugo.io/找个主题
>
> 我找到一个 **air** 比较喜欢的
>

1. 到themes目录下
2. git clone https://github.com/syui/hugo-theme-air

> 只要是在官方找到的主题下载到themes目录中就行。
>
> 不是所有的主题都持续更新的有些已经不能用了，多下载两个在后面的启动中确保你的主题正常。

## 启动博客

```shell
E:\DBank\hugoblog>hugo server -t air --buildDrafts
Web Server is available at http://localhost:12870/ (bind address 127.0.0.1)
```

> 访问以确认是否成功启动

## 写博客

```shell
hugo new post/hugo.md
```

> 创建的文件会在/content/post/. 中
>
> 重新发布
>
> E:\DBank\hugoblog>hugo server -t air --buildDrafts

## 部署

### 生成 public

```shell
hugo -t air -b "https://akachi10.github.io/" -D
```

> 参数解释
>
> -t, --theme strings
>
> -b, --baseURL string
>
> -D, --buildDrafts                include content marked as draft

### 创建项目

> 在github上创建项目
>
> 项目名必须是[username].github.io.git
>
> 比如我的账号叫akachi10 所有 username=akachi10

### 上传public到github

```shell
cd public
git init
git add .
git commit -m "第一次推送hugoblog"
git remote add hugoblog https://github.com/akachi10/akachi10.github.io.git
git push -u origin master
```

> 无论如何吧public中的内容推送到 你创建的项目"[username].github.io.git" 中去就好了。



## 写作要点
