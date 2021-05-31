# advanced-git

A repo for testing rebase and squash commands

# Rebase and Squash

To keep branches up-to-date with the develop branch and to keep git history clean, the git rebase and git squash commands can be used.

## Default Git Editor

Before starting, if you are not comfortable with using the Vim editor, you can change your default Git editor to the one of your choice. Below is how to set VSCode as your default Git editor

```shell
$ git config --global core.editor "code --wait"
```

# Squashing Commits

If you have many small commits that are not useful, then you may want to squash them into one single commit. 
This can be done with the squash command. In the example below, we will squash 4 commits into one. 
First, you will need to determine how many commits are in your branch. We also pass the -i option which is the “interactive” mode. 
This will open your default editor, see previous section for changing your default editor

In this example we use branch `feature/branch-to-squash`

```shell
$ git checkout feature/branch-to-squash
$ git rebase -i HEAD~4
```

# Simple Rebase

You can use rebase to update your branch to bring in the latest changes from a remote branch. The example below brings in changes from the latest `main` branch.

In this example we use branch `feature/no-conflict`

First checkout the branch you want to rebase

```shell
$ git checkout feature/my-feature-branch
```

Next, fetch all changes from the remote origin (prune will remove any remote-tracking references that no longer exist on the remote)

```shell
$ git fetch --all --prune
```

Then, run the rebase command against the origin/develop branch

```shell
$ git rebase origin/develop
```

Finally, push your changes to your remote branch. Since rebasing changes history, you will need to force push. 
`--force-with-lease` is a safer option that will not overwrite any work on the remote branch if more commits were added
to the remote branch (by another team-member or coworker or what have you)

```shell
$ git push origin feature/my-feature-branch --force-with-lease
```

# Rebase with Conflicts

Sometimes when rebasing or merging you can run into conflicts. It’s good practice to rebase frequently to minimise the chances of running into conflicts. When there are conflicts during a rebase, you will notice the following message. For a more detailed article about resolving conflicts, see  

In this example we use branch `feature/conflict-between-main`

```shell
$ git checkout feature/conflict-between-main
$ git fetch --all --prune
$ git rebase origin/develop

Auto-merging conflict_b.txt
CONFLICT (content): Merge conflict in conflict_b.txt
error: could not apply 9acbdb4... update conflict_b.txt
Resolve all conflicts manually, mark them as resolved with
"git add/rm <conflicted_files>", then run "git rebase --continue".
You can instead skip this commit: run "git rebase --skip".
To abort and get back to the state before "git rebase", run "git rebase --abort".
Could not apply 9acbdb4... update conflict_b.txt
```

Before you can continue, you will need to solve the conflicts and continue the rebase. You can run git status to get a list of files which have conflicts. If a file is in conflict, it will show in “Unmerged paths”

```shell
$ git status

Last command done (1 command done):
   pick 9acbdb4 update conflict_b.txt
No commands remaining.
You are currently rebasing branch 'feature/conflict-between-main' on 'b3fabe1'.
  (fix conflicts and then run "git rebase --continue")
  (use "git rebase --skip" to skip this patch)
  (use "git rebase --abort" to check out the original branch)

Unmerged paths:
  (use "git restore --staged <file>..." to unstage)
  (use "git add <file>..." to mark resolution)
	both modified:   conflict_b.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

Open the file in your editor of choice to fix the conflicts. The lines below show that the same line of code was updated on both the main branch and your feature branch. Choose the correct line, save the file, and close. Repeat this step for each conflict.

```txt
  1 <<<<<<< HEAD↲
  2 conflict updated on main↲
  3 =======↲
  4 conflict feature/conflict-between-main↲
  5 >>>>>>> 9acbdb4 (update conflict_b.txt)↲
```

Example of conflict_b.txt after resolving the conflict the solved conflict:

```txt
1 conflict feature/conflict-between-main↲
```

Next, run git add to stage the individual files. Note: If a file was removed, use git rm instead.

```shell
$ git add conflict_b.txt
```

Next, you can run git status to check that everything is staged correctly. 

```shell
$ git status
interactive rebase in progress; onto b3fabe1
Last command done (1 command done):
   pick 9acbdb4 update conflict_b.txt
No commands remaining.
You are currently rebasing branch 'feature/conflict-between-main' on 'b3fabe1'.
  (all conflicts fixed: run "git rebase --continue")

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	modified:   conflict_b.txt
```

Then run git rebase --continue to finish the conflict resolution.

```shell
$ git rebase --continue
[detached HEAD 0bea638] update conflict_b.txt
 1 file changed, 1 insertion(+), 1 deletion(-)
Successfully rebased and updated refs/heads/feature/conflict-between-main.
```

At this point, the rebase process should complete and you can push your branch

```shell
$ git push origin feature/my-feature-branch --force-with-lease
```

# Aborting a Rebase

If things get too difficult, you can always abort a rebase at any time

```shell
$ git rebase --abort
```
