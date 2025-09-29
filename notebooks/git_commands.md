# Git commands

## Pulling and pushing

To manage modifications to a repo with Git, just follow this basic workflow: 

```bash
# the first time we work with the repo on a machine, we need to clone the repo from the remote server (ex. GitHub)
git clone https://github.com/PCas95/programming-notes/edit/main/notebooks.git
# syncronise the local cloned repo with the remote one, ensuring you get the latest modifications from the remote (activities from other co-workers)
git pull
# add all files in current directory (or any specific dir or file) to the queue of files to send to server
git add .
# commit the file transfer of the queued items. Use `-m` to add a descriptive message of the modifications in the commit
git commit -m "message"
# send new files to server and update remote files with any modifications in the committed files
git push
```

> **Notes:**
>
> - `git clone`: just use the http link to the repository, adding **.git** at the end. From that moment on, the direcotry created will be a "git directory", which tracks a remote repository. `git` commands only work in `git` directories.
> - `git pull`: pull every single time you start working on a repo.
>
> Every time new changes need to be done to a repo, proceed in this order: `git pull`, `git add .`, `git commit`, `git push`. 
>
> This ensures that:
> 1. you don't cause misalignments with the repo if more users are working on the same branch;
> 2. you add to the queue only after ensuring you are up to date with the remote;
> 3. you document your commits and you push only after you're sure everything is fine.

## `git clone`

```bash
 git pull genpat-it/ngsmanager_brucella 
 git clone https://github.com/genpat-it/ngsmanager_brucella
 rm -rf .git
 git init 
 git branch -M main
 git remote add origin https://github.com/genpat-it/ngsmanager_brucella.git
 git add .
 git commit -m "initial commit"
 git config user.email "pierluigi.castelli95@gmail.com"
 git config user.name "Pierluigi Castelli"
 git commit -m "initial commit"
 git push -u origin main
 git branch -M main
 git push -f origin main
 git pull origin main
 git checkout main
 git fetch 
 git checkout main
 git status 
 git checkout main
 git checkout -b main
 git clone http://gtc-gitea.izs.intra:3000/bioinfo/ngsmanager
 rm -rf .git
 git init 
 git remote add origin https://github.com/genpat-it/ngsmanager_brucella.git
 git branch -M main
 git add .
 git commit -m "initial commit"
 git config user.email "pierluigi.castelli95@gmail.com"
 git config user.name "Pierluigi Castelli"
 git commit -m "initial commit"
 git push -u origin main
 git checkout -b main
 git push -u origin main
 history | grep 'git' > ~/Documents/notes/git_cloning.txt
```

## Track remote branch 

If there are branches in the repo, this doesn't mean that you will have them on your local repo right after cloning the repo. Usually cloning will only give you the `main` branch into the local repo.

In order to work with branches, we first need to:
- create a new local and remote branch (if the branch does not exist in the remote repo) 
- switch to a local branch that maps the remote one

```bash
# download new commits, branches and tags from the remote repository
git fetch origin
# list branches of the remote repository
git branch -r
# OR
git branch -r | grep 'pattern'
# create new local branch tracking remote branch, then switch to new branch
git checkout -b <local_branch_name> <remote_branch>
# list branches of local repo
git branch 
```

## `git merge`

In most occasions, branches will need to re-join to the `main` branch. This process is called a "`merge`".

If we are on a branch and need to re-merge to the **master** or main branch:

0. starting situation

    ```bash
    git branch
      master
    * wgsbac_update
    ```

1. Commit or stash any changes on your current branch
    - Make sure your working directory is clean:
        ```bash
        git status
        ```
    - If you have uncommitted changes you want to keep, commit them or stash them:
        ```bash
        # commit
        git add .
        git commit -m "Your message"
        # or stash
        git stash
        ```

2. Switch to the master branch

    ```bash
    git checkout master
    ```

3. Pull the latest changes from remote (if working with a remote repo)

    ```bash
    git pull origin master
    ```

4. Merge your branch into master

    ```bash
    git merge wgsbac_update
    ```

    > If the branches have diverged significantly, you might have to resolve conflicts. Git will guide you through resolving them.

5. Push the updated master branch to remote

    ```bash
    git push origin master
    ```

6. Clean up your branch (once merged, if you no longer need the old branch, you can delete it):

    ```bash
    # delete locally
    git branch -d <branch_name>
    # delete from remote
    git push origin --delete <branch_name>
    ```

> **Notes:**
>
> During the procedure, a "fast-forward" may happen. A fast-forward merge occurs when the master branch was directly behind the branch to merge, with no divergent commits. Git just moves the master pointer forward.
