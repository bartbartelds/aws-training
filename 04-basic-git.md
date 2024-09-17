# Git

Over the course of these tutorials you'll be using ```git``` to obtain source code. But git doesn't just allow you to download files from a repository.  

Git is a distributed version control system (VCS) that helps developers track changes in code during software development. It allows multiple people to work on the same project simultaneously, maintain a history of changes, and collaborate effectively.

Git ensures that code changes are saved in a controlled manner and can be reviewed, reverted, or updated as needed.

## Key Concepts of Git
- **Version Control:**\
Git allows you to keep track of every change made to your code. This means you can revert to previous versions if something breaks or compare changes over time.

- **Distributed System:**\
Unlike centralized systems, Git is distributed. Each developer has a complete copy (repository) of the entire project history on their local machine. This makes collaboration more flexible and efficient since changes can be made, reviewed, and merged without needing constant access to a central server.

- **Repository (Repo):**\
A Git repository is a directory that contains your project files along with the full history of changes. It can be local (on your computer) or remote (e.g., on GitHub or GitLab).

- **Commiting, Branching, Merging:**\
These concepts are how developers can collaborate using git. They'll be covered in [21-advanced-git](21-advanced-git.md)


## Installing Git
If you are working on an managed device, and you cannot install local software, simply use the other .md files in this repository the way you're reading them now. 

>On cloudShell: No need to do anything - git comes pre-installed

>On Ubuntu/Debian: sudo apt update && sudo apt install git

>On macOS (using [Homebrew](https://brew.sh)): brew install git

>On Windows: Grab the installation files you need from [git-scm.com](https://git-scm.com/download/win)

## Verify git installation

You can verify whether ```git``` is installed by running ```git -v``` in your shell. \
The output should look something like ```git version 2.40.1```

If the command gives an error, git is not present on the system yet. 

## Cloning a repository
In order to download the contents of a git repository to your local system, you have to clone it using the **git clone** command. 

<mark style="background: #00ced1!important">*__Exercise 1:__ Cloning a git repository.*</mark>

1. Move to your home directory by running ```cd ~```
2. Run ```git clone https://github.com/bartbartelds/aws-training.git``` \
You should now see something like \
```bash
Cloning into 'aws-training'...
remote: Enumerating objects: 24, done.
remote: Counting objects: 100% (24/24), done.
remote: Compressing objects: 100% (19/19), done.
remote: Total 24 (delta 5), reused 19 (delta 3), pack-reused 0 (from 0)
Receiving objects: 100% (24/24), 7.05 KiB | 1.76 MiB/s, done.
Resolving deltas: 100% (5/5), done.
```

Notice the first line. \
3. Run ```ls -l``` \
Notice there is now a new directory called ```aws-training``` in your home directory.\
4. Run ```cd aws-training``` \
5. Run ```ls -al``` \
Notice that, beside the .md files, there is also a ```.git``` directory and a ```.gitignore``` file present. 

## The .git folder

The ```.git``` directory is a hidden folder located at the root of a Git repository that contains all the necessary information and metadata about the version control for that repository. It is the backbone of Git's functionality, as it tracks the entire history of the project, configuration settings, and changes made to files.

There is a lot going on in the ```.git``` folder. For now, just know that it exists. If the ```.git``` folder is removed, git no longer functions for this particular folder.

<mark style="background: #00ced1!important">*__Exercise 2:__ Explore the .git directory*</mark>

1. Change to the ```.git``` directory by running ```cd /home/cloudshell-user/aws-training/.git```
2. Run ```ls -al``` \
Notice that there's quite a bit of stuff in the ```.git``` folder. All if it is required to make git keep track of changes, and allow you to contribute effectively to the contents of the repository.  \
3. Run ```cat config``` \
You should see: 
```bash
[core]
        repositoryformatversion = 0
        filemode = true
        bare = false
        logallrefupdates = true
[remote "origin"]
        url = https://github.com/bartbartelds/aws-training.git
        fetch = +refs/heads/*:refs/remotes/origin/*
[branch "main"]
        remote = origin
        merge = refs/heads/main
```
Notice that the ```remote "origin"``` is the url you used to clone the repository from. \
Notice that the ```branch``` is current ```main``` \
4. Return to the ```aws-training``` folder in your home directory
5. Type ```git status``` \
You should see: 
```bash
On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
```
This means you're currently on the git branch called ```main```, and your local files are up to date with the files are the remove origin. 

## The .gitignore file
The ```.gitignore``` file is a special configuration file in Git that specifies which files or directories should be ignored by Git.

This means that any files or patterns listed in the ```.gitignore``` file will not be tracked by Git, even if they are present in the repository’s directory. This is useful for preventing unnecessary or sensitive files (such as logs, temporary files, build artifacts, or personal settings) from being committed to the repository.

This might not make a lot of sense now - but it will once you start writing and submitting code.

<mark style="background: #00ced1!important">*__Exercise 2:__ Explore the .gitignore file*</mark>

1. In the ```aws-training``` folder, type ```ls -al``` \
Notice the ```.gitignore``` file. \
2. Run ```cat .gitignore``` to view the file contents \
You should see something like: 
```bash
# Local .terraform directories
**/.terraform/*

# Logs
logs
*.log
npm-debug.log*
crash.log
crash.*.log

# Dependency directories
node_modules/

# .tfstate files
*.tfstate
*.tfstate.*

# Environment files
.env
.env.test
.env.production
.env.local

# Exclude all .tfvars files, which are likely to contain sensitive data, such as
# password, private keys, and other secrets. These should not be part of version 
# control as they are data points which are potentially sensitive and subject 
# to change depending on the environment.
*.tfvars
*.tfvars.json

# Ignore override files as they are usually used to override resources locally and so
# are not checked in
override.tf
override.tf.json
*_override.tf
*_override.tf.json

# Ignore transient lock info files created by terraform apply
.terraform.tfstate.lock.info

# Include override files you do wish to add to version control using negated pattern
# !example_override.tf

# Include tfplan files to ignore the plan output of command: terraform plan -out=tfplan
# example: *tfplan*

# Ignore CLI configuration files
.terraformrc
terraform.rc
```
Most of these files and directories currently do not exist in the ```aws-training``` directory on your system. But apparently a developer found it necessary to exclude them by adding them to the ```.gitignore``` file. This is usually done to keep the code files small and easily shareable. 

Again - for now, just know the ```.gitignore``` file exists. It will make more sense later. 