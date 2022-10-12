### Build with PATH
```
docker build .
```
```
docker build /opt/my-custom-app
```

### Build with URL
```
docker build github.com/creack/docker-firefox
```
```
docker build github.com/creack/docker-firefox#<branch>
```
```
docker build github.com/creack/docker-firefox:<folder>
```
```
docker build github.com/creack/docker-firefox#<branch>:<folder>
```

### Build with -
```
docker build - < Dockerfile
```

### Tag an image (-t)
```
docker build -t vieux/apache:2.0 .
```

Multiple tags to an image.
```
docker build -t whenry/fedora-jboss:latest -t whenry/fedora-jboss:v2.1 .
```

### Specify a Dockerfile (-f)
```
docker build -f Dockerfile.debug .
```
```
docker build -f dockerfiles/Dockerfile.debug -t myapp_debug .
```

