# Prometheus Pushgateway

>reference:  
>https://devconnected.com/monitoring-linux-processes-using-prometheus-and-grafana/  
>https://prometheus.io/docs/practices/pushing/#alternative-strategies  
>https://prometheus.io/docs/instrumenting/pushing/  
>https://github.com/prometheus/pushgateway/ 

## Get Processes Information

script use ps command (user, pid, cpu, ram)  
```
#!/bin/bash
process_info=$(ps aux)
cpu_usage_metrics=""

while read -r process
do
   cpu_usage=$(awk '{print "cpu_usage{process=\""$11"\", pid=\""$2"\", user=\""$1"\"}", $3}')
   cpu_usage_metrics="$cpu_usage_metrics$cpu_usage"
done <<< "$process_info"

curl -X POST -H "Content-Type: text/plain" --data "$cpu_usage_metrics
" http://localhost:9091/metrics/job/node_process/instance/localhost
```
```
#!/bin/bash
process_info=$(ps aux)
mem_usage_metrics=""

while read -r process
do
   mem_usage=$(awk '{print "mem_usage{process=\""$11"\", pid=\""$2"\", user=\""$1"\"}", $4}')
   mem_usage_metrics="$mem_usage_metrics$mem_usage"
done <<< "$process_info"

curl -X POST -H "Content-Type: text/plain" --data "$mem_usage_metrics
" http://localhost:9091/metrics/job/node_process/instance/localhost
```

use top, execute by /etc/crontab every minute
```
#!/bin/bash
process_info=$(top -b -n 1 | tail -n +8)
cpu_usage_metrics=""

while read -r process
do
   cpu_usage=$(awk '{print "cpu_usage{process=\""$12"\", pid=\""$1"\", user=\""$2"\"}", $9}')
   cpu_usage_metrics="$cpu_usage_metrics$cpu_usage"
done <<< "$process_info"

curl -X POST -H "Content-Type: text/plain" --data "$cpu_usage_metrics
" http://localhost:9091/metrics/job/test
```
```
#!/bin/bash
process_info=$(top -b -n 1 | tail -n +8)
memory_usage_metrics=""

while read -r process
do
   memory_usage=$(awk '{print "memory_usage{process=\""$12"\", pid=\""$1"\", user=\""$2"\"}", $10}')
   memory_usage_metrics="$memory_usage_metrics$memory_usage"
done <<< "$process_info"

curl -X POST -H "Content-Type: text/plain" --data "$memory_usage_metrics
" http://localhost:9091/metrics/job/test
```




sudo yum install iotop  
sudo iotop -P (for process, use flag "-P" to show PID instead of TID)  

sudo yum install sysstat  
iostat (for disk)


## Install Pushgateway

mkdir -p /tmp/pushgateway && cd /tmp/pushgateway

wget https://github.com/prometheus/pushgateway/releases/download/v1.7.0/pushgateway-1.7.0.linux-amd64.tar.gz

tar xvzf pushgateway-*.tar.gz

cd pushgateway*/

sudo mv pushgateway /etc/pushgateway

rm -rf /tmp/pushgateway

sudo chown -R prometheus:prometheus /etc/pushgateway

sudo chmod 775 /etc/pushgateway

sudo ./pushgateway &

open firewall for 9091 port

check localhost:9091

touch process_info_catcher