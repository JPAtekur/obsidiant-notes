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
* Show list of running container short id
```
docker container ls -q
```
* Show list fo all containers short id
```
docker container ls -aq
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

### Stoping and removing container
* Pause container (use freezer cgroup instead of passing SIGSTOP signal)
```
docker container pause con-id
```
* Resume container (use freezer cgroup instead of passing SIGCONT signal)
```
docker container unpause con-id
```
* Stop container (First pass SIGTERM signal, if not sotp then pass SIGKILL signal)
```
docker container stop con-id
```
* Remove stop container
```
docker container rm con-id
```
You can't remove running container. If you remove a container first you need to stop and then remove.
```
docker container stop con-id
```
* Remove all stop containers
```
docker container rm $(docker container ls -aq)
```
Or
```
docker container prune
```
* Remove flag
```
docker container run --rm image-name
```
#### Pass specific signals to container
```
docker container kill --signal=signal-number con-id
```
* Set container hostname
```
docker container run --name=webapp --hostname=webapp ubuntu
```

### Coping contents into container
Directory must be present in container
```
docker container cp host_machine_path container-name:container-path
```

### Coping contents from container
```
docker container cp con-name:con-file-path host-machine-path
```
We can copy file from stoped container also.