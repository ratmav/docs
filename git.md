# git

[documentation](https://git-scm.com/doc)

## remote git host/server

**note**: access to remote repositories is managed via [[ authorized ssh keys | ssh key management ]] for the `git` user on the remote host.

### setup

#### create the remote git user

```bash
$ sudo adduser git
```

#### create the remote storage directory

```bash
$ sudo su git
$ cd ~/
$ mkdir repositories
```

#### initialize the remote repository

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

### disable/enable shell git shell access

```bash
$ sudo vi /etc/passwd
```
>change the `git` user's shell from `/bin/bash` to `/usr/bin/git-shell` to disable, vice versa to enable.

## local git client

### global `.gitignore`

```bash
$ git config --global core.excludesfile '~/.gitignore_global'
```

#### example

```bash
# vim buffers.
*.swp
*.swo
```

### project specific configuration

assuming you have a `~/.misc_envars` file, or something similar, that's sourced into your dotfiles, you can define functions like:

```shell
# some example variables.
export WEWLAD_NO="buTWhy?"
export COMMIT_SECRETS="w0uldYouDOThIs!?!!"

gconf_clientfoo() {
  git config user.email "your.user@clientfoo.com"
}

gconf_clientbar() {
  git config user.email "your.user@clientbar.com"
  git config user.url "https://github.com/look-a-client-specific-user"
  git config user.signingkey oHB0ygpGS1gn3dC0mm1ts
  git config credential.useHttpPath true # cloning via https, are we?
  git config credential.helper osxkeychain
  git config commit.gpgsign true # moar gee pah gee
  git config pgp.program gpg # even moar gee pah gee
}
```

**note**: commiting secrets is a bad plan. it's a good plan to keep creds and project-specific git config functions in a seperate file, like `~/.misc_envars` and then gitignoring the `.misc_envars` file. to use, clone a repo, move into it, then run the git config function of your choice.

this is _essentially_ what a git alias is doing, except the git alias prepends what's in the single quotes with `git`: for example

```bash
$ git config --global alias.clientfoo 'config user.email "your.user@clientfoo.com"'
```

is like `gconf_clientfoo` function in `~/.misc_envars`. aliases work best for simple wrappers around git commands. for more full featured configuration, live off the land (bash).

#### use the alias in appropriate projects:

```bash
$ cd project-name
$ git workprofile
```

### local working copy management

#### create local repository

```bash
$ cd project-name
$ git init
$ git add *
$ git commit -m “initial commit"
```

#### push local repository to remote host/server

```bash
$ cd project-name
$ git remote add origin ssh://git@your.domain.name/home/git/repositories/project-name.git
$ git push origin main
```

#### create branch

```bash
$ cd project-name
$ git checkout -b branch-name
$ git push -u origin branch-name
$ git branch --set-upstream-to=origin/branch-name # or set push.autosetupremote=true in your git config.
```

#### sync branch with `master`

##### merging from `master`

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

##### rebasing on `master`

```bash
$ cd project-name
$ git fetch --all # sanity check.
$ git checkout master
$ git pull
$ git checkout branch-name # the branch you care about.
$ git pull
$ git rebase master
$ git diff # confirm expected changes, resolve conflicts, etc.
$ git push origin branch-name --force-with-lease
```

###### a note on `--force` v. `--force-with-lease`

typically, `rebase` is used to groom git logs so those logs provide a more meainingful history of work on the project. this means that history is typically overwritten on a rebase, which means that a local working copy is going to diverge from the remote. in that case, `--force` is required to overwrite the history on the remote to match the local working copy. this introduces a smaller margin for error, so measure twice and cut once.

`--force-with-lease` will check for commits that are on the remote but missing in the local working copy, and fail if there are missing commits. this is much safer. use `--force` only when absolutely required.

#### merging branch to `master`

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

#### branch cleanup

##### remove remote branch

```bash
$ cd project-name
$ git push --delete origin branch-name
```

##### remove local branch

```bash
$ cd project-name
$ git branch -d branch-name
```

##### delete local branches with no remote

**note**: useful for cleaning up when branches are deleted by cicd after merging downstream.

```bash
# handy function to drop in .bashrc/_profile/wherever.
git-prune-sync() {
  if [ $# -eq 0 ]; then
    local remote=origin
  else
    local remote=$1
  fi

  if [ "$(type -P git)" ]; then
    git remote prune "$remote"
    echo "pruned $remote branch references."

    if git rev-parse --git-dir > /dev/null 2>&1; then
      gone_remote_branches=$(git branch -vv | grep "gone" | awk "{print \$1}")

      if [[ -z "$gone_remote_branches" ]]; then
        echo "no local branches track a gone $remote branch."
      else
        for gone_remote_branch in $gone_remote_branches; do
          echo "$gone_remote_branch" | xargs git branch -D
        done
      fi
    else
      echo "not a git repository."
    fi
  else
    echo "'git' command not available. check your installation."
  fi
}
```

#### remove files from repo and history

##### most recent commit

```
$ git rm --cached file_or_dir
$ git commit --amend -chead
$ git push --force-with-lease
```

##### all commits

```
$ git filter-branch --tree-filter "rm -rf file_or_dir"
$ git push --force-with-lease
```

#### undo commit

##### local and remote

```
$ git reset --hard head~1
$ git push -f <remote> <branch>
```

#### squash last n commits via interactive rebase

```
$ git log # find the sha of the n-1 commit.
$ git rebase -i <sha of n-1 commit/> # note: will open vim, so if you're in vim already use fugitive.
# use the editor (vim) to squash (and possibly reword) commits.
# if you're running a terminal inside vim (or neovim), be careful to avoid
# "vimception," i.e. running vim inside of a vim terminal emulator.
$ git push --force-with-lease
```
