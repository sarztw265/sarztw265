# Prometheus Process Exporter

>reference:
>
>https://github.com/ncabatoff/process-exporter  
>https://developer.couchbase.com/tutorial-process-exporter-setup 

It needs to install Prometheus and Grafana before starting this part, reference [here](./Install%20Prometheus%20Server%20on%20CentOS%207%20RHEL%207.md).

## What Will We Do

1. [Create new group, user and directories for Prometheus](#1-create-new-group-user-and-directories-for-prometheus): If the prometheus user and group had been created, you can jump to create directories.
2. [Download and Install Process Exporter](#2-download-and-install-process-exporter)
3. [Create Process Exporter systemd unit file](#3-create-process-exoorter-systemd-unit-file)

## Steps

### 1. Create new group, user and directories for Prometheus

```
sudo groupadd --system prometheus

sudo useradd -s /sbin/nologin --system -g prometheus prometheus

sudo mkdir /etc/process-exporter
```

### 2. Download and Install Process Exporter

```
mkdir -p /tmp/process-exporter && cd /tmp/process-exporter  

wget https://github.com/ncabatoff/process-exporter/releases/download/v0.7.10/process-exporter-0.7.10.linux-amd64.tar.gz

tar -xvf process-exporter-0.7.10.linux-amd64.tar.gz

cd process-exporter-0.7.10.linux-amd64

> # Move files
sudo mv process-exporter /etc/process-exporter/ && cd /etc/process-exporter/

sudo rm -rf /tmp/process-exporter

sudo vim /etc/process-exporter/process-exporter.yml  
```
This yaml file can be named whatever you want, and it should contain below:
```
process_names:
  - name: "{{.Comm}} [{{.Username}}]"
    cmdline:
    - '.+'
```
The `process-exporter.yml` is a configuration for Process Exporter to select and group the processes to monitor, for more information to see https://github.com/ncabatoff/process-exporter?tab=readme-ov-file#configuration-and-group-naming

```
> # Set permissions
sudo chown -R prometheus:prometheus /etc/process-exporter
```

### 3. Create Process Exporter systemd unit file

Create file with `/etc/systemd/system/process-exporter.service` then edit file:
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

Then reload daemon and start Process Exporter
```
sudo systemctl daemon-reload

sudo systemctl start process-exporter

sudo systemctl enable process-exporter

sudo systemctl status process-exporter
```

Open Process Exporter port and reload firewall

```
sudo firewall-cmd --permanent --add-rich-rule 'rule family="ipv4" source address="<your IP>" port protocol="tcp" port="9256" accept'

sudo firewall-cmd --reload
```

Check firewall of the server and connect to `http://localhost:9256/metrics` to see if Process Exporter works well.