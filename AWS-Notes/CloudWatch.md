## Hands On
Step-1: Install amazon-linux-extra
``` 
 sudo amazon-linux-extras install epel -y
```
Step-2: Install stress tool
```
 sudo yum install stress -y
```
Step-3: Create stress process
```
 stress -c 4 -t 60 && sleep 60 && stress -c 4 -t 60 && sleep 60 && stress -c 4 -t 360 && sleep  && stress -c 4 -t 460 && sleep 30 && stress -c 4 -t 360 && sleep 60
```

## Create CPU Monitoring Alarm
- Go to CloudWatch service
- All Alarms -> create alarm
- Select matric -> EC2 -> Per instance matric
- Select CPUUtilization
- Select conditions
- Define threshold value
- Alarm state trigger -> In Alarm
- Select topic or create one
- Add name and description
