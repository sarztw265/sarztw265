# Jenkins Docker安裝

https://hub.docker.com/r/jenkins/jenkins/
https://www.jenkins.io/doc/book/installing/docker/#downloading-and-running-jenkins-in-docker

## 拉取映像檔
`docker pull jenkins/jenkins`

## 建立橋接網路
`docker network create jenkins`
橋接網路在此被命名為jenkins

## 起容器
```
docker run \
  --name jenkins-docker \
  --rm \
  --detach \
  --privileged \
  --network jenkins \
  --network-alias docker \
  --env DOCKER_TLS_CERTDIR=/certs \
  --volume jenkins-docker-certs:/certs/client \
  --volume jenkins-data:/var/jenkins_home \
  --publish 2376:2376 \
  docker:dind \
  --storage-driver overlay2
  ```
  
  解釋：
  --name 命名容器為jenkins-docker
  --rm 關閉時自動刪除該容器
  --detach 背景執行容器，可以用 `docker stop jenkins-docker` 關閉該容器
  --privileged 讓容器內的root有宿主機完整的root權限；[參考1](https://mileslin.github.io/2019/05/%E5%9C%A8%E5%AE%B9%E5%99%A8%E4%B8%AD%E5%8F%96%E5%BE%97%E7%89%B9%E6%AC%8A%E5%AD%98%E5%8F%96%E6%AC%8A%E9%99%90/)和[參考2](https://blog.csdn.net/wangxuelei036/article/details/107457712) 
  --network 指定容器用的網路，這邊使用前面建立的叫做jenkins的橋接網路
  --network-alias 設定容器在網路中的別名而不是使用IP，這邊直接叫docker
  --env 環境變數，這邊設定 `DOCKER_TLS_CERTDIR` 是容器中的路徑 `/certs`
  --volume 指定/certs/client和/var/jenkins_home要映射到本機的哪裡
  --publish 開放本機的埠號給對應的docker埠號
  
  https://zoejoyuliao.medium.com/%E7%94%A8-docker-%E5%AE%89%E8%A3%9D-ci-%E5%B7%A5%E5%85%B7-jenkins-347ffe630e40
  ```
 docker run \
  --name jenkins \
  --detach \
  -u root \
  -p 8080:8080 \
  -v jenkins-data:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v "$HOME":/home \
  jenkinsci/blueocean
 ```
  
  ## 進入容器
  查看容器ID
  `docker container ls`
  找到NAMES是jenkins-docker的容器ID
  
  `docker exec -it <容器ID> /bin/sh`
  jenkins官方的映象檔裡沒有bash，所以用sh進入
  
  ## 啟動容器
  `docker start <container_id>`