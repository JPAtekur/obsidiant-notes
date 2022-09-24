### Create container
```
docker container create 'httpd:latest'
```
* List of containers
```
docker container ls
```
* List of all containers
```
docker container ls -a
```
* Show last container
```
docker container ls -l
```
* Start container
```
docker container start container-name or id
```
* Create and run container
```
docker container run images
```
* Attach container in terminal
```
docker container attach container-id
```
* Getout of container terminal without exiting
```
press: Ctl + p + q
```
* Execute command in container
```
docker container exec container-id command
```
* Container inspect to view details
```
docker container inspect container-id
```
* Container stats
```
docker container stats
```
* Conatianer top
```
docker container top con-id
```
* Container logs
```
docker container logs con-id
```
* Stream contianer logs
```
docker container logs -f con-id
```

### Docker system events
```
docker system events --since 60m
```
