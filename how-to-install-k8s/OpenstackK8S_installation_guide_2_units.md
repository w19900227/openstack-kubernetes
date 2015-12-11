# openstack&kubernetes安裝手冊 - 2 (units)
 
 
### Step-1
將一些需要檔案放置service提供後續安裝需要檔案抓取
 
    $ ssh core@service IP
    $ sudo su
    $ scp -r ubuntu@跳板機IP:/home/ubuntu/fet_150619/files/* /
 
同樣在JumpBoard上作業
### Step-2
將`units/herd.service`檔案中的資料作修改
1. `-etcd_machines`中的資料，依照etcd數量及IP位置做修改
2. `-k8s_host`對應master IP位置
 
 
### Step-3
將`units/setup-network-environment.service`檔案中的資料作修改
1. `/usr/bin/wget`後面對應的IP位置改為service IP位置
 
 
### Step-4
將`units/flannel.service`檔案中的資料作修改
1. `-/usr/bin/etcdctl`與`/opt/bin/flanneld`依照etcd數量及IP位置做修改
2. `/usr/bin/wget`後面對應的IP位置改為service IP位置
 
### Step-5
將`units/docker.service`檔案中的資料作修改
1. `--insecure-registry`後面對應IP修改為service IP位置
 
### Step-6
將`units/kube-apiserver.service`檔案中的資料作修改
1. `/usr/bin/wget`後面對應IP修改為service IP位置(有三行)
2. `--etcd_servers`依照etcd數量及IP位置做修改
 
 
### Step-7
將`units/kube-controller-manager.service`檔案中的資料作修改
1. `/usr/bin/wget`後面對應IP修改為service IP位置
 
 
### Step-8
將`units/kube-scheduler.service`檔案中的資料作修改
1. `/usr/bin/wget`後面對應IP修改為service IP位置
 
 
### Step-9
將`units/kube-kubelet.service`檔案中的資料作修改
1. `/usr/bin/wget`與`--pod_infra_container_image`後面對應IP修改為service IP位置
2. `--api_servers`後面對應IP修改為master IP位置
 
 
### Step-10
將`units/kube-proxy.service`檔案中的資料作修改
1. `/usr/bin/wget`後面對應IP修改為service IP位置
2. `--master`後面對應IP修改為master IP位置
 
 
### Step-11
依照此順序執行
 
 
    $ cd /home/ubuntu/fet_xxxxxx
 
#service VM: 
    $ ./fleetctl start units/trove.service
    $ ./fleetctl start units/distribution.service
    $ ./fleetctl start units/herd.service
 
#worker VM: 
    $ ./fleetctl start units/setup-network-environment.service
    $ ./fleetctl start units/flannel.service
    $ ./fleetctl start units/docker.service
 
    $ ./fleetctl start units/kube-apiserver.service
    $ ./fleetctl start units/kube-controller-manager.service
    $ ./fleetctl start units/kube-scheduler.service
 
    $ ./fleetctl start units/kube-kubelet.service
    $ ./fleetctl start units/kube-proxy.service
 
(verify)
    ./kubectl version
