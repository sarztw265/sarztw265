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



## Steps