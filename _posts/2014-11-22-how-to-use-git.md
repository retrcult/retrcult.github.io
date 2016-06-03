---
layout: post
title: How to Use Git
tags: [Git, GitHub]
---


<!--more-->



Git Config Setup
-----------

The `git config` command can be used to change your Git configuration.

{% highlight bash %}
$ git config user.name "John Doe"
# To set your username for a specific repository
# Type the command in the root folder of your repository
$ git config user.name
# Verify the setting
  John Doe

$ git config --global user.name "Billy Everyteen"
# To set your username for every repository on your computer
$ git config --global user.name
# Confirm that you have set your username correctly
  Billy Everyteen
$ git config --global user.email "john_doe@example.com"
# Set your email address
$ git config --global user.email
# Verify
{% endhighlight %}

{% highlight bash %}
$ git config color.ui true
# Use colorful git output

$ git config format.pretty oneline
# Show log on just one line per commit
{% endhighlight %}


### Troubleshooting


#### My name doesn't show up on GitHub

If the email used in a commit matches a verified GitHub user account, the account's username is used, instead of the username set by Git.

If you have any local copies of personal repositories you have created or forked, you can find your username in the URL of the remote repository.

{% highlight bash %}
$ git remote -v
{% endhighlight %}


#### New commits aren't using the right name

If `git config user.name` reports the correct username for the repository you're viewing, but your commits are using the wrong name, your environment variables may be overriding your username.

Make sure you have not set the `GIT_COMMITTER_NAME` or `GIT_AUTHOR_NAME` variables. You can check their values with the following command:

{% highlight bash %}
$ echo $GIT_COMMITTER_NAME
# Print the value of GIT_COMMITTER_NAME
$ echo $GIT_AUTHOR_NAME
# Print the value of GIT_AUTHOR_NAME

$ echo $GIT_COMMITTER_EMAIL
$ echo $GIT_AUTHOR_EMAIL

# If you notice a different value, you can change it like so:
$ export GIT_AUTHOR_NAME = "Billy Everyteen"
$ export GIT_COMMITTER_NAME = "$GIT_AUTHOR_NAME"
$ source ~/.bashrc
{% endhighlight %}


#### My old commits still have my old username

Changing your username in Git only affects commits that you make after your change.
To rewrite your old commits, you can use `git filter-branch` to change the repository history to use your new username.




---------------------------------------


A Common Git Workflow
-----------

> This tutorial explains how to import a new project into Git, make changes to it, and share changes with other developers.

By default, the first branch on any git project is called "**master**".

Your local repository consists of three "<i>trees</i>" maintained by git. the first one is your **Working Directory** which holds the actual files. the second one is the **Index** which acts as a staging area and finally the **HEAD** which points to the last commit you've made.

![trees]({{ site.baseurl }}/images/2016/06/trees.png)



### Importing a new project

Assume you have a tarball <i>project.tar.gz</i> with your initial work. You can place it under Git revision control as follows.

{% highlight bash %}
$ tar xzf project.tar.gz
$ cd project
$ git init
  Initialized empty Git repository in .git/

$ git add .
# Tell Git to take a snapshot of the contents of all files under the current directory
# This snapshot is now stored in a temporary staging area which Git calls the "index"

$ git commit
# This will prompt you for a commit message
# By submitting a commit message for your changes, you can permanently store the contents of the index in the repository

# Alternatively, instead of running `git add` beforehand, you can use
$ git commit -am "Hotfix"
# which will automatically notice any modified (but not new) files, add them to the index, and commit a message says "Hotfix", all in one step

# To see what is about to be committed, using
$ git diff --cached
# Without `--cached`, `git diff` will show you any changes that you’ve made but not yet added to the index

# You can also get a brief summary of the situation with `git status`
$ git status
  On branch master
  Changes to be committed:
    (use "git reset HEAD <file>..." to unstage)
      modified:   gitAddSomeFile
  Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git checkout -- <file>..." to discard changes in working directory)
      modified:   someFileInGitThatHasBeenModified
  Untracked files:
    (use "git add <file>..." to include in what will be committed)
      newFileThatIsNotYetInGit
{% endhighlight %}



### Viewing Project History

{% highlight bash %}
# At any point you can view the history of your changes using
$ git log

# Often the overview of the change is useful to get a feel of each step
$ git log --stat --summary
{% endhighlight %}



### Managing Branches

Branches are cheap and easy, so this is a good way to try something out.

{% highlight bash %}
$ git branch experimental
# Create a new branch named "experimental"

$ git branch
# Get a list of all existing branches
# The asterisk marks the branch you are currently on
    experimental
  * master

$ git checkout experimental
# To switch to the experimental branch

# (edit file)
$ git commit -a
$ git checkout master
# (edit file)
$ git commit -a

# At this point the two branches have diverged, with different changes made in each
# To merge the changes made in experimental into master, run
$ git merge experimental

