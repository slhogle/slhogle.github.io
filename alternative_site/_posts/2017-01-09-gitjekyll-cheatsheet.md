---
layout:     post
title:      Git and Jekyll Cheatsheet
date:       2017-01-09
summary:    Useful commands for using git/github and jekyll. Tailored to hosting a blog.
---
### THE SHORT VERSION
Recipe for setting up a repository quick and dirty
{% highlight bash %}
echo "# my markdown info" >> README.md
git init
git add README.md
git commit -m "first commit"
## adding using https protocol
git remote add origin https://github.com/MYUSERNAME/MY-NEW-REPO.git
## RECOMMENDED - or using ssh (after you have added a proper ssh key)
git remote add origin git@github.com:MYUSERNAME/MY-NEW-REPO
git push -u origin master
# …or push an existing repository from the command line
git remote add origin https://github.com/MYUSERNAME/MY-NEW-REPO.git
git push -u origin master
{% endhighlight %}

For updating a Jekyll based blog hosted on github pages
{% highlight bash %}
jekyll serve --watch
# take a look at the changes you make live
jekyll build 
# uses jekyll to convert/build html version
# rendered files located in _site
git add --all
# add all changes in current repo to be staged
git commit -m "my commit message"
# create a message for the commit
git push
# changes should be live at the github pages site
{% endhighlight %}

Adapted from [here](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf)

### CONFIGURE TOOLING
Configure user information for all local repositories
{% highlight bash %}
git config --global user.name "[name]"
# Sets the name you want atached to your commit transactions
git config --global user.email "[email address]"
# Sets the email you want atached to your commit transactions
git config --global color.ui auto
# Enables helpful colorization of command line output
{% endhighlight %}

### CREATE REPOSITORIES
Start a new repository or obtain one from an existing URL
{% highlight bash %}
git init [project-name]
# Creates a new local repository with the specified name
git clone [url]
# Downloads a project and its entire version history
{% endhighlight %}

### MAKE CHANGES
Review edits and craft a commit transaction
{% highlight bash %}
git status
# Lists all new or modified files to be commited
git add [file]
# Snapshots the file in preparation for versioning
git reset [file]
# Unstages the fiale, but preserve its contents
git diff
# Shows file differences not yet staged
git diff --staged
# Shows file differences between staging and the last file version
git commit -m "[descriptive message]"
# Records file snapshots permanently in version history
{% endhighlight %}

### GROUP CHANGES
Name a series of commits and combine completed efforts
{% highlight bash %}
git branch
# Lists all local branches in the current repository
git branch [branch-name]
# Creates a new branch
git checkout [branch-name]
# Switches to the specified branch and updates the working directory
git merge [branch]
# Combines the specified branch’s history into the current branch
git branch -d [branch-name]
# Deletes the specified branch
{% endhighlight %}

### REFACTOR FILENAMES
Relocate and remove versioned files
{% highlight bash %}
git rm [file]
# Deletes the file from the working directory and stages the deletion
git rm --cached [file]
# Removes the file from version control but preserves the file locally
git mv [file-original] [file-renamed]
# Changes the file name and prepares it for commit
{% endhighlight %}

### SUPPRESS TRACKING
Exclude temporary files and paths
{% highlight bash %}
*.log
build/
temp-*
# A text file named .gitignore suppresses accidental versioning of
# files and paths matching the specified paterns
git ls-files --other --ignored --exclude-standard
# Lists all ignored files in this project
{% endhighlight %}

### SAVE FRAGMENTS
Shelve and restore incomplete changes
{% highlight bash %}
git stash
# Temporarily stores all modified tracked files
git stash list
# Lists all stashed changesets
git stash pop
# Restores the most recently stashed files
git stash drop
# Discards the most recently stashed changeset
{% endhighlight %}

### REVIEW HISTORY
Browse and inspect the evolution of project files
{% highlight bash %}
git log
# Lists version history for the current branch
git log --follow [file]
# Lists version history for a file, including renames
git diff [first-branch]...[second-branch]
# Shows content differences between two branches
git show [commit]
# Outputs metadata and content changes of the specified commit
{% endhighlight %}

### REDO COMMITS
Erase mistakes and craft replacement history
{% highlight bash %}
git reset [commit]
# Undoes all commits afer [commit], preserving changes locally
git reset --hard [commit]
# Discards all history and changes back to the specified commit
{% endhighlight %}

### SYNCHRONIZE CHANGES
Register a repository bookmark and exchange version history
{% highlight bash %}
git fetch [bookmark]
# Downloads all history from the repository bookmark
git merge [bookmark]/[branch]
# Combines bookmark’s branch into current local branch
git push [alias] [branch]
# Uploads all local branch commits to GitHub
git pull
# Downloads bookmark history and incorporates changes
{% endhighlight %}