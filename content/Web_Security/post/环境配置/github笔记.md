---
title: "Git&Github笔记"
date: 2022-11-05T14:55:49+08:00
tags: ["环境配置"]
draft: false
featured_image: "/images/github and git.jpg"
---

# 将本地项目推送到Repo

1. 在本项目的根目录下建立git仓库

   ```
   git init
   ```

2. 将项目中的所有文件添加到管道

   ``````
   git add .
   ``````

3. 将管道中的文件提交到仓库

   ``````
   git commit -m “注释语句”
   ``````

4. 去github网站中创建一个repo

5. 复制`clone or download`下SSH的地址，前提是在本地已经建立了SSH密钥和公钥

6. 将本地仓库关联到github上

   ``````
   git remote add origin git@github.com:xxx/xxx.git
   ``````

7. 要先pull一下，获取当前github上的repo的他人的更新。（如果是刚建的仓库可有可无）

   ``````
   git pull origin master
   ``````

8. 上传代码push

   ``````
   git push -u origin master
   ``````

# 分支

> 分支功能存在于，当我们的开发过程中接到一个或两个新的（不同的）功能需求时，我们需要同时开发这几个分支，最后可以merge到主分支上。

- github仓库中有一个默认的`master`分支
- 当然可以直接在github网页的仓库中创建一个新的分支，但其实在本地通过git here创建分支后再push上去会更加的方便

1. 查看目前的分支

   ``````
   git branch
   ``````

2. 创建一个新的分支

   ``````
   git branch 新的分支名字
   ``````

3. 切换分支

   ``````
   git checkout 要切换到的分支名字
   ``````

4. 合并分支

   ``````
   git branch 要合并的分支名字   #此时位置处于另一个被合并的分支下
   #或者
   git merge gh-pages --allow-unrelated-histories   
   ``````

   