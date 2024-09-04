# Linux常用指令

## 壓縮
`tar -zcvf 檔名.tar.gz 要備份的檔案`
`tar -jcvf 檔名.tar.bz2 要備份的檔案`
`tar -Jcvf 檔名.tar.xz 要備份的檔案`

## 解壓縮
`tar -zxvf 檔名.tar.gz`
`tar -jxvf 檔名.tar.bz2`
`tar -Jxvf 檔名.tar.xz`

## 計算檔案大小並排序
`du -sh --time .[^.]* * | sort -rh`

## 不影響檔案寫入的清除檔案方式
`echo -n "" > 檔案`

## 查找大量CNAME
在要執行的地方先建立 `domain.txt` 這個檔案，放入域名格式如下
```
mhydve.com
nerbdu.com
jmfgnc.com
lbxhnl.com
lrvxbt.com
kjbhwi.com
ktdjov.com
```

然後執行
`filename="domain.txt"; while IFS= read -r domain; do dig CNAME $domain | grep "ANSWER SECTION" -A 1; done < "$filename"`

## Ubuntu列出監聽中的port
`ss -tulpn | grep LISTEN`
`netstat -tulpn | grep LISTEN`

## 個別壓縮資料夾中的每個檔案
```
#!/bin/bash

log_directory="/opt/app/task/log/xxladmin/2023-09-22"

for file in "${log_directory}"/*.log; do
    if [[ -f "$file" ]]; then
        base_name=$(basename "$file" .log)
        tar -czvf "${log_directory}/${base_name}.tar.gz" "$file"
        rm "${log_directory}/${base_name}.log"
    fi
done
```

## 查看系統版本
`cat /etc/os-release`

## 以systemd管理service
設定檔放在 `/etc/systemd/system/` 底下
```
[Unit]
Description=My Task Service
After=network.target

[Service]
User=<your_username>
Group=<your_groupname>
WorkingDirectory=<path_to_working_directory>
ExecStart=<path_to_task_executable>
Restart=always
RestartSec=3
StartLimitInterval=0
StandardOutput=journal
StandardError=journal
SyslogIdentifier=<service_name>

[Install]
WantedBy=multi-user.target
```
User：指定服務運行的使用者名稱。
Group：指定服務運行的使用者所屬的群組名稱。
WorkingDirectory：指定服務的工作目錄的路徑。
ExecStart：指定要執行的可執行文件或腳本的路徑。您可以使用絕對路徑或相對路徑。
Restart：指定服務的重啟行為。可用的值包括always（始終重啟）、on-failure（僅在非正常退出時重啟）和never（從不重啟）。
RestartSec：指定重啟間隔的秒數。例如，RestartSec=3表示在服務終止後的3秒後重啟服務，預設為100毫秒（100ms）。
StartLimitInterval：指定重啟的限制間隔。預設情況下，如果服務在5秒內啟動次數超過5次，則系統將停止重啟該服務。設置為0表示沒有限制。
StandardOutput：指定標準輸出的目的地。可用的值包括none（不輸出）、journal（使用systemd日誌）和syslog（使用系統日誌，舊版不支援）。
StandardError：指定標準錯誤輸出的目的地。可用的值與StandardOutput相同。
SyslogIdentifier：指定服務的識別符號，用於標識系統日誌中的該服務輸出。

`sudo chmod 644 /etc/systemd/system/服務.service`
`sudo systemctl daemon-reload`
`sudo systemctl start 服務`
`sudo systemctl enable 服務`
`sudo systemctl status 服務`

## 用journalctl輸出systemd日誌
`journalctl -u 服務名稱 > 指定路徑`
例如
`journalctl -u nginx > 指定路徑`

## 利用外部API查看自身IP
`curl ifconfig.me`

## JSON格式處理器
`jq`

e.g. `docker container inspect --format='{{json .State}}' 49a13d02c077 | jq`

https://www.onejar99.com/jq_commands/

## CentOS查看CPU數量
`nproc --all`