### Linux Signals
* Kill a process
```
kill -SIGSTOP process-id
```
If you don't know process ID
```
kill -SIGSTOP $(pgrep service-name)
```
* Resume kill process
```
kill -SIGCONT $(pgrep service-name)
```
* Terminat process
```
kill -SIGTERM $(pgrep service-name)
```
* Kill process immediately
```
kill -SIGKILL $(pgrep service-name)
```
