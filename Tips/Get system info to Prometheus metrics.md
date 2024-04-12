
https://www.binarytides.com/linux-get-gpu-information/  
https://www.baeldung.com/linux/check-monitor-active-gpu  
https://github.com/utkuozdemir/nvidia_gpu_exporter/tree/master  
https://grafana.com/grafana/dashboards/14574-nvidia-gpu-metrics/  

nvidia-smi

python3 -m venv tutorial-env  
source tutorial-env/bin/activate  
python  
python3.6 -m pip install notebook  
python3.6 -m pip install jupyter  
jupyter notebook  
deactivate  

----
Network load

https://linuxways.net/centos/how-to-install-nethogs-on-centos/#:~:text=Installing%20Nethogs%20on%20CentOS%201%20Step%201%3A%20Adding,syntax%20to%20use%20Nethogs%20is%20as%20follows%3A%20  
https://github.com/raboof/nethogs  

nethogs

sudo yum install ncurses*  
sudo yum install epel-release  
sudo yum install nethogs  
sudo setcap "cap_net_admin,cap_net_raw,cap_dac_read_search,cap_sys_ptrace+pe" /usr/local/sbin/nethogs  

----
get all user's home size

du -sh /home/* /root

```
#!/bin/bash
user_info=$(du -sm /home/* /root)
metric=""

while read -r size path; do
    user=$(basename "$path")
    metric+="user_home_usage_mb{user=\"$user\"} $size\n"
done <<< "$user_info"

echo -ne "$metric" | curl -X POST -H "Content-Type: text/plain" --data-binary @- "http://localhost:9091/metrics/job/test"
```
cron  
*/1  *  *  *  * root cd /etc/pushgateway-script && ./user_home_size
*/1  *  *  *  * root cd /etc/pushgateway-script && ./nethogs_network_catcher

