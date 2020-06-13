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

`> git log --pretty=oneline`
prints the commits hash and comment in one line

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