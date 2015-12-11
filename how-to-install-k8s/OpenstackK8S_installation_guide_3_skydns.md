# openstack&kubernetes安裝手冊 - 3 (skydns)
 
 
### Step-1
將`skydns/skydns-rc.yaml`檔案中的資料作修改
1. `image`後面對應IP修改為service IP位置(3)
 
 
### Step-2 (注意事項)
如有對`skydns/kubeconfig_dns`或`skydns/kubeconfig_monitoring`檔案做修改，需將整個檔案內容作Base64的加密，並儲存到對應檔案，對應檔案如下。
1. `skydns/kubeconfig_dns`對應`skydns/secretyaml_dns`
2. `skydns/kubeconfig_monitoring`對應`skydns/secretyaml_monitoring`
 
 
### Step-3
依照此順序執行
 
    $ cd /home/ubuntu
    $ ./kubectl create -f secretyaml_dns
    $ ./kubectl create -f secretyaml_monitoring
    $ ./kubectl create -f skydns-rc.yaml
    $ ./kubectl create -f skydns-svc.yaml