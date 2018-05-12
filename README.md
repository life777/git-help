# Git cheatsheet

## Aliases
To stop all argues between developers and sleepless nights due to merging/rebasing someones wrong code style configure your git

    git config core.whitespace "blank-at-eol,blank-at-eof,tab-in-indent"

To add an alias for some command we have to edit git config:

	git config alias.st status

Also it can be done globally:

	git config --global alias.st status

If we need to add a few flags to the command:

	git config alias.st 'status --short --branch'

Use `!` to call other tools from console. Also if you need to reference previously visited branch, you can use `@{-1}`. For example alias that checkout entered branch and merge it with branch that you are currently in:

	git config alias.qm '!git checkout $1;git merge @{-1}'

### Useful example 
    git config alias.st status
    git config alias.ch checkout

## History

Command for showing history of commits is `git log`. It cam be formatted as you want with `--pretty` flag:

	git log --pretty=oneline | short | medium | full | fuller | email | raw

	git log --pretty='%h %d %n%Cgreen%s %Creset[%an] %Cred(%cr)%Creset' -1  (https://git-scm.com/docs/pretty-formats)

	git log --pretty='%Cred%h%Creset | %d %n%Cgreen%s %Creset[%an] %C(cyan)(%cr)%Creset' --graph

To see a graph of commits use `--graph` flag:

    git log --graph

To see commits reachable from work2, but not in master:

	git log master..work2

To see all commits that are not reachable from both branches

	git log master...work2

To see merged commit

	git log --ancestry-path work2..master

To show branches and their commits. If you need to exclude commits in first branch (in this case `master`) use `topics` flag:

	git show-branch master work work2

To search for cherry-picked commits those changes are the same in both branches

	git log --cherry-mark --left-right master...work2

To search for commits that are not reachanble form master and not cherry-picked

	git log --cherry master...work

To get all merge commits from work2 to master

	git log --merges work2..master 

To get last merge commit from work2 to master

	git log --merges --ancestry-path work2..master 

To get all commits with this file. Flag `--follow` required if this file was renamed. Flag `--patch` is for showing all changes

	git log --follow index.html
	git log --patch --follow index.html

To search for commit where spesific line was added or removed

	git log -S'hello world*' --pickaxe-regex

or changed

	git log -G'hello world*'
	git log -S'hello world' --stat

For some languages you can search for all commits where spesific method or function was changed (see .gitattributes)

	git log -L:startup:speech/js/recognizer.js

To look for branches

    git branch -a
    git branch --merged
	git branch --no-merged

To show quick stat and differencces in files use commad `show`

	git show head^
	git show head~2
	git show head --pretty='%ar'
	git show head --no-patch
	git show head --stat

To find who changed some spesific line and what commit this was made in. `L` can be used to point at line numbers or for searching line with RegExp

    git blame index.html
	git blame index.html -L 12,14
	git blame -L:a:index.html

### Useful examples
To show your commit comments from all branches during the previous month:

    git log --author=vladimir --all --since="1 month ago" --until="2 days ago" --pretty='%s'

## Differences
To see difference between files. You also can choose an algorithm for comparing files

	git diff --word-diff --unified=10
	git diff --patience
	git diff --histogram

To see difference between commits you can use `..` to show a range for comparing.

	git diff head~2..head
	
    git diff head~3:future\index.html..head:future\index.html
    
	git diff --staged

Symbols to navigate bettwen commits:
1) `^` - one commit back `head^`
2) `~5` - 5 commits back `head~5`

Also you can use built-in graphic tool for comparing commits 

	git difftool

## Cat files
	git cat-file -p head
	git cat-file -p hash

## Commiting
To add to stage (`-i` for interactive mode where you can add single file or line for file)

	git add -p
	git add -i

Commiting with fast forward and without

    git commit -am "Some comment" --no-ff

To reset last commits. If you don't need changes from commit that you are going to reset use `--hard` flag, if you want to put them in stage use `--soft` flag

    git reset head^ --hard

## Stashing
To save your current changes in stack

    git stash
    git stash -u
	git stash save --keep-index -u "work in progress"

### Useful examples
To pul changes from server to the branch where you have your own changes use it

    git pull --autostash

## Rebasing 

	git rebase head~2..head --whitespace=fix
	git rebase -i head~4

## File and branches
    
    git shortlog -s -n
	git show-ref --heads//all files with branches
	git fsck --unreachable
	git fsck --dangling
	git branch work <hash>

All logs about current branch

    git reflog
	git log --grep=C --walk-reflog

To remove and restore branches (cleare a new branch that pointing old commit)

## Merge
To merge one branch to another

    git merge work2

To cherry-pick one commit 

	git cherry-pick -m 1 <hash>

To return hash of the merge commit

	git merge-base master work2

If there are conflicts it will show you two commits and their messages

	git log --merge

To add to the file the line with changes before creating branch

	git ch --conflict=diff3 some.js

Command for resolving conflicts before full branch merge

	git rerere

## Binary search

You can use it if you need to find acommit the broke everything:

Starting binary search

	git bisect start

Point at last commit where you found a bug

	git bisect bad head

Point at commit where wasn't any bugs

	git bisect good head~4

Mark commit that you are currently on as a commit without bug

	git bisect good

Mark as a bad commit

	git bisect bad

Stop binary search

	git bisect reset

## Working tree
To create working tree from some branch to another branch. `--track` adds upstream to origin. `-b` creates new branch. 

    git worktree add --track -b <new-branch> ../quick_fix <remote>/<branch>

To list them

    git worktree list