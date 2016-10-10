# git_info
Useful git tricks I've learnt using git that might save your life some time.

## Introduction
Git is an amazing tool, really powerful, but a bit complex as well. Many people I know are sticked to a [stupid interface][itbwtcl] where the options are nothing more than commit/checkout/push/pull/stash. Most of them get really embarrassed anytime something unexpected happens, because they're not aware of all the git possibilities.

Each time I do something I don't like on a repository, I look for some solution, because I know you always will find a fix for almost any mess you'd done on git.


## How to write a commit message
This is not a real trick involving commands, but is one of the most important things I've learned of Git: *commit messages matter*. I encourage you to [read this amazing article][gitcommit] explaining why you should really care about it and give some tips to mastering commit messages.

IMHO, git is not only a versioning tool, but a really good documentation on the project. While commenting a method explain how this method works, having a look to the repository history may help a new developer to understand how things are done. Adding a new feature, implementing a new request, fixing a bug... the whole process of developing these things could be better understood taking a look over the repository history. How your code change, tells a story. And good commit messages are the best narrator to deeply understand it and place the reader on the right context.

## Rewritting history
Modify last commit message:

`git commit --ammend -m "New fancy message"`

`git commit --ammend` Will prompt editor with the old message to modify.

Forgot to add some files to last commit

`git add forgotten_files`

`git commit --amend` Will prompt editor with the old message to have a chance to rewrite.

`git commit --amend -C HEAD` Was last message right? Reuse it and not show editor.


Modify n last commit messages (n = number of commits to change)

`git rebase -i HEAD~n` [Difference between HEAD^n and HEAD~n][headsdiff]

	Editor will open. Write `reword` in the commits you want to modify their messages, and let `pick` in the ones that are ok. A more elaborate way is to write `edit` in the commits to modify. Then, for each commit ammend the message as we learnt previously and continue with the rebase

`git commit --amend` and `git rebase --continue`

Change branch's parent

`git rebase --onto <new-parent> <old-parent>` ([Further reading][change-parents])


## Recover files
Delete last commit and recover the modified files. Sometimes I do temporal commits instead of stashing, or maybe you didn't want to change some files, or you want to split a big commit in some smaller chunks.

`git reset --soft HEAD^`

Cherry-pick a single file from another commit

`git show [COMMIT_ID]:[PATH_TO_FILE] > [PATH_TO_FILE]`

### Recovering a single file from stash
You even can take a single file from the stash. stash@{0} represent the latest stashed commit, you can change the 0 for any other number to control which stash you want to use.

`git show stash@{0}:<full filename>` Show a single file from stash

`git checkout stash@{0} -- <filename>` Get a single file from a certain stash

`git show stash@{0}:<full filename>  >  <newfile>` Get a single file from stash to a different filename

A stash is represented as a commit whose tree records the state of the working directory, and its first parent is the commit at HEAD when the stash was created. stash@{0}^1 shortcut means first parent of given stash. 

`git diff stash@{0}^1 stash@{0} -- <filename>` Show diff of file ([Further readhing][stash-get-file])


### Recover DELETED stash
This really saved my life once. In one sprint I left some fixing half-developed, so I stashed changes to continue later. I didn't have the chance to come back to this work until one month later. In the meantime I was cleaning up my repo, I forgot this precious stashed code, and I deleted that stash. It took my a while to realize of this (I knew I did some changes in the code, but I couldn't find it). Fortunately I found [this answer in SO solving my problem][[stash-restore]].

First you would like to find where is your stash. This command will show you all the commits at the tips of your commit graph *which are no longer referenced from any branch or tag* – every lost commit, including every stash commit you’ve ever created, will be somewhere in that graph.

`git fsck --no-reflog | awk '/dangling commit/ {print $3}'`

The easiest way to find the stash commit you want is probably to pass that list to gitk:

`gitk --all $( git fsck --no-reflog | awk '/dangling commit/ {print $3}' )`

To spot stash commits, look for commit messages of this form:

`WIP on somebranch: commithash Some old commit message`

`git stash apply $stash_commithash`

## Alias
Get all defined alias

`git config --get-regexp alias`

Create new alias
`git config --global alias.COMMAND {code triggered by that command, between quotes if it has spaces}`


git config --global alias.lga "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"


 git config --global alias.lga "log --branches --tags --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"


## Other
Change branch name:
`git branch -m newBranchName`




[itbwtcl]: http://www.cryptonomicon.com/beginning.html
[gitcommit]: http://chris.beams.io/posts/git-commit/
[headsdiff]: http://stackoverflow.com/questions/2221658/whats-the-difference-between-head-and-head-in-git
[change-parents]: http://stackoverflow.com/questions/3810348/setting-git-parent-pointer-to-a-different-parent
[stash-get-file]: http://stackoverflow.com/a/1105666/1516973
[stash-restore]: http://stackoverflow.com/a/91795/1516973