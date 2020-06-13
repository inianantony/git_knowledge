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

# Storage

`> git count-objects` will list the total objects in the repository and the disk space used

`> tree` shows the folder structure in command line

# Regular tags


# Annotated tags

`> git tag -a mytag -m "This is my first tag"`
`> git tag`
`> git cat-file -p mytag`

The tags are also objects linking to a commit

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