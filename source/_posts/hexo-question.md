---
title: 搭建Hexo博客遇见问题记录
date: 2018-10-12 12:12:12
tags:
- hexo
categories:
- 杂谈
---

### Hexo deploy过程中提示访问失败问题

#### 错误如下

<pre>
ssh: connect to host github.com port 22: Operation timed out
fatal: Could not read from remote repository.
Please make sure you have the correct access rights
</pre>

#### 解决

需要到根目录下配置config文件（~/.ssh/config），配置文件config,配置如下，或者可能是因为别某墙的原因，你懂的，多重试两次。
<pre>
Host {xxx}.github.io
User github账号
Hostname github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa
Port 443
</pre>


### 自定义域名后不能勾选 enforce HTTPS问题

- 把 Custom domain 中的值清空，并点击 Save 进行保存
- 在 Custom domain 中的填入之前清空的值，比如xxx.com,填入后点击保存
- 尝试在浏览器里主动访问 xxx.com ，注意协议类型是 https，正确情况下是能正常访问的
- 刷新项目设置页，正常情况下默认已经勾选了或者可以勾选了


### 源码和静态文件分别不同分支管理问题

#### 方案一

- 先github建用户名用户仓库，比如xx.github.io(xx替换你想要的）

- 然后本地建源码分支，比如 hexo
<pre>
	mddir blog && cd blog 
	git init
	git checkout -b hexo
	touch README
	git add .
	git commit -m "init blog"
	git remote add origin git@github.com:{username}/{username}.github.io.git
	git push origin hexo
</pre>

- 将本地 hexo init好的博客源码copy到 blog文件夹

- 建立忽略文件,因为源码和生成的静态文件在不同的分支，静态文件默认在master
<pre>
	.DS_Store
	Thumbs.db
	db.json
	*.log
	node_modules/
	public/
	.deploy*/
</pre>

- 设置_config.yml的 deploy,一定保证repo设置对，branch为master
<pre>
	deploy:
	  type: git
	  repo: git@github.com:xxx/xxx.github.io.git
	  branch: master
</pre>

- 执行 hexo g -d

- 然后在执行 git add . ，commit push等操作，完成源码push

#### 方案二

- 参考 [博客搭建](https://mp.weixin.qq.com/s/sXH031TVK8-ZVG4KLVYyog)中的如下部分即可
<pre>
	git init
	git checkout -b source
	git add -A
	git commit -m "init blog"
	git remote add origin git@github.com:{username}/{username}.github.io.git
	git push origin source
</pre>


### 参考
- [如何用 GitHub 从零开始搭建一个博客？](https://mp.weixin.qq.com/s/sXH031TVK8-ZVG4KLVYyog)
- [Hexo 博客部署到腾讯云教程](https://bujige.net/blog/hexoBlog-deployed-server.html)
- [Hexo-添加分类及标签
](https://linlif.github.io/2017/05/27/Hexo%E4%BD%BF%E7%94%A8%E6%94%BB%E7%95%A5-%E6%B7%BB%E5%8A%A0%E5%88%86%E7%B1%BB%E5%8F%8A%E6%A0%87%E7%AD%BE/)
- [How Hexo](https://hexo.io/docs)




