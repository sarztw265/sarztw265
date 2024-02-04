# Docker常用指令

## 容器在宿主機的輸出路徑
`/var/lib/docker/containers/容器ID`

## 看nginx log
1. 到輸出路徑看
2. 到容器內的 `/var/log/nginx/` 看
3. `docker logs -n 50 -f nginx`

## Nginx
nginx檔案位置
`/opt/nginx/volumes/nginx/conf.d`

檢查語法
`docker exec nginx nginx -t`

重啟nginx
`docker exec nginx nginx -s reload`

## 重啟容器
`docker restart <容器ID>`

## 清除不用的image
`docker image prune`

https://blog.miniasp.com/post/2022/11/18/docker-image-prune-notes