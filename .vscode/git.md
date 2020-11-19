## 创建仓库
- 初始化`git`仓库命令  
    `git init`  
    `git add .` 
- 增加文件  
    `git add filename1 [filename2 ...]`
- 提交项目  
    `git commit -m '提交信息'`
- 增加远程仓库  
    `git remote add origin 'https://github.com/applesack'`
- 将本地代码推送到远程仓库  
    `git push -u origin master`
---
## .gitignore
上传至远程仓库需要忽略某类文件时用到`。gitignore`，创建`.gitignore`在项目目录下。  
**语法**
1. 每个类型的文件独占一行。
2. 忽略单个文件，直接将该文件的路径写入`。gitignore`。  
    例如：`test.txt`

---
## 一些常用的命令含义
- `git init`： 初始化一个git仓库
- `git add .`： *使用它会把工作时的所有变化提交到**暂存区**，包括文件内容修改(modified)以及新文件(new)，但不包括被删除的文件。*
- `git commit -m 提交信息`：将**暂存区**的更改提交到**本地仓库**，`-m`参数表示提交时附带的信息。
- `git status`：查看当前**工作区**的状态，用于查看在你上次提交之后是否有对文件进行再次修改。*加上`-s`参数可以查看更精简的信息*
- `git log`：查看**远程仓库**的提交记录，*参数`--author [作者名]`可以查看某作者的提交记录*

## 一些常用操作
### 配置git用户名和邮箱
1. 使用`git --version`检查此电脑上git环境是否配置成功。
2. 输入`git config --global user.name '用户名'`，全局配置用户名
3. 输入`git config --global user.email '邮箱'`
4. 检查用户名和邮箱是否配置成功，输入`git config --global --list`
### 想要修改项目中的文件时如何进行操作
1. 输入`git status`查看当前项目的状态，查看被修改的文件信息。
2. 输入`git add [被修改的文件的文件名]`，将修改过的文件保存到暂存区，要保存多个文件时，每个文件用空格隔开。
3. `git commit -m [提交信息]`
### 想要删除不需要的文件时如何操作
1. **第一种方式手动删除**  
直接手动删除需要删除的文件，这个时候使用`git status`命令查看会发现有红色的提示信息。使用命令`git add .`同步暂存区的记录。
2. **第二种方式**
### 想要找到所有的操作记录时如何进行操作

