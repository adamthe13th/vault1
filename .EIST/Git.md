#git
## Reverting to a previous commit
```bash
git revert <commit_hash>..HEAD
# or to undo a certain number of commits
git revert HEAD~3..HEAD
```

## Staging and Committing
```bash
git add <file>
git add .
git commit -m "msg" # for comitting staged
git commit -am "msg" # for adding tracked and committing at once
```

## Branches

``` bash
git branch # list all branches
git branch <branche_name> #create new branche
git checkout <branche> # switch to branche
git checkout -b <branche_name> # create new branche and change to it
``` 
>[!tip] **--continue**
>if a conflict happens when using **cherrypick, rebase or revert (multi-commit)**, fix the conflict then use
> ```bash
> git add <conflicted_file>
>git <command> --continue
>```
>for merge, manually  fix and commit

### merging
keeps the original history of both branches

```bash
git checkout <branch_name> # moves to branche
git merge <other_branche_name> # merges other into current
``` 
>[!info] **In case of a Merge conflict** 
> the file with the conflict will be edited by git
> change the file to only accept one of the changes
> ``` bash 
> git add <conflicted_file> 
> git commit 
> ``` 

>[!tip] **Aborting Merge** returns branche to pre-merge state
>```bash
>git merge --abort
>``` 


![Merging main into feature branch](https://wac-cdn.atlassian.com/dam/jcr:4639eeb8-e417-434a-a3f8-a972277fc66a/02%20Merging%20main%20into%20the%20feature%20branh.svg?cdnVersion=2867)
### Rebase
```bash
git checkout <branche_name>
git rebase <other_branche_name> # merges other_branche into branche
# if conflict happens
# fix conflict in file
git add <conflicted_file>
git rebase --continue
```

![Rebasing feature branch into main](https://wac-cdn.atlassian.com/dam/jcr:3bafddf5-fd55-4320-9310-3d28f4fca3af/03%20Rebasing%20the%20feature%20branch%20into%20main.svg?cdnVersion=2867)
### Cherrypick
Apply a specific commit from another branche to another
```bash
git checkout <current_branche-name>
git cherry-pick <commit_hash>
# if conflict happens
# fix conflict in file
git add <conflicted_file>
git cherry-pick --continue
``` 
