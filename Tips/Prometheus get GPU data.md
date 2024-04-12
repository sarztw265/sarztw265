# Nvidia gpu exporter

wget https://github.com/utkuozdemir/nvidia_gpu_exporter/releases/download/v1.2.0/nvidia-gpu-exporter_1.2.0_linux_amd64.rpm

sudo rpm -i nvidia-gpu-exporter_1.2.0_linux_amd64.rpm

sudo systemctl status nvidia_gpu_exporter.service

curl http://localhost:9835/metrics

sudo firewall-cmd --permanent --add-rich-rule 'rule family="ipv4" source address="172.16.81.140/22" port protocol="tcp" port="9835" accept'

sudo firewall-cmd --reload