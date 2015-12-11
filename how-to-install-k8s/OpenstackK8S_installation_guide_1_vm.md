# openstack&kubernetes安裝手冊 - 1 (VM)
 
 
### Step-1
基本資料檔案`fet_150619`
 
 
### Step-2
*必須先申請帳/密 http://domain/horizon(openstack入口網)
demo/
 
 
### Step-3
需要兩個對外IP
 
 
### Step-4 (Network)
建立network(Network -> network)
1. `Subnet`
    1. Network Address -> 192.168.0.1/24
2. `Subnet Detail`
    2. DNS Name Servers -> 8.8.8.8
 
### Step-5 (Router)
建立router(Network -> routers)
1. 點選router name
2. 進入interfaces畫面
3. 建立interface
    1. `Subnet` -> 選擇剛剛建立的Network
 
### Step-6 (Access & Security) [2]
建議開啟port
1. TCP 22
2. TCP 30000 - 32767
 
 
### Step-7 (Instances - 跳板機)
建置一個跳板機，環境建議範例，依照以下步驟建立完成後，將step-1的檔案放置/home/ubuntu路徑底下
1. Instances - 跳板機
    1. `Details`
        1. `Flavor` => m1.small
        2. `Instance Count` => 1 
        3. `Instance Boot Source` => boot from image
        4. `Image Name` => ubuntu-14.04-x86_64(範例)
    2. `Access & Security`
        1. 選擇當初設定的(Step-6)
    3. `Networking`
        1. 選擇當初設定的(Step-4)
    4. `Post-Creation`
        1. 需輸入資料(如:vm/first_base.yaml)


#cloud-config
password: nick321
chpasswd: { expire: False }
ssh_pwauth: True
 
 
### Step-8 (產生ssh-key)
ssh連線至跳板機，並執行以下指令後獲得的key字串複製下來，並對檔案做修改`vm/etcd2.yaml`、`vm/service.yaml`、`vm/master.yaml`、`vm/worker.yaml`，將以上檔案中的`ssh_authorized_keys`做修改
 
    $ ssh ubuntu@ip
    $ ssh-keygen -t rsa
    $ cat ~/.ssh/id_rsa.pub
 
 
### Step-9 (Instances - etcd)
etcd建議3台以上，盡量以奇數為主
1. 執行此連結`https://discovery.etcd.io/new?size=3`，此連結`size`參數，需與你建立數量相同
2. 複製第一步驟產生出來連結(如：`https://discovery.etcd.io/1a0c2211dd87aacd20fc047d2d21c12a`)
3. 開啟`vm/etcd2.yaml`，將第二步驟連結覆蓋到etcd2.yaml檔案中的`discovery`，並`儲存`
4. Instances - etcd
    1. `Details`
        1. `Instance Name` => etcd
        2. `Flavor` => m1.small
        3. `Instance Count` => 3 (與你size步驟一size數量相同)
        4. `Instance Boot Source` => boot from image
        5. `Image Name` => CoreOS-723.3.0(範例)
    2. `Access & Security`
        1. 選擇與跳板機相同選項(Step-6)
    3. `Networking`
        1. 選擇與跳板機相同選項(Step-4)
    4. `Post-Creation`
        1. 需輸入資料(如:vm/etcd2.yaml)
    5. (verify) 回到jumpboard VM, enter "ssh core@192.168.x.x"
    6. (verify) from browser, go to link: https://discovery.etcd.io/1a0c2211dd87aacd20fc047d2d21c12a
 
 
### Step-10 (Instances - service)
service建議1台
1. 開啟`vm/service.yaml`，將檔案中的`content`依照所開啟etcd數量及IP位置在`Environment=FLEET_ETCD_SERVERS`後面做修改，`port不需要更動`，並`儲存`
2. Instances - service
    1. `Details`
        1. `Instance Name` => service
        2. `Flavor` => m1.small
        3. `Instance Count` => 1
        4. `Instance Boot Source` => boot from image
        5. `Image Name` => CoreOS-723.3.0(範例)
    2. `Access & Security`
        1. 選擇與跳板機相同選項(Step-6)
    3. `Networking`
        1. 選擇與跳板機相同選項(Step-4)
    4. `Post-Creation`
        1. 需輸入資料(如:vm/service.yaml)
    5. (verify) 回到jumpboard VM, enter "ssh core@192.168.x.x"
 
 
### Step-11 (Instances - master)
master建議一台
1. 開啟`vm/master.yaml`，將檔案中的`content`依照etcd數量及IP位置在`Environment=FLEET_ETCD_SERVERS`後面做修改，port不需要更動，並`儲存`
2. Instances - master
    1. `Details`
        1. `Instance Name` => master
        2. `Flavor` => m1.small
        3. `Instance Count` => 1
        4. `Instance Boot Source` => boot from image
        5. `Image Name` => CoreOS-723.3.0(範例)
    2. `Access & Security`
        1. 選擇與跳板機相同選項(Step-6)
    3. `Networking`
        1. 選擇與跳板機相同選項(Step-4)
    4. `Post-Creation`
        1. 需輸入資料(如:vm/master.yaml)
    5. (verify) 回到jumpboard VM, enter "ssh core@192.168.x.x"
 
 
### Step-12 (Instances - worker)
worker建議兩台
1. 開啟`vm/worker.yaml`，將檔案中的`content`依照etcd數量及IP位置在`Environment=FLEET_ETCD_SERVERS`後面做修改，port不需要更動，並`儲存`
2. Instances - worker
    1. `Details`
        1. `Instance Name` => worker
        2. `Flavor` => m1.small
        3. `Instance Count` => 2
        4. `Instance Boot Source` => boot from image
        5. `Image Name` => CoreOS-723.3.0(範例)
    2. `Access & Security`
        1. 選擇與跳板機相同選項(Step-6)
    3. `Networking`
        1. 選擇與跳板機相同選項(Step-4)
    4. `Post-Creation`
        1. 需輸入資料(如:vm/worker.yaml)
    5. (verify) 回到jumpboard VM, enter "ssh core@192.168.x.x"
 
 
### Step-12
修改檔案`open-sesame`
 
export ETCDCTL_PEERS=修改成任何一台etcd IP，port不變
 
export FLEETCTL_TUNNEL=修改為service IP，port不變
 
export KUBERNETES_MASTER=修改為master IP，port不變
 
執行
 
    $ cd /home/ubuntu/fet_xxxxxx
    $ . ./open-sesame
    $ ./check
