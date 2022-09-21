### How do I allow users to use Docker?
1.  Create the docker group. (If exist don't create)
	```
	sudo groupadd docker
	```
2.  Add your user to the docker group.
	```
	 sudo usermod -aG docker $USER.
	```
3.  Log out and log back in so that your group membership is re-evaluated.
4.  Verify that you can run docker commands without sudo .

### Docker directory in host machine
```
	/var/lib/docker
```
