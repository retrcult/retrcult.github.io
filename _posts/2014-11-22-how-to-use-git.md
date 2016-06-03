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

* [1](http://rogerdudler.github.io/git-guide/)

* [2](https://git-scm.com/docs/gittutorial)


