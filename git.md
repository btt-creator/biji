### git常用命令

参考链接：[gitlab（骨灰级入门）-CSDN博客](https://blog.csdn.net/weixin_47358139/article/details/126267861?spm=1001.2101.3001.6650.7&utm_medium=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromBaidu~Rate-7-126267861-blog-80681853.235^v43^pc_blog_bottom_relevance_base1&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromBaidu~Rate-7-126267861-blog-80681853.235^v43^pc_blog_bottom_relevance_base1&utm_relevant_index=14)

#### 拉取代码

1. 需要cd到一个刚建立的文件夹中。

   ` $ git init。`

2. 建立远程连接。

   `$ git remote add origin http://git.XXX.cn/XXX/XXX/XXX.git。`

3. 拉取远程分支，该操作只会下载到数据到本地仓库，而并不会合并，需要自己手动合并。

   - 直接拉取所有远程分支` $ git fetch --all。`

   - 拉取远程master分支 `$ git fetch origin master` 。

     以上两个分支拉取一个即可。

   - 当我们需要手动合并时，需要使用`$ git merge origin/master`

4. 查看所有分支

   `$ git branch -al`，如果不加-al只能看到当前分。

5. 拉取远程master代码到本地，**主要用于本地已经有仓库代码，用于更新。**

   `$ git pull origin master`。

6. 拉取远程其他分支到本地，例如tbt分支
   - 先需要本地切换到该分支上，如果本地没有该分支，将会创建一个文件夹，`$ git checkout -b tbt`。
   - 然后拉取该分支到本地tbt上， `$ git pull origin tbt`。

7. 获取源码，**用于第一次获取仓库代码。**

   `$ git clone [工程地址]`。

#### 提交代码

1. 需要进行Git全局设置+生成密钥

   ```bash
   $ git init #进行初始化
   $ git config --global user.name “用户名”
   $ git config --global user.email “邮箱”
   $ git config --list #查看配置好的用户名和密码添加远程仓库
   # 由于本地git仓库和Gitlab仓库之间传输是通过SSH进行加密的，所以配置验证信息
   $ ssh-keygen -t rsa -C "刚才输入的邮箱" #会要求确认路径和输入密码
   # C:\Users\lshel\.ssh会生成公钥和私钥文件
   # 使用cat进行查看
   $ cat ~/.ssh/id_rsa.pub
   ```

   

2. 将上述SSH复制到gitlab里面的SSH Keys这个模块。

3. 本地已有项目，上传上去

   ```bash
   $ git init #生成本地./git文件，用于跟踪和管理历史版本
   $ git add . #上传所有的文件到暂存区
   $ git add * #上传不包括隐藏文件的所有文件，并且较好的上传未追踪的文件，即还没被仓库记录下来的
   $ git commit -m ”描述上传的文件“ #将暂存区的文件提交到工作区 
   $ git commit -s #添加用户提交信息，用于版本维护
   $ git status #查看是否还有文件未提交
   $ git push origin master #提交到远程仓库
   ```

4. 上传到其他分支

   ```bash
   #全局设置如1.
   $ git branch -a # 查看所有分支
   $ git branch test #创建test分支 
   $ git checkout test #切换到test分支
   $ git remote add origin https://gitlab.com/helenls/sca_apitest01.git # 关联远程仓库，远程仓库的默认叫法就是origin
   $ git push origin test:test #本地分支（冒号前面的分支）于远程分支同名（冒号后面的分支，如果分支不存在，将会创建分支）
   $ git push origin test # 将本地内容上传到远程仓库分支。
   ```

   

#### 其他命令

1. 变基操作，参考[git rebase详解（图解+最简单示例，一次就懂）-CSDN博客](https://blog.csdn.net/weixin_42310154/article/details/119004977)

- 使用`$ git rebase <remote>/<branch> #例如：git rebase origin/master`,用于将当前分支上的更改应用在其他指定远程分支上。
- 如果存在冲突，则需要解决冲突，然后使用`$ git  add <file>`用于标记该冲突已经解决，用于更新git索引以此来准备重新提交。
- 如果解决冲突过程中，准备删除某些文件，则可以使用`$ git rm <file>`
- 当所有的冲突解决完成，可以使用`$ git rebase --continue`继续变基的过程。

2. cherry-pick操作，可以实现在不同的分支上共享一个特定的功能、将一个特定的修复从一个分支快速应用到另一个分支。

- `$ git cherry-pick --abort`用于停止操作，并回滚到最开始的状态，且保值‘HEAD’指向开始时的最后一个提交。

3. `$ git reset --hard HEAD^`用于撤销最近一次提交并清理当前工作目录。
4. 如果需要撤销对某些文件的修改，可以使用`$ git checkout --file`。
5. `$ git status`提供了有关当前工作目录和暂存区状态的消息，显示了哪些文件被修改，哪些文件是暂存的，哪些文件还未跟踪。
6. `$ git diff`展示了文件的具体更改内容，包括暂存区以及未跟踪的文件。