# Basic git commands
* Create a local repository
	```
	git init
	```
* Stage changes
	```
	git add .
	```
* Commit changes
	```
	git commit -m "commit message"
	```
* Add global user
	```
	git config --global user.name "Atekur Rahman"
	git config --global user.email "example@gmail.com"
	git config --global user.password "password1234"
	```
* push an existing repository from the command line
	```
	git remote add origin https://github.com/JPAtekur/titanwork.git
	git branch -M main
	git push -u origin main
	```
* Git log
	```
	git log
	git log --oneline
	git show <commit-id>
	```
* Git pull
	```
	git pull
	```
* Create local branch from existing branch
	```
	git checkout -b <new-branch-name> <existing-branch-name>
	```
* Push local branch in remote
	```
	git push origin <branch-name>
	```
* Merge branch
	```
	git merge <branch-name>
	```
* Clone repository in local
	```
	git clone <repository-url>
	```
