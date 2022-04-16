# Docker

###### tags: `DevOps`、`Docker`


## 什麼是Docker？
Docker 是一種容器化技術
### 部屬演進
![image alt](https://github.com/Poyaching/DevOps-docker/blob/main/image/01.jpg)
**圖片來源:Docker 部署入門完全指南-圖片速學攻略**

1. 應用程式皆放在同一台硬體上
    若有其中一個應用程式出問題，所有的應用程式都會一起有問題
2. 傳統主機模式
    直接讓一個應用程式放在一個硬體上，使每個應用程式不會互相影響，但所需的成本會極高。
3. VM模式
    在同一台硬體及作業系統上，利用Hypervisor建立起多個VM，並在VM上建立不同的作業系統，讓個應用程式存放在上面，雖然成本相較於傳統模式，已經降低許多成本，但是多個作業系統還是會造成部分成本存在。
4. 容器模式
    和VM模式一樣，在同一台硬體及作業系統上，但在並不是利用Hypervisor，而是使用 docker engine，並在上面建立不同的容器。容器會透過 docker engine 去跟底層的作業系統要資源，雖然是使用同一個作業系統的資源，但不會互相干擾，既可以大幅降低成本，也可以解決一開始存在的問題。
    
## Docker的三大功用
### 簡化部屬流程
在原本的部屬流程下，拿到`程式碼`後，必須建立起程式語言的`環境`，已即使程式碼正常運行的`指令`。但這會有些缺點，安裝環境本身就要很大的時間成本，運行指令又要避免輸入錯誤的人為疏失，
所以在docker的情況下，便可以排除這些問題
`程式碼`+`環境`+`指令` 透過 docker 打包成部屬包（Image），下次安裝時，直接執行部屬包即可

### 跨平台部屬
擁有了部屬包，可以將部屬包放置在雲端平台中（docker hub），在不同的作業系統中，只要安裝docker，就可以直接在docker運行，排除作業系統的影響。
![image alt](https://github.com/Poyaching/DevOps-docker/blob/main/image/02.jpg)

### 建立乾淨的測試環境
除了一般程式碼外，也可以將`測試資料`+`資料庫`+`運作指令`打包成部屬包，測試時，只要將程式部屬包及資料庫部屬包同時運行，就可以得到新的環境，即使測試後資料弄髒了，只要重新佈署，又是一個新的測試環境。

## Docker 如何在不同的作業系統上運作
### Linux
在 Linux 上首先要先安裝 Docker Engine ， Docker Engine 負責與 Linux 的底層 Linux Container 的做溝通， Linux Container 就會去幫忙分配 docker 的資源
### Windows、macOS
如果不是 Linux 系統，以前的做法是在作業系統上，再裝一個虛擬機，在虛擬機上安裝 Linux ，便可以像是 Linux 使用 docker。
又或者還有另一個做法，另一個也是相似的方式，利用系統本身的虛擬機，去建立虛擬作業系統。
![image alt](https://github.com/Poyaching/DevOps-docker/blob/main/image/03.jpg)
**圖片來源:Docker 部署入門完全指南-圖片速學攻略**
### 長大的Windows
近年 MS 努力走向 Open Source ，例如 redis，做一個新的 Container 來打應用程式。但在 docker 是使用合作模式，Docker 在建立一個 Windows API 。

## Docker 基礎架構介紹

![image alt](https://github.com/Poyaching/DevOps-docker/blob/main/image/04.jpg)

### Linux指令
`ls` 列出所有
`cat` 讀取檔案內容並顯示在 console 或 terminal
`/bin/sh` 指令介面
`tail` 讀取檔案最尾端的資料，-f fileName `輸出檔案

### Dockerfile
`程式碼`+`環境`+`指令` 透過 Dockerfile 產生 Docker Image 
每一條指令都會產生一個臨時的container，執行完指令，然後產生一個image layer
預設名稱必須是 `Dockerfile` ， 如果不使用預設名稱，必須在 build 時加上 -f (Dockerfile)

``` cmd
docker build  -f DockerfileName .
```

#### 語法介紹
1. form
    form 後面必須要加上 base image ，可以從 docker hub 內找尋 base image

2. run
     RUN 指令後面放任何base Image 的指令
     前面有提到，在跑每一條指令時，會產生一個container，又會產生一層image layer，有些比較小的指令，就可以利用 && 和其他指令縮成一條

     * &&
         如果有以下的指令，我們想要把它合併，就可以使用 &&
         ``` cmd
         run cd /var/www/localhost/htdocs
         run pwd
         ```
         ``` cmd
         run cd /var/www/localhost/htdocs && pwd
         ```
     * \
         雖然合併，但還是想要換行
         ``` cmd
         run cd /var/www/localhost/htdocs \ 
                && pwd
         ```
3. ENV
    用來設定環境變數
    以下的指令是cd 到該資料夾，然後寫入 "123456"，由於內容都是重複，就可以使用環境變數
    ``` cmd
     run cd /var/www/localhost/htdocs && echo "123456"
     run cd /var/www/localhost/htdocs && echo "123456"
     run cd /var/www/localhost/htdocs && echo "123456"
     ```
     ``` cmd
     ENV mypath /var/www/localhost/htdocs
     run cd ${mypath} && echo "123456"
     run cd ${mypath} && echo "123456"
     run cd ${mypath} && echo "123456"
     ```

4. worldir
    雖然上面的 ENV 已經省略的很多東西，但如果想將 cd 的內容直接抽出來，在docker執行起來時候的預設目錄位置，就需使用worldir
     ``` cmd
     ENV mypath /var/www/localhost/htdocs
     worldir ${mypath}
     run  echo "123456"
     run  echo "123456"
     run  echo "123456"
     ```
5. arg
    建立一個我們在 build image 時可以去改變的變數
     ``` cmd
     ENV mypath /var/www/localhost/htdocs
     arg data = 111111 
     worldir ${mypath}
     run  echo "${data}"
     run  echo "${data}"
     run  echo "${data}"
     ```
     在 build 時，直接去改變變數內容
     ``` cmd
     docker build -t ImageName --build-arg data=99999 .
     ```
6. copy
    從外界拿一個檔案，放進 container 內

7. entrypoint 
    在運行 container 時，後面可以加一些指令，指令Instance啟動後，程式的進入點
    ``` cmd
    docker container run -it -d  --name ImageName tail -f /dev/XXX
    ```
    但若不想要在運行時再打上這些指令，可以直接寫在 Dockerfile 內，就會需要用到 entrypoint 建立預設指令
    ``` cmd
    ENTRYPOINT ["tail", "-f"", "/dev/XXX"] 
    ```
    運行指令就可以剩下所需的內容
    ``` cmd
    docker container run -it -d  --name ImageName
    ```
8. expose
    指定所有發布的port
9. cmd
    指定Instance啟動後所要執行的指令
    在指行 docker run 的指令時會直接呼叫開啟 Tomcat Service
     ``` cmd
     ENV mypath /var/www/localhost/htdocs
     arg data = 111111 
     worldir ${mypath}
     run  echo "${data}"
     run  echo "${data}"
     run  echo "${data}"
     copy ./cccc.txt ./
     ```

10. MAINTAINER 
    用來說明，撰寫和維護這個 Dockerfile 的人是誰，也可以給 E-mail的資訊

11. add 
    把 Local 的檔案複製到 Image 裡，如果是 tar.gz 檔複製進去 Image 時會順便自動解壓縮。Dockerfile 另外還有一個複製檔案的指令 COPY 未來還會再介紹

### Docker Image
Docker Image 為部屬包，可以攜帶或者放置在 docker hub 上

#### 指令
1. 查詢所有image
    ``` cmd
    docker images 
    ```
2. 在 docker hub 上拉下 image 
    ``` cmd
    docker pull ImageName : tag 
    ```
3. 產生一個image
    * -t 代表take image名稱，如果是要將檔案推上docker hub ，命名就必須是 帳號/image name
    * . 會看本地當下目錄 Dockerfile 的檔案
    * --build-arg 代表可以放入參數（key = value）
    ``` cmd
    docker build -t ImageName --build-arg 設定參數 .
    ```
4. 登入 docker hub 帳號
    ``` cmd
    docker login 
    ```
5. 推 image 到 docker hub 
    ``` cmd
    docker push  ImageName
    ```
6. 清除image
    必須注意清除前是否有存在在container內，如果有的話需先刪除
    rmi = remove image 
    ``` cmd
    docker rmi ImageName
    ```


### Docker container
Docker Image 運行的地方，但 container 在沒有一個程序一直跑的情況下，會自行清掉

#### 指令
1. 建立 container
    -it 建造一個Interactive mode 可以讓我們跑一個需要的程序，一直跑下去，但只要下 `exit` 指令離開後，就會結束
    -d  daemon 讓 container 在背景執行 
    --name
    ls 列出所有的檔案
    -p OS的port:container內的port號 = 將 OS 的 port 對應到 container內的port號
    --rm 用完即刪

    ``` cmd
    docker container run -it -d  --name ImageName:tag Linux指令
    docker run --name ImageName:tag Linux指令
    ```
2. 在 container 內啟用新的程序
    ``` cmd
    docker exec -it containerID 
    ```
3. 關閉 container 
    ``` cmd
    docker container stopcontainerID 
    ```

4. 刪除 container
    ``` cmd

    docker container rm containerID 

    ```
5. 列出所有 container
    -a == --all
    ``` cmd
    docker container ls (所有正在啟動的)
    docker ps 
    docker container ls -a (所有的)
    docker ps -a

    ```


## Docker network 網路模式

docker run 創建 Docker 容器時，可以用 –net 選項指定容器的網絡模式

### 相關指令
1. 列出所有的 network
    ``` cmd
    docker network ls 
    ```

2. 在運作時設定network
    ``` cmd
    docker run -d --network 網路模式 --name ImageName:tag 
    ```

3. 查看網路介面
    ``` cmd
    docker network inspect networkName 
    ```
如果我們有將 Containers 設定成該模式，可以在 "Containers" 內中看到看到  Containers ID
![](https://github.com/Poyaching/DevOps-docker/blob/main/image/05.jpg)




![Docker 部署入門完全指南-圖片速學攻略](https://github.com/Poyaching/DevOps-docker/blob/main/image/06.jpg)
**圖片來源:Docker 部署入門完全指南-圖片速學攻略**
### host 模式
使用 –net=host 指定。
container 的網路設定和實體主機使用相同的網路設定，所以 container 裡面也就可以修改實體機器的網路設定，因此使用此模式需要考慮網路安全性上的問題

### none 模式
使用 –net=none 指定。
網路功能是關閉的

### bridge 模式（預設）

![https://godleon.github.io/blog/Docker/docker-network-bridge/](https://github.com/Poyaching/DevOps-docker/blob/main/image/07.jpg)


使用 –net=bridge 指定。
類似於NAT（網路地址轉換模式），在docker環境中存在數個 bridge Network ，每一個要使用 bridge 模式的 container 必須要與 bridge Network 裡面其中一個 IP 對接，如果不同 container 中要互相網路溝通，那麼就必須將需要的溝通的  container 與相同的 bridge Network 內的 IP 連結，反之如果是不同的 bridge Network 就無法互相溝通。

#### 相關指令
1. 建立 bridge Network
    ``` cmd
    docker network create --driver 網路模式 bridgeName
    ```
    ![](https://github.com/Poyaching/DevOps-docker/blob/main/image/08.jpg)
    ```
    "IPAM": {
        "Driver": "default",
        "Options": {},
        "Config": [
        {
            "Subnet": "172.18.0.0/16",
            "Gateway": "172.18.0.1"
        }
        ]
    },
	```
### container 模式
使用 –net=container:NAME_or_ID 指定。
雖然都是使用 docker 環境內的網路但與 bridge 模式不同的是，他會與其他的 container 有一模一樣的 IP ，而 bridge 模式則每一個 IP 都不相同。


## Docker Volume
![](https://github.com/Poyaching/DevOps-docker/blob/main/image/09.jpg)
**圖片來源:Docker 部署入門完全指南-圖片速學攻略**
1. 資料想永久儲存
預設執行 Docker Container 的時侯，檔案系統會分為 Image 層、Init 層以及可讀可寫層這三個部份。
把容器刪除後，存放在上面的資料也就會跟著刪除掉，又或者想要在docker外取用資料時，很難取用，這時候可以將不想要被刪除的資料存放在實體機器上，避免資料。
2. 更改程式碼
一般情況下，容器啟動後，想要改變程式碼，必須透過進入container改變內部的程式碼，但若不想要進入容器內部就改變時，可以透過Docker Volume。

### 相關指令
1. 列出所有的 Volume
    ``` cmd
    docker volume ls 
    ```

2. 建立新的 Volume
    ``` cmd
    docker volume create VolumeName
    ```

3. 查看 Volume 細節
    ``` cmd
    docker volume inspect VolumeName
    ```
    ```
    [
    {
    "CreatedAt": "2022-04-02T02:05:20Z",
    "Driver": "local",
    "Labels": {},
    "Mountpoint": "/var/lib/docker/volumes/volume-001/_data",
    "Name": "volume-001",
    "Options": {},
    "Scope": "local"
    }
    ]
    ```
    "Mountpoint": "/var/lib/docker/volumes/volume-001/_data" 是指`OS系統上的檔案位置`

4. 將 Volume 加入 docker run 
    ``` cmd
    docker run -d -p 8080:80 -v VolumeName:container裡面的資料夾 --name ImageName
    ```
    如果沒有預先建立 Volume，也可以下指令，但就是由docker 自動幫我們建立 Volume
    ``` cmd
    docker run -d -p 8080:80 -v container裡面的資料夾 --name ImageName:
    ```


## Docker compose 資源管理
Docker compose 會協助我們管理 `service(container(image、docker file))`、`network`、`volumes`、並提供我們可以一次啟動多個 container 的方法

官網對於 Docker compose 可以管理應用程式的所有生命週期，包括：
* 啟動、停止和重新生成服務
* 查看正在運行的服務的狀態
* 流式傳輸正在運行的服務的日誌輸出
* 在服務上運行一次性命令

### Docker compose yml 寫法

``` cmd yml
# Docker compose 的版本
version: "3.8" 

# service 的區塊：container
services:
  # container 名稱 container_001，可以以寫成container_name
  container_001: 
  	
	# build docker
	build:
		#dockerfile的位置
		context: .
		# dockerfile的參數
		args:
			Key :value
			
	# image 名稱
	image: image_001
	# port
	ports:
		- "8080:80"
	#環境變數
	enviroment:
		- key = ${環境檔案變數}
	#cmd指令，可以覆蓋掉dockerfile內的CMD指令
	command: 需要使用的指令
	networks:
		- network_001
	volume:
		- volume_001:container目錄位置

# network 的區塊：
	#沒有設定，compose 會自動幫你建 bridge 模式的 network
	#並將所有的 container 都放進去。
	#如果將多個service設定成同一個 network ，就可以使用 internal domain name
	#例如：command: ./wait-for-it.sh containerName:3306 -- java ……
	#其中看到的 containerName:3306 就會直接幫我們解析 containerName 所對應到的 IP 位置
network:
  # network 名稱 network_001
  network_001: 
  network_002: 

# Volume 的區塊：
volume:
  # volume 名稱 volume_001
  volume_001: 

```

### 相關指令
1. 建立 docker compose，--no-cache 指說每次build都會重新跑一次
    ``` cmd
    docker -compose build --no-cache 
    ```
2. 啟動 docker compose
    ``` cmd
    docker -compose up -d 
    ```
3. 結束 docker compose
    ``` cmd
    docker -compose down
    ```

## Docker swarm 規模化部屬
![](https://github.com/Poyaching/DevOps-docker/blob/main/image/10.jpg)
**圖片來源:Docker 部署入門完全指南-圖片速學攻略**
Docker swarm 可以讓我們進行規模化的`自動化部屬`，讓網站可以應付高流量的需求。

docker engine 進入到 swarm Mode，docker engine就會被稱為node，node 又可以分為不同腳色：manager node 負責統整所有 node，另一個是 work node 必須向 manager node 註冊，負責實際執行。

manager node 內會有一個service 裡面包含了 image & 啟動指令，當manager node 啟動時，他會建立很多個task，而這些task是屬於在service下，manager node會有一個目標，就是將所有的task啟動，但因為 manager node 只有負責統整，所以真正啟動的是被隨機指派的 work node ，work node 就會啟動一個新的容器，如果成功後，狀態就會顯示成功，如果啟動失敗， manager node 又會重新指派，產生一個容器直到成功。



|  | Docker swarm | k8s |GKE AKE EKS |ECS |
| -------- | -------- | -------- |-------- |-------- |
| 來源     | docker     | google 開源 |雲端上的k8s |aws |
| 適合情況     | 小     | 大型 |雲環境 |Fargate Mode |


## Docker v.s Podman

## Docker 安全
[幾個小技巧，讓你寫出更安全的Dockerfile]()

