View logging driver
```
docker system info | grep "Logging Driver"
```
default logging driver is ``json-file`` 

Contianr log file
``/var/lib/docker/containers/con-id/con-id.log``

Find logging driver
```
docker system info | grep "Log:"
```

To set logging driver, configure daemon.json file
```
{
	"log-driver": "awslogs"
}
```

Override logging driver
```
docker run --log-driver json-file nginx
```

Check container logging driver
```
docker continar inspect con-id
```
```
docker inspect -f {{.HostConfig.LogConfig.Type}} con-id
```
