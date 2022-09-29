* Check service status
```
systemctl status docker
```

* View logs
```
journalctl -u docker.service
```

* Check docker daemon configuration file
```
/etc/docker/daemon.json
```

* Check free disk space on host
```
df -h
```

* Debug docker
```
docker system info
```

* Reload docker using kill commmand
Find dockerd process id
```
ps -ef | grep -i dockerd
```
then 
```
kill -SIGHUP process-id
```