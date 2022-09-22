### Find where the container store data
```
	docker inspect container-name:tag
```
The above command return JSON in the JSON under Volumes field has a directory which is the data storage location of the container.

### Bind Mount a host directory to container data directory
```
	docker run -v /host-directory:/container-directory image-name:tag
```

### Create a docker Volume
```
	docker volume create volume-name
```
Attach a volume to container
```
	docker run -v volume-name:/container-directory image-name:tag
```
