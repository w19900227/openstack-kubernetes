# openstack&kubernetes安裝手冊 - 4 (guestbook)
 
### Step-1 (`guestbook/frontend-controller.json`)
將`guestbook/frontend-controller.json`檔案中的資料作修改
1. `image`後面對應IP修改為service IP位置
 
以下相關檔案設定說明
1. `replicas` 用於控制產生container數量
2. `image`後面對應IP修改為service IP位置
3. `containerPort` container對外port，需與`guestbook/frontend-service.json`的`port`相同
 
 
### Step-2 (`guestbook/redis-master-controller.json`)
將`guestbook/redis-master-controller.json`檔案中的資料作修改
1. `image`後面對應IP修改為service IP位置
 
以下相關檔案設定說明
1. `replicas` 用於控制產生container數量
2. `image`後面對應IP修改為service IP位置
3. `containerPort` container對外port，需與`guestbook/redis-master-service.json`的`port`相同
 
 
### Step-3 (`guestbook/redis-slave-controller.json`)
將`guestbook/redis-slave-controller.json`檔案中的資料作修改
1. `image`後面對應IP修改為service IP位置
 
以下相關檔案設定說明
1. `replicas` 用於控制產生container數量
2. `image`後面對應IP修改為service IP位置
3. `containerPort` container對外port，需與`guestbook/redis-slave-service.json`的`port`相同
 
 
## 補充說明 *-service.json
 
### 補充說明-1 (`guestbook/frontend-service.json`)
`guestbook/frontend-service.json`檔案設定說明
1. `port` container對外port，需與`guestbook/frontend-controller.json`的`containerPort`相同
2. `nodePort` 外部連線到container的port
 
 
### 補充說明-2 (`guestbook/redis-master-service.json`)
`guestbook/redis-master-service.json`檔案設定說明
1. `port` container對外port，需與`guestbook/redis-master-controller.json`的`containerPort`相同
2. `nodePort` 外部連線到container的port
 
 
### 補充說明-3 (`guestbook/redis-slave-service.json`)
`guestbook/redis-slave-service.json`檔案設定說明
1. `port` container對外port，需與`guestbook/redis-slave-controller.json`的`containerPort`相同
2. `nodePort` 外部連線到container的port
 
 
### Step-4
依照此順序執行
 
    $ cd /home/ubuntu
    $ ./kubectl create -f frontend-service.json
    $ ./kubectl create -f redis-master-service.json
    $ ./kubectl create -f redis-slave-service.json
    
    $ ./kubectl create -f frontend-controller.json
    $ ./kubectl create -f redis-master-controller.json
    $ ./kubectl create -f redis-slave-controller.json
