* Start docker daemon manually
```
dockerd
```
with debug
```
docker --debug
```

### Unix Socket
location
```
/var/run/docker.sock
```

### Daemon configuration file
```
/ect/docker/daemon.json
```
```
{
  "debug": true,
  "tls": true,
  "tlscert": "/var/docker/server.pem",
  "tlskey": "/var/docker/serverkey.pem",
  "hosts": ["tcp://192.168.59.3:2376"]
}
```
