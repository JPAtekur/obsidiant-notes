### Show all network
```
docker network ls
```

### Create network
```
docker network create -d bridge --subnet 182.18.0.0/15 mynet
```
* Default network driver ``bridge``

### Inspect network
```
docker network inspect net-id
```

### Connect container to network
```
docker network connect mynet nginx-app
```

### Disconnect container from network
```
docker network disconnect mynet nginx-app
```


