### Some advanced commands
* Rollback a changed file
	```
	git checkout <file-name>
	```
* View changes
	```
	git diff
	```
* View changes in staged files
	```
	git diff --cached
	```
* Unstage a staged file
	```
	git restore --staged <file-name>
	```
* Revert back to previous commit
	```
	git revert HEAD
	```
* Hard rollback to specific commit
	```
	git reset <commitId>
	```
