If the container stop greacefully the exited code will be 0 other then will not 0.

Use ``--restart``  flag to set restart police on a container. For restart police container should run atleast 10s.
It has 4 posible value which are
* ``no`` is the default value, which means never restart
* ``on-failure`` when the container terminated with non 0 exited code, container will restart
* ``always`` container will restart always when it terminated. If the container manually stop then container will restart when daemon restart.
* ``unless-stoped`` container will restart always unless manually stop.

```
docker container run --restart=always ubuntu
```

### Live Restore
live-restore configuration is use to alive a containers when docker daemon is stoped.

configure file location
``/etc/docker/daemon.json``
```
{
	"debug": true,
	"hosts": ["tcp:http://192.168.1.10"],
	"live-restore": true
}
```
To activate this configuration reload docker daemon using following commad
```
sudo systemctl reload docker
```
Then stop container
```
sudo systemctl stop docker
```
Then check running process
```
ps aux | grep nginx
```

