# Git

git

cloning a git repository
```shell
git clone https://url-to-the-repository-you-want-to-clone
```

.git folder

.gitignore file


```shell
git status
```


committing changes to a git repository

```shell
git add .
git commit -m "commit message describing the nature of the changes you made"
git push
```

branches, branch strategy, branch switching
```shell
git branch // see all local branches
git branch -r // see all remote branches
git branch -a // see all local and remote branches
```

creating a branch
```shell
git checkout -b my-branch-name
```

switching branches
```shell
git checkout my-branch-name //switch to another local repository
git checkout --track origin/my-branch-name //

```
merge requests