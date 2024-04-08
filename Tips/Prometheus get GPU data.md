
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
*/1  *  *  *  * root /etc/pushgateway/user_home_size


tee?

ESC[?1049hESC[1;32rESC(BESC[mESC[4lESC[?7hESC[HESC[2JNetHogs version 0.8.5

ESC(BESC[0;7m    PID USER     PROGRAM                                                                  DEV        SENT      RECEIVED       ESC[4;7HESC(BESC[m? root     unknown TCPESC[4;103H0.000        0.000 KB/secESC[6;1HESC(BESC[0;7m  TOTAL                                                                                               0.000       0.000 KB/secESC[7;1H ^MESC(B
ESC[mESC[4dESC[2L  31059 promet.. /usr/local/bin/prometheusESC[4;91Hens192ESC[103G0.122          2.674 KB/secESC[5;4H1018 studen.. sshd: student1@pts/1ESC[5;91Hens192ESC[103G0.251      0.047 KB/secESC[8;105HESC(BESC[0;7m373          2.721
ESC(BESC[mESC[5;105H484ESC[5;117H105ESC[8;105HESC(BESC[0;7m605ESC[8;118H79
ESC(BESC[mESC[32;1HESC[?1049l^MESC[?1lESC>Ethernet link detected



?     root      unknown TCP            0.000        0.000 KB/sec
19225 studen..  sshd: student1@pts/1   0.254        0.059 KB/sec
