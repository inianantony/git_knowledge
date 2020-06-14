# Git intro

Git was created by Linus Torvalds in 2005

# Git Internal

Git stores information in a map like structure with the content as the value and the SHA of the content as the key

# Initialize Git Repo

`> git init` creates a new git repo locally in computer
Note : ignore the > when copying the command.

The .git\objects folder contains the files. The folder names start with the first 2 letters of SHA and inside contians the remaining SHA in the file name

# Structure of Project

|file| description|
|-----------|------------|
|laptops.txt| contains the list of laptop model names|
|specifications| a folder containing the specification details of each laptop|
|specifications\Readme.txt| a file to describe how to organize the files inside specification folder|
|specifications\\*| each laptop has a file here to describe its specs|

# Add to staging

`> git add .` or `> git add laptops.txt` & `> git add specifications\` to add files and folders to staging

# Git log

`> git log -1`
prints only the last commit

`> git log -n`
prints only the last n commits

`> git log --oneline --graph --decorate` 
prints logs in oneline with a graph structure and the reference information

`> git log --pretty=oneline`
prints the commits hash and comment in one line

`> git log --patch` 
Used to show the commit history with the changes introduced in each commit in which lines

`> git log --grep git --oneline`
Used to filter the git commits based on comments that contain the word git, and print the results in oneline

`> git log -GMac --patch`
Used to show all the commits that added or removed the word Mac in the any file. The `-G` option is used to specify that and `--patch` is used to show the changes made in that commit in which lines

`> git log HEAD~5..HEAD^ --oneline`
Used to show the commits from 5 commits before head to the HEAD. the two dots `..` is used to specify the range

`> git log dell..master --oneline`
Used to compare between branches. In the example show the commits that are not in dell but are in master branch. So if we merge master into dell branch then all those commits will be get merged

# Git objects

- Blobs
- Treea
- Commits
- Annonated Tags

# Git's hierarchical storage structure

`> git log --pretty=oneline` and get the firt commit hash
`> git cat-file -p <SHA>` to see the content and details inside the object. Note : this command has run in the root folder and not inside the .git\objects folder

![Git Commit History](https://github.com/inianantony/git_knowledge/blob/master/images/git_commit_history.png?raw=true)
![Git Commit History Diagram](https://github.com/inianantony/git_knowledge/blob/master/images/git_commit_history_diagram.png?raw=true)
The diagram from draw.io can be edited using [CommitHistory.drawio](https://github.com/inianantony/git_knowledge/blob/master/images/CommitHistory.drawio) file

# Storage

`> git count-objects` will list the total objects in the repository and the disk space used

`> tree` shows the folder structure in command line

# Non Annotated tags / Lightweight tags

`> git tag atag` without proving `-a` argument we create a simple tag without extra information like the data and who created it and its description.

# Annotated tags

`> git tag -a mytag -m "This is my first tag"`
`> git tag`
`> git cat-file -p mytag`

the `-a` option says that its annotated tags

The tags are also objects linking to a commit, and it wont move , while when a new commit is made then the refs of brach moves to the new commit.

![Tags Are also objects pointing to commit](https://github.com/inianantony/git_knowledge/blob/master/images/git_tag.png?raw=true)

# Master branch

by defaut when we create a `git init`, git creates a master branch and stores it in `.git/refs/heads/master`. This master points to the crurrent commit hash

![Master branch](https://github.com/inianantony/git_knowledge/blob/master/images/git_master_branch.png?raw=true)

# New Branch

`> git branch microsoft`

now both master and microsoft branch are pointing to the same commit ref.

when you do `git branch` it outputs `* master` and `microsoft` and master has `*` appended to it showing that HEAD is now at master and how does git knows this information about HEAD?
Its from the file `.git\HEAD` which points to the file inside the `refs` folder

![HEAD Information](https://github.com/inianantony/git_knowledge/blob/master/images/git_head_info.png?raw=true)

Head will not point to any commit and only the master references to the commit. So when a new commit is made, then master moves to the new commit but HEAD will still refer to the master brach and thus inturn can get to the current commit. So HEAD has no change. 

Now `> git checkout microsoft` will move the HEAD from master to `microsoft` branch and most importantly our working directory is updated with the files at the time of `microsoft` branch is made

# Git merge

Add a new line to laptops.txt and commit the change. now checkout the master brach. `> git checkout master`

do a `> git merge microsoft` git merges the changes and then automatically does a commit for us.

if we cat-file the latest commit, it has two parents.

![Two parents](https://github.com/inianantony/git_knowledge/blob/master/images/git_cat_file_merged_commit.png?raw=true)

NOTE : The merge command does an implicit commit if there are no conflicts

## Fast forward

If we checkout microsoft brach and does a merge with master , then by right git has to create a new commit and merge both master and microsoft inside them. But we already have that commit and its the master. so git does a fast forward. git dont like waste

![Fast Forward](https://github.com/inianantony/git_knowledge/blob/master/images/git_fast_forward.png?raw=true)

# Deteaching head from branch

AS we have seen so far HEAD will contain reference to branch. but we can also keep commit's reference inside HEAD by

`> git checkout <SHA>` now head is not referencing a branch, but a commit hash. If we now make more changes and commit, then the commits inside the HEAD changes. 
If we checkout master `> git checkout master` then HEAD will now have the ref to master and not to the direct commit. So what will happen to the previous commits?
Since they have no reference to HEAD, brach or tag, it will be garbage collected after some time. If you want to prevent it, then `> git checkout <SHA>` and then create a branch name there by `> git branch tryingdetachedhead` this way there is a reference created and the commits wont we garbage collected.

![Detached Head](https://github.com/inianantony/git_knowledge/blob/master/images/git_detached_head.png?raw=true)

# Rebasing

We have another branch called dell, and we have made some commits to the dell brach. in the same way we have made some changes to master as well. the below diagram can show that.

|Divergent Branches| Git Merge Situation | Git Rebase Situation |
|-----------|------------|------------------|
|![Divergent Commit](https://github.com/inianantony/git_knowledge/blob/master/images/git_divergent_commits.png?raw=true)| ![git_merge_situation](https://github.com/inianantony/git_knowledge/blob/master/images/git_merge_situation.png?raw=true)| ![git_rebase_situation](https://github.com/inianantony/git_knowledge/blob/master/images/git_rebase_situation.png?raw=true)|

Now we have a choice to do merge, but if we do that, then git will do create a new commit which has two parents : from master branch and from dell branch. but if we do a rebase
`> git rebase master` from `dell` branch then git makes copies of the commits except that the parent is not the old parent and then starts adding one by one on top of the `master` branch's commit and moves the `dell` branch ref to the top. Thus the new commits have new parents and they have new <SHA>. The old commits are then garbage collected. The top most commit is still pointing to dell, and the place where dell attached is pointing to master.
So if we need to move master branch also to the top , we can do a `> git rebase dell` from `master` branch or `> git merge dell` to move the master reference also to the top part, in this case git fast forwards the master branch. while in this process there is possibility of conflicts and we need to solve them if arises.

# Git pull

`> git pull` command has two actions behind the scene. A `> git fetch` followed by merging `master` with `origin\master` and then doing a new `commit`.

# Fork

`Fork` in github is similar to cloning, except that this process is done on remove provider and not on local machine.

# pull request

since we dont have write access to a branch or repo, we can make commits to our forked repo and then sent a pull request for the orginal owner of the project to pull from our branch or from our repo. If they review and are ok with our changes then they accept the request and the changes gets pulled to thier repo or branch

# Four stages of Git repo

- working area
- index
- repository
- stash

The index files are tracked by the file `.git\index` file from .git folder

when we run any git command, ask the basic question, 
- how does this command move data accross the 4 areas?
- how does this command change the repository?

# Compare and move data

`> git diff` command is used to compare the working area and the index
`> git diff --cached` is used to compare the index and the repository 

- Content moves from left to right 
`> git add .` moves the working area content to index
`> git commit -m "message"` moves data from index to repository

- Content moves from right to left
`> git checkout microsoft` git copies the files from repository and replaces with the files of repository

- Removing files from index
`> git rm <filename> --cached` will remove the staged file from index, but preserves it in working area. so with `--cached` is the opposite of `git add`
`> git rm <filename>` without --cached will remove the file from working area and the index
`> rm <filename>` will remove the file from working directory if its not yet staged

# moving and renamning files

when you rename a file as an example, rename `laptops.txt` to `laptops.md` and do a `> git status` git will say that `laptops.txt` is deleted and `laptops.md` is new file.
`> git add laptops.md` running this command, will add the `laptops.md` file to index and tells us that the deleted `laptops.txt` is not staged yet. ok so lets stage that as well.
`> git add laptops.txt` this will delete the `laptops.txt` from index as well.

`> git mv laptops.md laptops.md` command does the above operations automatically and stages them ready for commiting.

# Git Reset

Reset moves the current branch to other commit, and optionally copies data from repository to other areas

- `--hard` will copy files from repository to index and staging area.
- `--mixed` will copy files from repository to the index
- `--sodt` will only change the repository and leave the other two areas untouched.

if no paramter is specified then git does a `--mixed` reset

As an example, I have added Iphone 11 into the laptops.txt file and commited it to repository. Also I have add the necessary specifiations and commited it. During code review I found that iphone 11 should not be added to laptops.txt , but now I have two commits with wrong info. SO to rollback I can get the <SHA> of the commit that didnt contain the iphone 11 changes and then do a 
`> git reset --hard <SHA of the commit before the iphone 11 change>`

What if, I have added `acer aspire 5` to the laptops.txt and staged them. Before commiting then I decided that I dont want to commit at this point and I need to add more info. So how can I unstage?
`> git reset --mixed HEAD` will take the files from where head is pointing at and then overwrites the index stage so the changes are gone from index.
What if I completely changed my mind and remove the `Acer Aspire 5` from everywhere, then 
`> git reset --hard HEAD` will remove every change related to Acer.

# Git stash

I have decided to add `Lenovo ThinkPad X1 Yoga Gen 5` to the laptops folder and add a new spec desciption as well in the folder.
while in the process I had to focus on some a new model of laptop, but I still need to preserve my change of Lenovo. I can `stash` this change
`> git stash --include-untracked` becuase by default `stash` will ignore the untracked files and only stash the files from index.
The above command will move the changes to stash area , and then checkout the current commit back to the working directory.
`> git stash list` can show the list of stashes

`> git stash apply` will retreive the data from stash and copies to working directory and index in the same state as it was before, if no name is specified then the most recent stash will be copied. If a stash name copied from `> git stash list` is given then that data wil be used.

`> git stash clear` will clear eveything from stash

# Git Merge

If during a merge there occurs a conflict and we need to manually resolve them then there are few files created in .git folder like `MERGE_HEAD`, `MERGE_MSG`, `MERGE_MODE` to track the merge and its progress in the middle of merge. `> git status` uses these files to know we are in the middle of a merge

# Remove changes from only one file

Assume we have changed laptops.txt and Readme.md file and staged them. But I want only the change from Readme.md file to be persisted, but not the chnages in laptops.txt file. 

`> git rest --mixed laptops.txt` will remove the laptops.txt change from index

What if I totally dont want the change from laptops.txt file.
~~`> git reset --hard laptops.txt`~~ we will be tempted to use this command but it will not work, 
insted use the `> git checkout HEAD laptops.txt` to checkout only one file from HEAD and replace the index and working are with that file.

# Traversing history

`> git show Head` shows information of commit HEAD is pointing to
`> git show master` shows information of the commit master is pointing to
`> git show <SHA>` shows information of the commit
`> git show HEAD^` shows information of the parent of HEAD
`> git show HEAD^^` shows information of the parent of parent of HEAD
`> git show HEAD~` & `> git show HEAD~1`shows information of the parent commit where HEAD is pointing to
`> git show HEAD~2` shows information of the 2nd parent from HEAD
`> git show HEAD~2^2` go to 2 commits before head and then get the information of the 2nd parent

# Amend commit

I want to add `macbook air` into the laptops list, so I added it and commited it, but according to the convention of my project, I need to add specificaton while I commit,so I created a new file with `macbook air` in the specifications folder and statged it. Now I have the option to commit, But if I do git will create two commits, but what I need is one commit. So I need to amend my previous commit to add new data and **also update the commit message** to reflect the new changes. So git will create a new object and update the master branch reference to the new commit and the old commit will be garbage collected.

`> git commit --amend`

# Fixing History

Its always a golden rule to only fix history for the commits that are not pushed to shared repository

Step 1 : Add information to readme file with a good commit message
Step 2 : Add more information to the readme file thatwas supposed to go with the previous commit
Step 3 : Add another new commit by adding some information to readme file
Step 4 : Add a dummy line `dummy line` to the readme file
Step 5 : Oen interactive rebase to fix the history
`> git rebase -i`

Git will open up the inteactive commands for us, so we can change the commands for it to run

| Original | Changed|
|----------|---------|
|pick 6f2a7e7 fixing history|pick 6f2a7e7 fixing history|
|pick 80f67a6 add extta line|squash 80f67a6 add extta line|
|pick dc7f83c open an interactive rebase|pick dc7f83c open an interactive rebase|
|pick 996643b add dummy line|drop 996643b add dummy line|

From all the pick commands I decided to pick the `fixing history` commit, and squash `add extta line` commit with `fixing history` commit, and then pick the `open an interactive rebase` commit and totally drop the `add dummy line` commit.
so the end result will be

cf87bbc (HEAD -> master) open an interactive rebase
8e2a358 fixing history
8ce7033 (origin/master, origin/HEAD) use bold to signify

# Git Reflog

We discussed earlier that the non referenced objects are garbage collected eventually. But before its garbage collected how can we retreive the objects back if we dont know theie <SHA>. Thanks to the reflog command 

`> git reflog HEAD` tracks all the changes happened to the HEAD. So using the stating few letters of the <SHA> that is tracked in reflog we can still get our objects back
**Example**
- ba818dd (HEAD -> master) HEAD@{0}: commit (amend): fixing history explained
- 033bcb2 (origin/master, origin/HEAD) HEAD@{1}: commit: fixing history explained
- cf87bbc HEAD@{2}: rebase -i (finish): returning to refs/heads/master
- cf87bbc HEAD@{3}: rebase -i (pick): open an interactive rebase
- 8e2a358 HEAD@{4}: rebase -i (squash): fixing history
- 6f2a7e7 HEAD@{5}: rebase -i (start): checkout refs/remotes/origin/master
- **996643b HEAD@{6}: commit: add dummy line**
- dc7f83c HEAD@{7}: commit: open an interactive rebase
- 80f67a6 HEAD@{8}: commit: add extta line
- 6f2a7e7 HEAD@{9}: commit: fixing history
- 8ce7033 HEAD@{10}: commit: use bold to signify

I can get the deleted commits information of `996643b` by `> git show 996643b` or `> git show HEAD@{6}`. If you dont want this to ge garbage collected, then checkout this commit and put a label or branch on it.

![Deleted Commit Info](https://github.com/inianantony/git_knowledge/blob/master/images/git_reflog.png?raw=true)

`> git reflog refs/heads/master` tracks all the changes happened to the master branch

