## 创建仓库

- 初始化`git`仓库命令  
    `git init`  
    `git add .` 
- 增加文件  
    `git add filename1 [filename2, ...]`
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
- `git status`：查看当前**工作区**的状态，用于查看在你上次提交之后是否有对文件进行再次修改。

## 一些常用操作
### 想要找到所有的操作记录时如何进行操作

