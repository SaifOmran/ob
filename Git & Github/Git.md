### Types of version control
- 1- Local version control: Tracking the files or the project on your on PC only.
- 2- Central version control: Track and edit files on a server shared between different users, and the changing in the files is live.
- 3- Distributed version control: Every developer has a full copy of the files and can track the changes from any other developer, and Git is this type.
---
- The difference between Git and other version control systems that Git is cumulative version control which takes a full snapshot after each change in the file, where the other version controls are incremental which only take the difference between the new version and the pervious one because of the limited storage in the past.
---
### Git architecture
- The environment we work on it is called *working directory*, which contains only the file we work on.
- After any change in the file in working directory, we store the old version in *Git repository*.
- In the end the git repo with be in a hidden folder called *.git* and all of them will be in the working directory and it will be called repo.
	- Working directory (repo) -> .git -> git repo -> versions.
- So, the working directory contains only the latest version and git repo has all versions except the latest one.
- As an architect, what do you think how the requirements of git were ?
	- Track everything: files, folders, metadata.
	- OS independent: compatible with Windows, Linux, MacOS and any other OS, and it is implemented by making git as a folder that can be read by any OS.
	- Unique ID: every object (file, folder) in git should has unique ID for easier tracking, and it can be made using hashing algorithm like SHA-1 or MD5 which are deterministic.
		- When git is hashing an object it adds some info and then hashing them all, the added info are the type of object, the size of the object, null value.
		- ![[Pasted image 20251214001508.png]]
	- Track history (Logs): track who made what, which version is older and everything
		- git knows there is a change on object by comparing the old SHA-1 result of the object and the new SHA-1, if there is any difference git will register the old version in the repo, if there is not any change git will not do anything. 
---
### Git objects
- Blob: it is the content of a file ONLY.
- Tree: it is the content of a folder which are folders and files and the metadata of the folder itself.
- Commit: which is rapper that rap related objects in a commit batch.
- Tagged annotations: It is like an alias for a commit, as not every commit is a new version so when we commit the file after many modifications and the file is considered as new version we add tag to it for easier access and to make it have meaning between the commits.
---
### Internal architecture
- Old version controls was using 2-tree architecture which is the *working directory* and *repository*
- Git is using 3-tree architecture which is *working directory*, *staging area or index*, and *git repository*.
- The most useful benefit from the staging area is the selective commits.
- Staging area: it is the place where the files are prepared to be committed to the repo, if there are 3 files and each file will be modified, if I committed the 3 files in one time I will have to restore them all if I want to change anything on one file, but If I modified file1 and staged it, the file 2 and staged it and so on, I will restore the file which I want to change only.
- When we create a file it is created in *working directory* and the file in this situation is UNTRACKED, to make the file TRACKED by git we use
```Git
	git add
```
- here the file is tracked and there is a SHA-1 key is created for it and it is in the staging area file and also in the repo file (.git).
- we still also don't have the first snapshot or first version of the file, to make the first snapshot or version we use
 ```Git
	 git commit
```
---
### File states
1- untracked: you just created the file but it is not staged (U).
2- tracked: it is staged and can also be committed once before.
	tracked file can have 2 stats:
			1-Unmodified
			2-Modified (M)

---
### Basic configuration before starting
- we need to configure the user name and the mail to make git track who made the changes of the files 
```Git
git config --global user.name "Saif Omran"
git config --global user.email "saifomran2@gmail.com"
```

> --global means it is applied for one user for all repos, git modifies in (~/.gitconfig)
> --system means  it is applied for all user for all repos, git modifies in (etc/.gitconfig) 
> --local means it is applied for one repo, git modifies in (.git/config)

- To show all configurations
```Git
git config --list
```

- First thing we should do in the folder to create the .git folder to make the repo.
```Git
git init
```

![[Pasted image 20251214174902.png]]

- To show the files on the working directory
```Git
ls
```

- To show the files on the staging area
```Git
git ls-files
```

- To show the files in the repo (versions), there is no direct way , but in Linux we can use
```Linux
find .git/objects/ -type f
```

