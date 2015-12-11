### VM Description
* Service VM: 192.168.1.28
* Master  VM: 192.168.1.29
* Work    VM: 192.168.1.31


## ON SERVICE MACHINE
**************************************************************
### 1. git clone source code
1.git clone frontend code      
```sh
         $ cd ~
         $ git clone http://220.228.145.216/tesseratic/Frontend.git
         $ cd ~/Frontend
         $ git checkout TCE-Trial-Dev-v1.0
```
**************************************************************
2.git clone backend code       
```sh    
     $ cd ~
     $ git clone http://220.228.145.216/tesseratic/Backend.git
     $ cd ~/Backend
     $ git checkout test
```             
**************************************************************
### 2. Build Image File
1.Build Frontend image file  
    Change `~/Frontend/src/config` to Your web page IP and protcol settings     
    For example:
```sh
        http://220.228.145.212:31000/   
```

   Build frontend image file
```sh
         $ cd ~/Frontend/dockerfile
         $ ./build.sh
```
**************************************************************
2.Build Backend image file

__(可以不改`~/Backend/src/app.py` =>先啟動service 再啟動endpoint)__ 
```sh
         $ ./kubectl create -f guestbook/guestbook/master.service.json   
         $ ./kubectl create -f guestbook/guestbook/herd.service.json        
         $ ./kubectl create -f guestbook/guestbook/master.endpoint.json      
         $ ./kubectl create -f guestbook/guestbook/herd.endpoint.json         
```
    
Change `~/Backend/src/app.py` line50 IP as Your master(Kubernetes) machine IP   
        For example:    
```sh
         baseurl = "http://192.168.1.29:8080/api/v1"
```
Change `~/Backend/src/app.py` line53 IP as Your service machine IP  
    For example:
```sh
         hred_ip = 'http://192.168.1.28:8090'
```
Build Backend image file
```sh
        $ cd ~/Backend/dockerfile
        $ ./build.sh
```
**************************************************************
### 3. Docker tag&push to service machine (private docker registry)      
1.Check docker tag unique names             
```sh
         $ curl http://localhost:5000/v2/app/tags/list
         $ curl http://localhost:5000/v2/dev/tags/list
```
**************************************************************
2.docker tag
```sh
    $ cd ~/Backend
    $ docker images
```
** should be two more image files app & dev     
docker tag [IMAGE ID] [PRIVATE/LOCAL DOCKER HUB]:[PORT]/[FILE NAME]:[TAG NAME]      
For example:
```sh
    $ docker tag 67473683Ffsf83 localhost:5000/dev:ac
    $ docker tag 6xxx4683Ffsddd localhost:5000/app:ac
```
**************************************************************
3.docker push   
check IP    
docker push [PRIVATE/LOCAL DOCKER HUB]:[PORT]/[FILE NAME]:[TAG NAME]    
For example:
```sh
    $ docker push localhost:5000/dev:ac
    $ docker push localhost:5000/app:ac
```
**************************************************************
## ON JUMP BOARD 
### 4. Create App&Dev Rc+Pod
(Execute K8s command, which is  
    Create Dev service  
    Create Dev Replica Controll + Pod   
    Create APP service  
    Create App Replica Controll + Pod)

```sh
        $ cd fet_150619/
        $ . ./open-sesame
```
**************************************************************
change `guestbook/guestbook/app-controller.json` line25 as    
```sh
"image":"192.168.1.28:5000/app:ac",
```
change `guestbook/guestbook/dev-controller.json` line25 as
```sh
"image":"192.168.1.28:5000/dev:ac",
```
check `guestbook/guestbook/app-service.json` line13 line14 as
```sh
"port":8000,
"nodePort":31000
```
check `guestbook/guestbook/dev-service.json` line13 line14 as
```sh
"port":80,
"nodePort":30001
```
**************************************************************

        $ ./kubectl create -f guestbook/guestbook/dev-controller.json
        $ ./kubectl create -f guestbook/guestbook/app-controller.json
        $ ./kubectl create -f guestbook/guestbook/app-service.json
        $ ./kubectl create -f guestbook/guestbook/dev-service.json
 
**************************************************************
## ON WORKER MACHINE
### 5. 打洞
    ssh -R 31000:localhost:31000 [Username]@[Public_IP]
    ssh -R 30001:localhost:30001 [Username]@[Public_IP]