git
===

[documentation](https://git-scm.com/doc)

# remote git host/server

**note**: access to remote repositories is managed via [[ authorized ssh keys | ssh key management ]] for the `git` user on the remote host.

## setup

### create the remote git user

```bash
$ sudo adduser git
```

### create the remote storage directory

```bash
$ sudo su git
$ cd ~/
$ mkdir repositories
```

### initialize the remote repository

```bash
$ sudo su git
$ cd ~/repositories
$ mkdir project-name.git
$ cd project-name.git
$ git --bare init
$ git config core.sharedrepository 1
$ git config receive.denynonfastforwards true
$ find objects -type d -exec chmod 02770 {} \;
$ exit
```

## disable/enable shell git shell access

```bash
$ sudo vi /etc/passwd
```
>change the `git` user's shell from `/bin/bash` to `/usr/bin/git-shell` to disable, vice versa to enable.

# local git client

## global `.gitignore`

```bash
$ git config --global core.excludesfile '~/.gitignore_global'
```

### example

```bash
# vim buffers.
*.swp
*.swo
```

## project specific email addresses

### create the global alias:

```bash
$ git config --global alias.workprofile 'config user.email "user@work-email.com"'
```

### use the alias in appropriate projects:

```bash
$ cd project-name
$ git workprofile
```

## local repository management

### create local repository

```bash
$ cd project-name
$ git init
$ git add *
$ git commit -m “initial commit"
```

### push local repository to remote host/server

```bash
$ cd project-name
$ git remote add origin ssh://git@your.domain.name/home/git/repositories/project-name.git
$ git push origin master
```

### create branch

```bash
$ cd project-name
$ git checkout -b branch-name
$ git push -u origin branch-name
$ git branch --set-upstream-to=origin/branch-name
```

### sync branch with `master`

#### merging from `master`

```bash
$ cd project-name
$ git fetch --all # sanity check.
$ git checkout master
$ git pull
$ git checkout branch-name
$ git pull
$ git merge master
$ git push origin branch-name
```

#### rebasing on `master`

```bash
$ cd project-name
$ git fetch --all # sanity check.
$ git checkout master
$ git pull
$ git checkout branch-name # the branch you care about.
$ git pull
$ git rebase master
$ git diff # confirm expected changes, resolve conflicts, etc.
$ git push origin branch-name --force
```

##### a note on `--force`

typically, `rebase` is used to groom git logs so those logs provide a more meainingful history of work on the project. this means that history is typically overwritten on a rebase, which means that a local working copy is going to diverge from the remote. in that case, `--force` is required to overwrite the history on the remote to match the local working copy. this introduces a smaller margin for error, so measure twice and cut once.

### merging branch to `master`

```bash
$ cd project-name
$ git fetch --all # sanity check.
$ git checkout branch-name
$ git pull
$ git checkout master
$ git pull
$ git merge branch-name
$ git push origin master
```

### branch cleanup

#### remove remote branch

```bash
$ cd project-name
$ git push --delete origin branch-name
```

#### remove local branch

```bash
$ cd project-name
$ git branch -d branch-name
```

### remove files from repo and history

#### most recent commit

```
$ git rm --cached file_or_dir
$ git commit --amend -chead
$ git push --force
```

#### all commits

```
$ git filter-branch --tree-filter "rm -rf file_or_dir"
$ git push --force
```

### undo commit

#### local and remote

```
$ git reset --hard head~1
$ git push -f <remote> <branch>
```