- So, we typed the command *git init* to make our folder or project as a repo and git created *.git* folder
- Now, we want to move the file1 to the staging area or from *UNTRACKED* to *TRACKED*, by using
```Git
git add file.txt
```

- In GUI the file name will be followed by (A) which meaning that file is index added (staged).
- To see the file status we use
```Git
git status
git status -s #it shows summary of files states
```
 - This command will show you the branch, the commits you made, the staged files and unstaged files.
 - ![[Pasted image 20251214180728.png]]
 - To show the staged files and their SHA-1 values
 ```Git
 git ls-files -s
 ```

>When a file is staged there is a SHA-1 value created for it, git uses this value to create a blob for this file in .git/objects, it uses the first 2 digits as directory name and the rest digits are stored in it.

![[Pasted image 20251214181804.png]]

- After the number of blobs and trees increased, How we can know if this SHA-1 value represents a blob or tree ?

```Git
git cat-file -t <SHA-1>
# -t referes to type
```

- To show the size of object
```Git
git cat-file  -s <SHA-1>
```

- To show the content itself
```Git
git cat-file -p <SHA-1>
```

- To commit the file
```Git
git commit -m "message"
```

- To see the commits with their date and SHA-1 values
```Git
git log
```

- We notice that when we mad the commit there are 3 objects are in the .git/objects, what are those 3 files ?
	- first one is the blob itself, it is already there.
	- second one is the tree which is the folder that contains the file
	- third one is the commit which indicates that those blobs and trees related to same commit process, it helps me to know the root tree which is the begin to know which files are modified.
	- ![[Pasted image 20251215224459.png]]
- So, I have commit object contains the root tree for the changed blobs, and the root tree contains the blobs and they are all for the same commit batch.
	- ![[Pasted image 20251215233002.png]]
	- ![[Pasted image 20251215225545.png]]
- When we modify a file, letter M appears beside the file name which indicating that the file is modified, and if we typed *git status -s*  we will see red M which indicating that the file in working tree is different from the file in the staging area and the repo (it does NOT have commit object), if we typed *git add* and added the file to the staging area, the red M would change to green M which indicating that the file in the working tree is the same of the file that in the staging area.

- After that we commit the file and this step creates 3 objects as we saw in the previous part which are the commit, tree and blobs and we use the SHA-1 value of the commit to know the node tree of the commit batch.

- We notice that in the second commit if we type *git cat-file -p* "SHA-1 value of second commit", we will see parent and SHA-1 value.
	- ![[Pasted image 20251215232119.png]]

- The parent commit is the commit that comes directly before the current (latest) commit.
  it is used to track the project history and to identify the changes (differences) between the last commit and the one before it.
	- ![[Pasted image 20251216005424.png]]

- There is a *HEAD pointer* which pointing to the version (commit) that will be in the working directory, and this means you will modify this version.
	- ![[Pasted image 20251216010011.png]]
- you can move the HEAD pointer to point on any version to modify it.
	- ![[Pasted image 20251216010147.png]]

- If I modified the file and save it, this means that the file in working tree is different from the file in staging area, To see the differences between the files in working tree and staging area we use
```Git
git diff
```

![[Pasted image 20251216190153.png]]

- Once we add the file to the staging area, there will be no differences between them and command *git diff* will show NOTHING.
- Now, after staging the file we want to know the differences between the file in repo and the file in staging area, To see the differences we use
```Git
git diff --staged
```

>Every command relative to the repo and the staging area we will use *--staged* for most cases.

- We can type the commit message using Vim editor
```Git
git commit
#then the vim will open (or the configured text editor)
```

- To show the summary of commit logs
```Git
git log --oneline
```
![[Pasted image 20251216204804.png]]

- To show the summary of just last 2 commits
```Git
git log --oneline -2
```

![[Pasted image 20251216204902.png]]

- To show the summary of specific file named file.txt commits
```Git
git log --oneline file.txt
```

![[Pasted image 20251216204936.png]]

- To show the changes of specific commit
```Git
git show <SHA-1 of commit>
```

![[Pasted image 20251216205015.png]]

>This command likes traversing through the commit then tree then blob using git cat-file **SHA-1 value of each object**

