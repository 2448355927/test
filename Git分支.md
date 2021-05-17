# Git 分支

### 一、简介

Git 把我们之前每次提交的版本串成一条时间线，这条时间线就是一个分支。截止到目前只有一条时间线，在git里，这个分支叫主分支，即master分支。HEAD严格来说不是指向提交，而是指向master，master才是指向提交的，所以，HEAD指向的就是当前分支。
(1) 一开始的时候，master分支是一条线，git用master指向最新的提交，再用HEAD指向master，就能确定当前分支，以及当前分支的提交点：
![img](https://img-blog.csdnimg.cn/20190222143941869.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNTE3MjIw,size_16,color_FFFFFF,t_70)

每次提交，master都会自动向前一步，就每次提交之后分支会越来越长。

（2）当我们创建新的分支，dev，git新建了一个指针，并且会与master指向相同的提交，再把head指向dev。

![img](https://img-blog.csdnimg.cn/20190222144045155.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNTE3MjIw,size_16,color_FFFFFF,t_70)

git创建一个分支很快，因为他只是增加一个dev指针以及改变了head的指向，工作区的文件没有任何变化。

（3）但是从现在开始的话，对工作区的修改和提交都是针对dev分支来操作了，新提交一次后，dev指针就会往前移动一步，但是master指针不会变动。

![img](https://img-blog.csdnimg.cn/20190222144114527.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNTE3MjIw,size_16,color_FFFFFF,t_70)

（4）当我们再dev上完成工作时，就可以把dev 和master合并起来，就把master 指向的dev  的当前提交。



### 二、分支实例

git branch     ：查看分支

git checkout  分支名  ：切换分支

git branch  -b  ： 创建分支

git branch  -d  ：删除分支 

### 三、分支冲突

当一个分支的文件被修改还未提交合并到主分支时，被另一个分支修改，此时系统就不知道以哪个分支的内容为准来提交到主分支，此时就会产生冲突。

##### 1）我们现在再创建一个新的分支 dev。

```java
ubantu@ubantu-virtual-machine:~/git_folder$ git checkout -b dev
 Switched to a new branch 'dev' 
ubantu@ubantu-virtual-machine:~/git_folder$ 
```



##### 2）修改first.txt的内容，并且进行提交。

```java
ubantu@ubantu-virtual-machine:~/git_folder$ cat first.txt
this is my first data
this is my second data
this is my third data
this is my forth data
add one data
add another data
ubantu@ubantu-virtual-machine:~/git_folder$ git add first.txt
ubantu@ubantu-virtual-machine:~/git_folder$ git commit -m "dev branch second submit"
[dev d9e0e8c] dev branch second submit
 1 file changed, 1 insertion(+)
ubantu@ubantu-virtual-machine:~/git_folder$
```



##### 3）再切换回master分支。

```java
ubantu@ubantu-virtual-machine:~/git_folder$ git checkout master 
Switched to branch 'master'
ubantu@ubantu-virtual-machine:~/git_folder$ 
```



##### 4）在master分支上给first.txt添加一行新的内容。

```java
ubantu@ubantu-virtual-machine:~/git_folder$ cat first.txtthis is my first data
this is my second data
this is my third data
this is my forth data
add one data
add another data in master
ubantu@ubantu-virtual-machine:~/git_folder$ git add first.txt
ubantu@ubantu-virtual-machine:~/git_folder$ git commit -m "master branch submit"
[master c19bc13] master branch submit
 1 file changed, 1 insertion(+)
ubantu@ubantu-virtual-machine:~/git_folder$
```



到现在，master分支和dev分支就都有了新的提交。

![img](https://img-blog.csdnimg.cn/20190222162948970.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNTE3MjIw,size_16,color_FFFFFF,t_70)



在这种情况下，git就无法进行“快速合并”，只能试图把各自的修改合并起来，就是这种合并就会发生冲突。

##### 5）尝试如下命令尝试将dev分支合并到master分支上来。

```java
ubantu@ubantu-virtual-machine:~/git_folder$ git merge dev
Auto-merging first.txt
CONFLICT (content): Merge conflict in first.txt
Automatic merge failed; fix conflicts and then commit the result.
ubantu@ubantu-virtual-machine:~/git_folder$

```

此时便会提示我们自动合并产生冲突，并且告诉我们发生冲突的文件为first.txt，需要手动修正后再提交

##### 6）git status 也会告诉我们发生冲突的文件。

```java
ubantu@ubantu-virtual-machine:~/git_folder$ git status
On branch master
You have unmerged paths.
  (fix conflicts and run "git commit")

Unmerged paths:
  (use "git add <file>..." to mark resolution)

	both modified:   first.txt

no changes added to commit (use "git add" and/or "git commit -a")
ubantu@ubantu-virtual-machine:~/git_folder$ 
```

1、告诉我们有尚未合并的路径，需要解决冲突后运行git commit命令。

2、有两个人修改了first.txt文件。

##### 7）查看code.txt的内容

```java
ubantu@ubantu-virtual-machine:~/git_folder$ cat first.txt 
this is my first data
this is my second data
this is my third data
this is my forth data
add one data
<<<<<<< HEAD
add another data in master
=======
add another data
>>>>>>> dev
ubantu@ubantu-virtual-machine:~/git_folder$
```

git会用以下标记区分不同的分支内容，我们手动删除即可。

<<<<<<<， =======， >>>>>>>

##### 8）修改后保存，再提交。

```java
ubantu@ubantu-virtual-machine:~/git_folder$ cat first.txt
this is my first data
this is my second data
this is my third data
this is my forth data
add one data
add another data in master
add another data
ubantu@ubantu-virtual-machine:~/git_folder$ 
```

```java
ubantu@ubantu-virtual-machine:~/git_folder$ git commit -m "solve merge conflict" 
[master 194ad75] solve merge conflict
ubantu@ubantu-virtual-machine:~/git_folder$
```

##### 9）现在就可以看到master分支和git分支变成如下图所示了。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190222165746269.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNTE3MjIw,size_16,color_FFFFFF,t_70)

##### 10) 用带参数的git log也可以看到分支的合并情况

```java
ubantu@ubantu-virtual-machine:~/git_folder$ git log --graph --pretty=oneline
*   194ad7508eb5656a243bf93ca36f6c23412566e1 solve merge conflict
|\  
| * d9e0e8cc0026d256043938ef38ecfe9a1e11bbc0 dev branch second submit
* | c19bc130daf086d984aa1a7395a95a2a580d1998 master branch submit
|/  
* 3f6bc28880f2edd636e5b0527e18f8225bef4209 dev branch submit
* d7df4195d600a5c9d414519c757e91dae50df39c 删除文件second.txt
* f696849773147218c4d72dfb62932be47a079bbc 版本4
* 724a2201044ae9f6fc5f2905fd3b032001a71d51 版本3
* ca3a776239c5532e728ffae0be467584a12215b0 版本2
* 22355f7dffd474ed51ac033e825c67b4393eaa87 版本1
ubantu@ubantu-virtual-machine:~/git_folder$
```

##### 11）完成工作时，就可以删除dev分支了。

```java
ubantu@ubantu-virtual-machine:~/git_folder$ git branch -d dev
Deleted branch dev (was d9e0e8c).
ubantu@ubantu-virtual-machine:~/git_folder$ git branch
* master
ubantu@ubantu-virtual-machine:~/git_folder$ 
```

### 四、分支的管理

通常，合并分支时，如果可能的话，git 会采用 fast forward模式，但是有些时候快速合并并不能成功而且没有文件冲突，这个时候git就会帮你合并，并且做一次新的提交。但是在这种情况下，删除分支后，有可能会丢失一部分信息。（再分支上新建再修改某一个文件内容之后，还未合并到主分支之前，此时主分支修改了其他文件，再合并就会出现信息丢失的情况）

##### 1）创建切换到dev分支下。

```java
ubantu@ubantu-virtual-machine:~/git_folder$ git checkout -b dev
Switched to a new branch 'dev'
ubantu@ubantu-virtual-machine:~/git_folder$
```

##### 2）新建一个first2.txt并且编辑内容如下，提交一个commit。

```java
ubantu@ubantu-virtual-machine:~/git_folder$ vim first2.txt
ubantu@ubantu-virtual-machine:~/git_folder$ cat first2.txt 
first data commit
ubantu@ubantu-virtual-machine:~/git_folder$ git add first2.txt 
ubantu@ubantu-virtual-machine:~/git_folder$ git commit -m "create first2.txt file"
[dev 1f40b6f] create first2.txt file
 1 file changed, 1 insertion(+)
 create mode 100644 first2.txt
ubantu@ubantu-virtual-machine:~/git_folder$
```

##### 3）切换回master分支，编辑first.txt并进行一个提交。

```java
ubantu@ubantu-virtual-machine:~/git_folder$ git checkout master 
Switched to branch 'master'
ubantu@ubantu-virtual-machine:~/git_folder$ echo "add another data second" >> first.txt
ubantu@ubantu-virtual-machine:~/git_folder$ cat first.txt
this is my first data
this is my second data
this is my third data
this is my forth data
add one data
add another data in master
add another data
add another data second
ubantu@ubantu-virtual-machine:~/git_folder$ git add first.txt
ubantu@ubantu-virtual-machine:~/git_folder$ git commit -m "add new data"
[master 510d39d] add new data
 1 file changed, 1 insertion(+)
ubantu@ubantu-virtual-machine:~/git_folder$
```

##### 4）合并dev的内容到master分支。

```java
ubantu@ubantu-virtual-machine:~/git_folder$ git merge dev
```

##### 5）出现如下提示，这是因为这次不能快速合并，所以git提示输入合并并说明信息，输入之后合并之后git会自动创建一个新的提交。

```
  1 Merge branch 'dev'
  2 
  3 # Please enter a commit message to explain why this merge is necessary,
  4 # especially if it merges an updated upstream into a topic branch.
  5 #
  6 # Lines starting with '#' will be ignored, and an empty message aborts
  7 # the commit.   
```

我们需要修改第一行，写上我们提交的内容

```java
  1 合并dev分支
  2 
  3 # Please enter a commit message to explain why this merge is necessary,
  4 # especially if it merges an updated upstream into a topic branch.
  5 #
  6 # Lines starting with '#' will be ignored, and an empty message aborts
  7 # the commit. 
```

```java
ubantu@ubantu-virtual-machine:~/git_folder$
Merge made by the 'recursive' strategy.
 first2.txt | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 first2.txt
ubantu@ubantu-virtual-machine:~/git_folder$ 
```

保存并退出：wq（如果不是编辑器模式则用ctrl+x然后输入y退出）

##### 6）查看日志

```java
ubantu@ubantu-virtual-machine:~/git_folder$ git log --pretty=oneline
4189c3f26784405d7285c49a02e0be13123bb1c1 合并dev分支
62d02bb7aafae14bc625190c00e26aab544e1b93 add new data
```

##### 7）删除dev分支。

```java
ubantu@ubantu-virtual-machine:~/git_folder$ git branch -d dev
Deleted branch dev (was 7c13d45).
ubantu@ubantu-virtual-machine:~/git_folder$
```

## 如果要强制禁用fast forward模式，git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。

##### 1）创建并切换到dev分支。

```java
ubantu@ubantu-virtual-machine:~/git_folder$ git checkout -b dev
Switched to a new branch 'dev'
ubantu@ubantu-virtual-machine:~/git_folder$ 
```

##### 2）修改first.txt内容，并提交一个commit。

```Java
ubantu@ubantu-virtual-machine:~/git_folder$ echo "add another data third" >> first.txt 
ubantu@ubantu-virtual-machine:~/git_folder$ cat first.txt 
this is my first data
this is my second data
this is my third data
this is my forth data
add one data
add another data in master
add another data
add another data second
add another data third
ubantu@ubantu-virtual-machine:~/git_folder$ git add first.txt 
ubantu@ubantu-virtual-machine:~/git_folder$ git commit -m "第三次添加数据"
[dev 7ee2d8f] 第三次添加数据
 1 file changed, 1 insertion(+)
ubantu@ubantu-virtual-machine:~/git_folder$
```

##### 3）切换回master分支。

```java
ubantu@ubantu-virtual-machine:~/git_folder$ git checkout master 
Switched to branch 'master'
ubantu@ubantu-virtual-machine:~/git_folder$ 
```

##### (4) 准备合并dev分支，请注意–no-ff参数，表示禁用Fast forward：

```Java
ubantu@ubantu-virtual-machine:~/git_folder$ git merge --no-ff -m "禁用fast-forward合 并" dev 
Merge made by the 'recursive' strategy.
 first.txt | 1 +
 1 file changed, 1 insertion(+)
ubantu@ubantu-virtual-machine:~/git_folder$
```

- 因为本次合并要创建一个新的commit，所以加上-m参数，把commit描述写进去。

##### (5) 不使用Fast forward模式，merge后就如下图



![img](https://img-blog.csdnimg.cn/20190225114545747.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNTE3MjIw,size_16,color_FFFFFF,t_70)

### 五、bug 分支

软件开发中，bug就像家常便饭一样。有了bug就需要修复，在git中，由于分支是如此的强大，所以，每个bug都可以通过一个新的临时分支来修复，修复后，合并分支，然后将临时分支删除。

##### (1) 当你接到一个修复一个代号001的bug的任务时，很自然地，你想创建一个分支bug-001来修复它，但是，等等，当前正在dev上进行的工作还没有提交（模拟正在操作first.txt文件）：

```java
ubantu@ubantu-virtual-machine:~/git_folder$ git checkout -b dev
ubantu@ubantu-virtual-machine:~/git_folder$ echo "add another data fouth" >> first.txt
ubantu@ubantu-virtual-machine:~/git_folder$ cat first.txt
this is my first data
this is my second data
this is my third data
this is my forth data
add one data
add another data in master
add another data
add another data second
add another data third
add another data fouth
ubantu@ubantu-virtual-machine:~/git_folder$ git status
On branch dev
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   first.txt

no changes added to commit (use "git add" and/or "git commit -a")
ubantu@ubantu-virtual-machine:~/git_folder$
```

##### (2) git还提供了一个stash功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作

```java
ubantu@ubantu-virtual-machine:~/git_folder$ git stash
Saved working directory and index state WIP on master: 07a63f9 禁用fast-forward合并
HEAD is now at 07a63f9 禁用fast-forward合并
ubantu@ubantu-virtual-machine:~/git_folder$ 
```

##### (3) 首先确定要在哪个分支上修复bug，假定需要在master分支上修复，就从master创建并切换到临时分支bug-001，加入出错内容位于first2.txt文件内：

```java
ubantu@ubantu-virtual-machine:~/git_folder$ git checkout master 
Switched to  'master'
ubantu@ubantu-virtual-machine:~/git_folder$ git checkout -b bug-001
Switched to a new branch 'bug-001'
ubantu@ubantu-virtual-machine:~/git_folder$
```

##### (4) 现在修复bug,把 first2.txt文件`first data commit` 删掉，然后提交。

```Java
ubantu@ubantu-virtual-machine:~/git_folder$ cat first2.txt
first data commit
ubantu@ubantu-virtual-machine:~/git_folder$ cat first2.txt
ubantu@ubantu-virtual-machine:~/git_folder$ git add first2.txt
ubantu@ubantu-virtual-machine:~/git_folder$ git commit -m "修复bug-001"
[bug-001 d63018d] 修复bug-001
 1 file changed, 1 deletion(-)
ubantu@ubantu-virtual-machine:~/git_folder$
```

##### (5) 修复完成后，切换到master分支，并完成合并，最后删除bug-001分支。

```java
ubantu@ubantu-virtual-machine:~/git_folder$ git checkout master
Switched to  'master'
ubantu@ubantu-virtual-machine:~/git_folder$ git merge --no-ff -m "修复bug-001" bug-001
 Merge made by the 'recursive' strategy.
 first.txt | 1 -
 1 file changed, 1 deletion(-)
Switched to a new branch 'bug-001'
ubantu@ubantu-virtual-machine:~/git_folder$ git branch -d bug-001
Deleted branch dev (was 7c13d45).
```

##### (6) 现在bug-001修复完成，是时候接着回到dev分支干活了！

```java
ubantu@ubantu-virtual-machine:~/git_folder$ git checkout dev
Switched to branch 'dev'
ubantu@ubantu-virtual-machine:~/git_folder$ git status
On branch dev
nothing to commit, working directory clean
```

##### (7) 工作区是干净的，刚才的工作现场存到哪去了？用git stash list命令看看：

```java
ubantu@ubantu-virtual-machine:~/git_folder$ git stash list
stash@{0}: WIP on master: 07a63f9 禁用fast-forward合并
ubantu@ubantu-virtual-machine:~/git_folder$
```

- 作业现场还在，git把stash内容存在某个地方了，但是需要恢复一下.

```Java
ubantu@ubantu-virtual-machine:~/git_folder$ git stash pop
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   first.txt

no changes added to commit (use "git add" and/or "git commit -a")
Dropped refs/stash@{0} (9e0bfcae9767c6e805a0243aaf7070464c1cfea0)
ubantu@ubantu-virtual-machine:~/git_folder2$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   first.txt

no changes added to commit (use "git add" and/or "git commit -a")
ubantu@ubantu-virtual-machine:~/git_folder$ 
```

