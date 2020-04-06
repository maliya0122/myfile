## git的使用

git分支：

https://juejin.im/post/5aa28b9af265da2381553a1f

### 1.基本使用

```js
//1.初始化
git init

//2.提交到暂缓区
git  add  project.md

//3.检查状态
git status

//4.提交到仓库
git commit -m '2020 3/24 鹿哥的货物 10吨'

//5.查看日志
git log
```

### 2.版本回退

```js
//1.回滚当上次提交代码之前
git  reset --hard HEAD^
    
//2.查看一下我们管理员执行命令的记录
git reflog

//3.回滚到哪个版本
git reset --hard 3cc34bb(提交 ID) 
```

### 3.连接远程仓库

```js
git init //初始化mymall文件夹，多了.git文件夹
git touch READMEgit //可以不要
git add README //可以不要
git commit -m 'first commit'  //提交文件
git remote add origin https://github.com/maliya0122/supermall.git
git push -u origin master
```

### 4.提交分支

```js
git checkout -b user			 //创建并切换到user分支
git add .						 //将文件添加到仓库
git commit -m '完成用户管理模块'	//提交到本地仓库
git push -u origin user			//将user分支推送到github（首次）

git checkout master				//切换到主分支
git branch						//查看分支
git merge user					//合并本机电脑上的分支
git push 						//将master推送到github

git checkout login				//切换到login分支
```

### 5.解决冲突

```js
git fetch --all          //将远程主机的更新全部取回本地
```

