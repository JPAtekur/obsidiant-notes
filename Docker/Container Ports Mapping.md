* Publish a container port to host machine
```
docker run -p <host-port>:<container-port> nginx
```
* Specify host network
```
docker run -p 198.168.0.1:80:5000 nginx
```
* Random port mapping. port range 32768 - 60999
```
docker run -p 80 nginx
```
* View port range
```
cat /proc/sys/net/ipv4/ip_local_port_range
```

#### Publish port automatically
```
docker run -P nginx
```

* Expose additional container port
```
docker run --expose=9090 nginx
```

* Show all expose container ports
```
docker inspect image-name
```

* IP Tables port mapping
```
iptables -t nat -S DOCKER
```