# If the changes don’t conflict, you’re done. If there are conflicts, markers will be left in the problematic files showing the conflict
  CONFLICT (content): Merge conflict in conflictFile
  Automatic merge failed; fix conflicts and then commit the result.

# See which files are unmerged at any point after a merge conflict
$ git status
# or
$ git diff
# If you want to use a graphical tool to resolve these issues, you can run `git mergetool`

# Once you’ve edited the files to resolve the conflicts
$ git commit -a

# At this point you could delete the experimental branch with
$ git branch -d experimental
# This command ensures that the changes in the experimental branch are already in the current branch

# If you develop on a branch crazy-idea, then regret it, you can always delete the branch with
$ git branch -D crazy-idea

{% endhighlight %}




Using Git for Collaboration
-----------











Git Workflow 2
-----------

git log --graph
















Ref:

* [1](https://help.github.com/articles/setting-your-username-in-git/)


http://rogerdudler.github.io/git-guide/








### 查看公式代码

可以在渲染完成的公式上右键点击唤出菜单：**Show Math As** > **Tex Commands**。

菜单中还提供了设置显示效果和渲染模式的选项。



### 在线编辑预览

推荐使用 [StackEdit](https://stackedit.io/editor) 学习 MathJax 的语法，它支持 Markdown 和 MathJax。

使用 [Detexify](http://detexify.kirelabs.org/classify.html)，你可以在网页上画出符号，Detexify 会给出相似的符号及其代码，但是不能保证它给出的符号可以在 MathJax 中使用，你可以参考 [Supported LaTeX commands | MathJax](http://docs.mathjax.org/en/latest/tex.html#supported-latex-commands) 或 [$\TeX$ Commands available in MathJax](http://www.onemathematicalcat.org/MathJaxDocumentation/TeXSyntax.htm) 以确定 MathJax 是否支持此符号。


{% highlight js %}
_paq.push(['setUserId', USER_ID_HERE]);
{% endhighlight %}


https://confluence.atlassian.com/bitbucket/git-and-mercurial-reference-795935901.html
=======
{% highlight bash %}

git config --global user.name "John Doe"

$ git config user.name
# View the setting
YOUR_USERNAME
git config --global user.email johndoe@example.com
{% endhighlight %}


## Clone your repository to your local system
git clone https://by@bitbucket.org/by/example.git



## Make changes to the repository source
git add -A
git rm
// Undo `git add` before commit
git reset <file>
git status
git commit -m "add something"

git push -u origin master



## Pull changes from a remote repository
git pull --all
// or
git pull origin master



## Working with branch

// List all the branches
git branch -a

// Create a branch
git branch some-new-feature

// Switched to branch `some-new-feature`
git checkout some-new-feature

// Work hard and commit
git commit -am "making a change in a branch"

// Merge your branch
git checkout master
git merge some-new-feature

! CONFLICT (content): Merge conflict in ***
! Automatic merge failed; fix conflicts and then commit the result.
// See which files are unmerged at any point after a merge conflict
git status

//After you’ve resolved each of these sections in each conflicted file
// If you want to use a graphical tool to resolve these issues, you can run `git mergetool`
git add *.*
git status
git commit -m "retry"


// Get changes from master into branch in git
git fetch origin
git checkout some-new-feature
git merge master
// or
git rebase master
git reset --hard HEAD~

! needs merge
! error: you need to resolve your current index first
// Reset your git merge
git reset --merge
// Switch to the dev branch
git checkout some-new-feature
// Discard everything from the master branch and keeping everything from `some-new-feature`
git merge -s ours master
// Switch back to the `master` branch:
git checkout master
// Leaves you with `master` exactly as `some-new-feature` was
git merge some-new-feature

! If you don't plan on using `some-new-feature` anymore, you can delete the branch.
git branch -d some-new-feature
// Deleted branch some-new-feature (was e3b7732).

! When you delete some-new-feature, you can still access the branch from master using a commit id. For example, if you want to undo the changes added from some-new-feature, use the commit id you just received to go back to that branch.

git status
// On branch master
// Your branch is ahead of 'origin/master' by 1 commit.
//   (use "git push" to publish your local commits)
// nothing to commit, working directory clean



## Undo a commit and redo

git commit -m "Something terribly misguided"
// undo the commit but keep your changes
git reset --soft HEAD~
// or nuke the commit and never see it again
git reset --hard HEAD~
// edit files as necessary
git add
git commit -c ORIG_HEAD

// Open your default commit message editor pre-populated with the last commit message.
git commit --amend

// Suppose you destroy a commit as in the first example, but then discover you needed it after all
git reflog
// Find the commit you destroyed, and do this
git checkout HEAD@{x} // x is the number of commits t go back
// or
git checkout -b <new branch> <commit_id>

// http://stackoverflow.com/questions/34519665/how-to-move-head-back-to-a-previous-location/34519716

