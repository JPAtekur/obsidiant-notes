### Authenticating to registry
Login to docker hub as docker registry
```
docker login docker.io
```

### Retagging an image locally
```
docker image tag httpd:alpine httpd:new-tag
```

### Object sizes
```
docker system df
```

### Removing images
```
docker image rm image-name:tag
```

remove all unused images
```
docker image prune -a
```

### Inspecting a docker image
Image layers
```
docker image history httpd
```

### Image save and load

Save an image
```
docker image save alpine:latest -o alpine.tar
``` 

Load local image
```
docker image load -i alpine.tar
```

### Import and Export 
Export a container
```
docker export con-name > test.tar
```

Import local container
```
docker 
```