# Build process monitor with Prometheus and Grafana

>reference:  
>https://devconnected.com/monitoring-linux-processes-using-prometheus-and-grafana/  
>https://prometheus.io/docs/practices/pushing/#alternative-strategies  
>https://prometheus.io/docs/instrumenting/pushing/  
>https://github.com/prometheus/pushgateway/ 

It needs to install Prometheus and Grafana before starting this part, reference [here](./Install%20Prometheus%20Server%20on%20CentOS%207%20RHEL%207.md).

## How to Get Processes Information

Pushgateway  
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

## What Will We Do

### Install Pushgateway

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


---
## Install Process Exporter

https://github.com/ncabatoff/process-exporter  
https://developer.couchbase.com/tutorial-process-exporter-setup  

mkdir -p /tmp/process-exporter && cd /tmp/process-exporter  

wget https://github.com/ncabatoff/process-exporter/releases/download/v0.7.10/process-exporter-0.7.10.linux-amd64.tar.gz

tar -xvf process-exporter-0.7.10.linux-amd64.tar.gz

cd process-exporter-0.7.10.linux-amd64

sudo mkdir /etc/process-exporter

sudo mv process-exporter /etc/process-exporter/ && cd /etc/process-exporter/

sudo rm -rf /tmp/process-exporter

sudo vim /etc/process-exporter/process-exporter.yml  
```
process_names:
  - name: "{{.Comm}}"
    cmdline:
    - '.+'
```
sudo chown -R prometheus:prometheus /etc/process-exporter

sudo vim /etc/systemd/system/process-exporter.service
```
[Unit]
Description=Process Exporter
Documentation=https://github.com/ncabatoff/process-exporter
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
User=prometheus
Group=prometheus
ExecStart=/etc/process-exporter/process-exporter \
  --config.path /etc/process-exporter/process-exporter.yml
ExecReload=/bin/kill -HUP $MAINPID
Restart=on-failure
RestartSec=5s

[Install]
WantedBy=default.target
```
sudo systemctl daemon-reload  
sudo systemctl start process-exporter  
sudo systemctl enable process-exporter  
sudo systemctl status process-exporter  

sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="172.16.81.140/22" port port="9256" protocol="tcp" accept'

sudo firewall-cmd --reload

curl http://localhost:9256/metrics


test python process  
https://docs.python.org/zh-tw/3/tutorial/venv.html  


## Steps