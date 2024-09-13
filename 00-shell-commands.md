# Shell commands


## Linux
The internet runs on linux - for the vast majority at least. Linux is a lightweight and stable operating system that is the foundation for most websites, web api's and saas services you're likely to encounter. Linux was created in the 90s, and is largely based on Unix. It's open source, meaning that you can see exactly how it is programmed - and make changes to it if you so desire. 

You can run linux on very modest specs, such as raspberry pi's. The small installation footprint that most server-side linux installations have, is due to the fact that linux can be administered effectively through a shell.

## What's a shell
A shell is a text based interface that allows you to run commands on its host system. It's considered a non-graphical interface, contrasted with windows which is a graphical user interface. Although linux does have graphical user interfaces, such a gnome or kde, the majortiy of linux servers don't have these installed since all they would do, is consume system resources.

A shell offers a way for you to interact with a linux installation without a user interface. You do this by typing commands and interacting with the results printed on the command line. 

## Users and Groups
[not relevant for users of aws cloudshell]

## Everything is a file
On linux, everything is file-based. Programs are a file, log files are a file, regular files are a file. The only difference between a simple txt file and a program are the permissions and flags the file has. This means that you can f.e. run scripts by simply typing commands into a txt file and altering its permissions. 

It also means that you can nearly completely control all elements of a linux installation through running commands through a shell, and manipulating files.


## Shell commands
You enter commands in the shell in the following format: 
```shell
command -options target-file
```
Nearly all commands have options, or switches, you can use to alter what it does - or the change the output it provides. For example: 
```shell
ls      -- lists the filenames for all files in the current directory - ignores files and directories starting with .
ls -a   -- same as ls, but also shows files and directories starting with . 
ls -l   -- same as ls, but also shows the permissions, owner and group, file size, and last modification date
```

In most cases, switches can be combined
```shell
ls -al
```
Compare the differences and similarities between ls, ls -a, ls -l and ls -al

```shell
ls
ls -l
cd 
pwd
touch
cat

chmod
chown
```

## Path
Everything you type into a shell, the system will attempt to locate and execute. The locations the system checks for the command you want to run, are configured in the PATH variable. You can see what the PATH variable is set to by echoing it to the command line
```shell
echo $PATH
```

### Directories on the linux file system
```shell
/       root directory of the entire file system hierarchy
/bin    essential command binaries for all users
/dev    device files
/etc    system-wide configuration files
/home   home directories for users on the system. You enter the system at /home/yourusername
/lib    libraries for the essential binairies
/mnt    temporarily mounted file systems    
/opt    add-on application software packages
/proc   virtual file system providing process and kernel information
/root   home directory for the root user
/run    runtime variable data
/sbin   essential system binaries
/srv    site-specific data served by this system
/sys    contains information about devices, drivers and kernel features
/tmp    temporary files - often ephemeral
/usr    non-essential binaries for all users
/var    variable files, files whose content is expected to change continually during normal operation oft the system, such as logs, spool files and temp emails.
/var/lock   lock files, files keeping track of resources currently in use
/var/log    log files
/var/tmp    temporary files that need to be preserved between reboots


```shell
nano filename
```
