# git命令
### git安装
sudo apt-get install git
### git创建本地仓库
git init
### git配置个人信息
git config user.name 'zhangsan'   
git config user.email 'zhangsan@163.com'
### 查看文件状态
git status   
红色文件名表示在工作区的新建文件或新修改文件   
绿色文件名表示在暂存区   
### 将工作区文件添加到暂存区  
添加指定文件
git add 文件名
添加项目中所有的文件  
git add .
### 将暂存区文件提交到本地仓库区
git commit -m '版本描述信息'
### 将工作区文件添加到本地仓库区
git commit -am '版本描述信息'
### 查看历史版本
git log
git reflog
### 回退版本
git reset --hard 版本号
### 撤销修改
撤销工作区代码  
git checkout 文件名  
撤销暂存区代码到工作区  
git reset HEAD 文件名  
仓库区不能撤销  
### 对比版本
git diff HEAD --文件名
### 删除文件
git rm 文件名
git commit -m '删除描述'
### 远程克隆仓库
git clone url
### 推送到远程仓库
git push
### 同步服务器代码
git pull
### 查看分支
git branch
### 创建分支并切换
git checkout -b dev
### 推送本地分支到远程
git push -u origin dev
### 合并分支
切换到主分支   
git master   
将dev分支合并在master分支   
git merge dev
推送分支到远程   
git push
### 标签
记录大版本    
git tag -a '标签名' -m '标签描述'   
git push origin 标签名