- To show the differences between 2 commits
```Git
git diff <SHA-1 of first commit>..<SHA-1 of second commit>
```
![[Pasted image 20251216205530.png]]

---
### Undoing things
- To unstage (untrack) file after staging (tracking) it by mistake we use
```Git
git rm --cached <file>
```

![[Pasted image 20251216210401.png]]

- If we modified the file and then we decided that those modifications are not right and we need to remove them we use
```Git
git restore <file>
```
it will compare the modified file with file in the staging area and discard the modifications.
> It is used in case we want to discard the changes before staging the file.

- What if we staged the file and then we want to discard the changes ? in this situation we lost the version in the staging area that was compared with the modified file in working tree, but we can unstage the file with special command and then restore it, we use
```Git
git restore --staged <file>
git restore <file>
```

- Now we want to traverse across the commits, if I am in the latest commit and want go back to the commit before it, How I can do it ?
```Git
git reset Head~1 # 1 refers to the number of the commit to the back ارجع لورا خطوة
```
this command will bring the file in the previous command back to the staging area and you can see the differences between it and your current file in working tree, and the working tree will not be affected, to make it reflects the working tree we use *git restore --staged 'file'* then *git restore 'file'*.

- If I am sure that I want to change the working tree directly with the content of specific commit, we use
```Git
git reset --hard Head~2
```

![[Pasted image 20251217180504.png]]

but this command is very dangerous in live production as you change the working tree of your file directly with previous version.

>HEAD is just a file that contains pointer that points to the SHA value of the commit object that also appears in the working directory.

 - What if I moved the HEAD back and I want to move it forward ? we use
```Git
git reset --hard HEAD@{number of commit}
```

![[Pasted image 20251217181109.png]]

 How we can specify the number of commit we need to forward to ? using
```Git
git reflog
```

- `git reflog` shows a local log of where HEAD have pointed over time.

![[Pasted image 20251217180614.png]]

we notice here that all HEAD moves are recorded and we can go to any specific commit like where the forth line added using `git reset --hard HEAD@{3}`

- Annotated tags : they are object which indicate that the commit is considered as a new version for easier access and to have meaning between the other commits.
```Git
#After making the commit
git tag -a v2.0 -m "Version 2.0 of the file"
git show v2.0
```

![[Pasted image 20251218004324.png]]

---
### Branching
- By default, we are working on main (master) branch which is critical, what if we need to test new features without affecting the master branch ?, so we make another branch will start from the commit which the HEAD is pointing to it.

- To create a new branch
```Git
git branch <branch-name>
```

- To switch from branch to branch
```Git
git switch <branch-name>
```

- To create a new branch and switch to it at the same time
```Git
git switch -c <branch-name>
```

- To show all branches
```Git
git branch
```

- To merge a branch to the main branch after finishing the modifications on the branch, I switch to the main branch then we use
```Git
git merge testing 
```

- To show the branches that are already merged with the main branch and the data is identical in both branches we use
```Git
git branch --merged
```

- After merging a branch to the main branch so we don't need it anymore, so we can delete it
```Git
git branch -d <branch-name>
```

### Divergent branches
- We have explained the simplest type of the branches where there is a branch from specific commit where the main branch still there and we do work on the created branch and at the end we merge it to the main, what if we made a branch from specific commit and we made work on this branch and also we made work on the main branch, how is the merge made in this situation ?
![[Pasted image 20251219004920.png]]

- I made file.txt and made 3 commits and then I created a branch called testing
![[Pasted image 20251219011007.png]]

- Now I will make a commit in branch testing (adding a line) and another commit on branch main (create new file called file2.txt)

- First thing we notice that the new file is created only for the main branch
![[Pasted image 20251219011424.png]]

![[Pasted image 20251219011520.png]]

- Second thing we notice that the graph show that there is a separation in commits from the point of creating the testing branch (909c2e3 commit)
![[Pasted image 20251219011702.png]]

- Here the merging is different as it is not linear merge, so git is creating a new commit to merge the branches and make the line linear again, and this is called 3-way commit.
![[Pasted image 20251219011940.png]]