
---
title: hexo + github pages 搭建个人博客
---

## step1 安装hexo 

按照官网首页(https://hexo.io/zh-cn/)的安装方法，能在本地快速安装成功，并生成本地服务,mac 和 ubuntu 测试ok
```bash
npm install hexo-cli -g
hexo init blog
cd blog
npm install
hexo server
```

## step2 创建github pages
根据官网提供的方法创建一个名为**username.github.io**的仓库即可，非常丝滑(https://pages.github.com/)

## step3 关联本地blog 和 github pages
参考官网教程(https://hexo.io/zh-cn/docs/one-command-deployment)

1. 编辑_config.yml文件内容如下

```
deploy:
  type: git
  repo: git@github.com:wangchao-couson/wangchao-coulson.github.io.git // 官方提供的方法是使用hppts url，这里改为了ssh url，因此还需要提前设置ssh pub key到github仓库
  branch: main
```
2. 安装发布工具(deployer)

该工具能将./source/_posts目录中的md文件编译成html文件并设置相关配置然后推送到前面配置好的仓库中
发布工具有多种可选，这里以git作为发布工具，安装命令如下
```
npm install hexo-deployer-git --save
```
3. 发布上线

```
hexo clean && hexo deploy //官网文档写的不好，写的使用hexo deploy 命令，实际使用hexo clean && hexo deploy 组合命令能避免本地缓存
```

## step4. 配置主题
默认的主题比较简陋，甚至可能无法正常显示，因此需要安装一个自己喜欢的主题，主题可以在主题市场上找到 https://hexo.io/themes/
这里以**next**主题为例：https://github.com/next-theme/hexo-theme-next
```bash
使用
cd hexo-site
npm install hexo-theme-next
或者
cd hexo-site
git clone https://github.com/next-theme/hexo-theme-next.git themes/next
然后配置_config.yml文件中的theme字段
theme: next
最后重新发布
hexo clean && hexo deploy
```
## step5. 配置博客站点同步
完成上述配置后博客能正常运行，但是github仓库中仅仅同步了发布的文档，并没有同步博客站点配置文件、md源文件和themes等文件，如果需要在其他电脑上工作则无法更新博客站

因此在原有 https://github.com/wangchao-couson/wangchao-coulson.github.io 仓库上创建新分支**hexo**，用于同步博客站点配置数据
```bash
https://github.com/wangchao-couson/wangchao-coulson.github.io 仓库上创建新分支**hexo**
本地blog目录下执行如下命令
git init
git checkout -b hexo
git remote add origin git@github.com:wangchao-couson/wangchao-coulson.github.io.git    
git branch --set-upstream-to=origin/hexo hexo   
git add .
git commit -m "init"
git config pull.rebase true //解决git push origin hexo:master 时出现rejected错误
git pull
git push
```

此后，在任意电脑上同步hexo分支，就可以更新博客站点了
```
切换到hexo分支
$ git add .
$ git commit -m “…” 
$ git push
$ hexo deploy -g //生成网站并部署到 GitHub,也可以使用 hexo generate -d 命令，功能相同
```



## 问题排查
1. 无法访问 https://wangchao-couson.github.io ,提示404错误

按照官网的教程，执行完上面的步骤，按道理是已经完成了blog的部署和发布，且可以正确访问 https://wangchao-couson.github.io 了，实际上并不能
继续查看文档发现在 https://docs.github.com/en/pages/quickstart 一文中提到需要设置 一个“Branch”，简要步骤如下
```
Under your repository name, click  Settings. If you cannot see the "Settings" tab, select the  dropdown menu, then click Settings.
In the "Code and automation" section of the sidebar, click  Pages.
Under "Build and deployment", under "Source", select Deploy from a branch.
Under "Build and deployment", under "Branch", use the branch dropdown menu and select a publishing source.(root or doc)
```
然后重新执行发布命令：hexo clean && hexo deploy ，此时发现还是无法访问 https://wangchao-couson.github.io
再查看github仓库 wangchao-coulson.github.io->Settings->Pages 发现上面已经提示：Your site is live at https://wangchao-couson.github.io/wangchao-coulson.github.io/
因此实际上线的url并非 https://wangchao-couson.github.io,而是 https://wangchao-couson.github.io/wangchao-coulson.github.io/