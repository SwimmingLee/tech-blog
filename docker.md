# Docker 사용학기

도커를 사용해보고자 합니다. aws에 도커를 이용해서 웹 서버를 배포해고자 합니다. 

## 도커를 왜 사용할까?

버전관리!! 배포

특히 당근마켓 사례!!



그전에 도커를 잠깐 알아보자.

확장성

- 이미지를 만들어 놓고 컨테이너는 그냥 띄우기만 하면 됨
- 다른 서버로 서비스를 옮기거나 새로운 서버에 서비스를 하나 더 띄우는 건 docker run 명령어 하나로 끝
- 개발서버 띄우기도 간편, 테스트 서버 띄우기도 간편

표준성

- 도커를 사용하지 않는 경우 ruby, nodejs, go 로 만든 서비스들의 배포 방식이 제각각 다름
- 컨테이너라는 표준으로 배포하므로 모든 서비스들의 배포과정 동일

이미지

- 이미지에서 컨테이너를 생성하기 떄문에 이미지 만드는 과정이 필요

설정

- 설정은 보통 환경변수로 제어함
- MYSQL_PASS 와 같은 환경변수 같이 지정
- 하나의 이미지가 환경변수에 따라 동적으로 설정파일을 생성하도록 만들어져야 함

공유자원

- 컨테이너는 삭제 후 새로 만들면 데이터 초기화됨
- 업로드 파일을 외부 스토리지와 링크하여 사용하거나, S3같은 별도의 저장소 필요
- 세션이나 캐시를 파일로 사용하고 있다면 ㅇmemcached나 redis와 같은 외부로 분리



## 도커 이미지와 컨테이너

이미지는 붕어빵 틀

컨테이너는 거기서 생성된 붕어빵들

### 도커 이미지

ex) 비슷하게 가상 머신을 생성할 때 사요하느 ㄴiso파일과 비슷한 개념이다.

도커에서 사용하는 이미지의 이름은 기본적으로

[저장소 이름]/[이미지 이름]:[태그]의 형태로 구성된다. 

ex) swimming/ubuntu:14.04



### 도커 컨테이너 

이미지로 컨테이너를 생성한다는 것..

붕어빵틀에서 붕어빵을 만드는 것

해당 이미지의 목적에 맞는 파일이 들어 있는 파일시스템과 격리된 시스템 자원 및 네트워크를 사용할 수 있는 독립된 공가이 생성된다. 이것이 바로 도커 컨테이너이다!!

컨테이너는 이미지를 읽기 전용으로 사용하되 이미지에 변경된 사항만 컨테이너 계층에 저장하므로 컨테이너에서 무엇을 하든 이미지는 영향을 받지 않는다. 



## 도커 컨테이너 다루기 

```bash
$ docker -v
Docker version 19.03.12, build 48a66213fe
```



-it 옵션은 컨테이너와 상호(interactive) 입출력이 가능하도록 한다.

-i 상호 입출력

-t tty 홀성화 하여 bash 쉘 사용할 수 있게 해줌

```bash
$ sudo docker run ubuntu:16.04
Unable to find image 'ubuntu:16.04' locally
16.04: Pulling from library/ubuntu
7b378fa0f908: Pull complete
4d77b1b29f2e: Pull complete
7c793be88bae: Pull complete
ecc05c8a19c0: Pull complete
Digest: sha256:0eb024b1147ab61246cfdbdf05c128550ede262790b25a8a6fd93dd3385ab1c8
Status: Downloaded newer image for ubuntu:16.04
root@80f4f2538e4f:/#

```

우분투 이미지가 로컬 도커 엔진에 존재하지 않으므로 도커 중앙 이미지 저장소인 도커 허브에서 자동으로 이미지를 내려받습니다.



- 컨테이너는 호스트 파일시스템과 독립적이여서 ls로 파일 시스템을 화인해보면 아무것도 깔리지 않은 것을 확인할 수 있다. 

```bash
root@80f4f2538e4f:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
```



컨테이너 셀에서 나갈라면 Ctrl+D 또는 exit 명령어를 키면 된다. 

(이 방법은 컨테이너를 종료하고 나오게 된다.)



그래서 `Ctrl + P, Q`를 사용하면 단순히 컨테이너 셀에서만 빠져나오기 떄문에 컨테이너 애플리케이션 개발하는 목적으로는 이 방법을 주로 사용한다.



## 도커 치트 시트

### Build

docker build -t myimage:1.0

docker image ls

docker image rm  alpine:3.4



### Share

docker pull myimage:1.0

docker tag myimage:1.0 myrepo/myimage :2.0

docker push myrepo/myimage:2.0



### Run

docker container run --name web -p 5000:80 alpline:3.9

docker container stop web

docker container kill web

docker container ls

docker container rm -f $(docker ps -aq)

docker network ls



## 도커 이미지 다운로드

```bash
$ sudo ocker pull centos:7
524b0c1e57f8: Pull complete
Digest: sha256:e9ce0b76f29f942502facd849f3e468232492b259b9d9f076f71b392293f1582
Status: Downloaded newer image for centos:7
docker.io/library/centos:7

```





```bash
$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              16.04               fab5e942c505        40 hours ago        126MB
centos              7                   b5b4d78bc90c        2 months ago        203MB
hello-world         latest              bf756fb1ae65        6 months ago        13.3kB

```

다운받은 것을 확인할 수 있다.



### 컨테이너 생성

컨테이너를 생성 할 때는 run이 아니라 create를 사용할 수도 있다.

create는 run고 달리 실행하고 나서 컨테이너 내부로 들어가지 않는다. 말 그래도 컨테이너 생성만 한다. 

```bash
$ sudo docker create -it -name mycentos centos:7
6b6f908dfa5aaad5fc336ebe09d87c32df891ac1ebdd12670c6986372bea630a

```



그러면 컨테이너를 실행해보자. 그리고 그 내부로 들어가자 

```bash
$ sudo docker start mycentos
mycentos

$ sudo docker attach mycentos
[root@6b6f908dfa5a /]#

```



run과 create의 관계는 음... 실행하고 들어가는 차이밖에 없다. 사실은 run을 더 많이 사용한다. 

그리고 Ctrl +P,Q로 나와서 컨테이너를 확인해보자



### 컨테이너 목록 확인

docker ps 명령어는 실행중인 컨테이너를 출력한다. 

```bash
$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
6b6f908dfa5a        centos:7            "/bin/bash"         4 minutes ago       Up 3 minutes                            mycentos

```



정지되어 있는 컨테이너까지 확인해라면 -a 옵션을 붙이면 된다.

```bash
$ sudo docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                       PORTS               NAMES
6b6f908dfa5a        centos:7            "/bin/bash"         5 minutes ago       Up 4 minutes                                     mycentos
80f4f2538e4f        ubuntu:16.04        "/bin/bash"         About an hour ago   Exited (127) 9 minutes ago                       admiring_meitner
a786f6d7e16a        hello-world         "/hello"            2 hours ago         Exited (0) 2 hours ago                           sleepy_kowalevski

```



### 컨테이너 삭제

```bash
$ sudo docker rm admiring_meitner
admiring_meitner

```



```bash
$ sudo docker rm mycentos
Error response from daemon: You cannot remove a running container 6b6f908dfa5aaad5fc336ebe09d87c32df891ac1ebdd12670c6986372bea630a. Stop the container before attempting removal or force remove

```

실행 중인 컨테이너는 바로 삭제되지 않는다. -f option을 주면 강제로 삭제할 수는 있따.



```bash
$ sudo docker stop mycentos
mycentos

$ sudo docker rm mycentos
mycentos

```



## 도커 네트워크 테스트

```bash
$ sudo docker run -it --name network_test ubuntu:16.04
root@ec21922ab95e:/# apt update
root@ec21922ab95e:/# apt install net-tools
root@ec21922ab95e:/# ifconfig
eth0      Link encap:Ethernet  HWaddr 02:42:ac:11:00:02
          inet addr:172.17.0.2  Bcast:172.17.255.255  Mask:255.255.0.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:4506 errors:0 dropped:0 overruns:0 frame:0
          TX packets:3679 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:17029425 (17.0 MB)  TX bytes:247377 (247.3 KB)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

```

도커는 기본적으로 컨테이너에 172.17.0.x IP를 순차적으로 할당한다. 

외부에 컨테이너의 애플리켕션을 노출하기 위해서는 eth0의 IP와 포트를 호스트의 IP와 포트에 바인딩해야 한다. 

```bash
docker run -it --name mywebserver -p 80:80 ubuntu:16.04
```



### 아파치 서버 열어서 네트워크 연결 테스트 해보기

```bash
root@f3c82abc5520:/# apt update
root@f3c82abc5520:/# apt install apache2 -y
root@f3c82abc5520:/# service apache2 start
 * Starting Apache httpd web server apache2                                     AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 172.17.0.2. Set the 'ServerName' directive globally to suppress this message
 *

```



![image-20200726160743189](C:\Users\swimm\AppData\Roaming\Typora\typora-user-images\image-20200726160743189.png)



aws 주소로 접근해보니 아파치 컨테이너랑 연결된거 확인!!! 너무 잘됩니다.



### DB + Webserver 테스트해보기

하나의 컨테이너에 디비, 웹서버 등등 모두 넣을 수는 있지만, 기능별로 컨테이너를 따로 관리하는게 독립성와 보장함과 동시에 모듈화 , 버전 관리 등에 유리하다. 즉 따로 컨테이너 만드는게 굳굳이다.



```bash
$ sudo docker run -d \
--name wordpressdb \
-e MYSQL_ROOT_PASSWORD=password \
-e MYSQL_DATABASE=wordpress \
mysql:5.7
Unable to find image 'mysql:5.7' locally
5.7: Pulling from library/mysql
6ec8c9369e08: Pull complete
177e5de89054: Pull complete
ab6ccb86eb40: Pull complete
e1ee78841235: Pull complete
09cd86ccee56: Pull complete
78bea0594a44: Pull complete
caf5f529ae89: Pull complete
4e54a8bcf566: Pull complete
50c21ba6527b: Pull complete
68e74bb27b39: Pull complete
5f13eadfe747: Pull complete
Digest: sha256:97869b42772dac5b767f4e4692434fbd5e6b86bcb8695d4feafb52b59fe9ae24
Status: Downloaded newer image for mysql:5.7

```



```bash
$ sudo docker run -d \
-e WORDPRESS_DB_PASSWORD=password \
--name wordpress \
--link wordpressdb:mysql \
-p 80 \
wordpress
Unable to find image 'wordpress:latest' locally
latest: Pulling from library/wordpress
6ec8c9369e08: Already exists
081a822af595: Pull complete
bb5bea655fca: Pull complete
1e5d9e6a44c7: Pull complete
51c80d726a75: Pull complete
41f3ef5189e5: Pull complete
c1a9c1efdc83: Pull complete
348c6ac67813: Pull complete
d16c4c4b2a5f: Pull complete
035ee560bfbc: Pull complete
4c16f7d16e86: Pull complete
560feb679e04: Pull complete
0bc8defe61af: Pull complete
fba669638605: Pull complete
e7e6887919c1: Pull complete
0a2bc4bd1e57: Pull complete
bfc2dedb5a6a: Pull complete
9d6e9899cbb7: Pull complete
627e98c5d45c: Pull complete
504e4867f2f5: Pull complete
Digest: sha256:095e933a48619e3dfb11f51b11fbe2ebdd0e2f827f1a38c556c80f635c8baf72
Status: Downloaded newer image for wordpress:latest
90c587a2eb816760749e28ac5f8ae3c4e11edaf570db7687510e72beb55336a8

```



> --link : 도커는 컨테이너가 생성될 때마다 순차적으로 증가하면서 내부에서 IP를 할당해준다.  이 변동되는 IP를 그떄그떄 적어서 컨테이너를 실행하는 것은 매우 어렵다. 그래서 --link 옵션을 이용해서 내부 IP를 알 필요없이 항상 컨테이너에 별명으로 접근하도록 설정한다. 
>
> => 도커 브릿지 네트워크를 사용하면 --link 옵션과 동일한 기능을 손쉽게 사용할 수 있다. 

그러면 두개의 컨테이너를 실행하고 돌아가고 있는지 확인해보기!

```bash
$sudo docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                   NAMES
90c587a2eb81        wordpress           "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:32768->80/tcp   wordpress
065fb966296f        mysql:5.7           "docker-entrypoint.s…"   4 minutes ago        Up 4 minutes        3306/tcp, 33060/tcp     wordpressdb

```



그렇다면 실제 동작되고 있는 곳으로 들어가보자

보니까 포트가 32768이다. 그러면 32768 포트로 들어가보자!!

![image-20200726161633717](C:\Users\swimm\AppData\Roaming\Typora\typora-user-images\image-20200726161633717.png)





## 도커 볼륨

위에서 처럼 mysql 컨테이너를 삭제하면 컨테이너 계층에 저징되어 잇던 데이터베이스 정보도 같이 삭제되게 된다!!



호스트와 볼륨을 공유할 수 있고, 볼륨 컨테이너를 활용할 수 도 있으며, 도커가 관리하는 볼륨을 생성할 수도 있습니다.



### 호스트와 볼륨 공유하기

```bash
sudo docker run -d \
> --name wordpressdb_hostvalume \
> -e MYSQL_ROOT_PASSWORD=password \
> -e MYSQL_DATABASE=wordpress \
> -v /home/wordpress_db:/var/lib/mysql \
> mysql:5.7
bb9d7b0b3906566815d45d7a6e87adc9bebafb8dbc799f93fb2351b2efb7b053

```



```bash
$ sudo docker run -d 
-e WORDPRESS_DB_PASSWORD=password 
--name wordpress_hostvolume 
--link wordpressdb_hostvolume:mysql 
-p 80 wordpress

fe3263aed6946482ff26e0adbfbac1bb937653d5815416acb6ca7d131617acd1

```



-v 옵션을 추가해서 호스트의 파일 시스템 공간과 컨테이너에서 사용하는 mysql 디렉터리를 공유한 것이다!

> 정확히 말하면 -v 옵션을 통한 호스트 볼륨 공유는 호스트 디렉터리를 컨테이너의 디렉터리에 마운트한 것이다!!

```bash
$ ls /home/wordpress_db
auto.cnf         client-key.pem  ibdata1             private_key.pem  sys
ca-key.pem       ib_buffer_pool  ibtmp1              public_key.pem   wordpress
ca.pem           ib_logfile0     mysql               server-cert.pem
client-cert.pem  ib_logfile1     performance_schema  server-key.pem

```



### 볼륨 컨테이너

컨테이너를 생성할 때 --volume-from 옵션을 설정하면 -v 또는 --volume 온셥을 적용한 컨테이너 볼륨 디렉터리를 공유할 수 있습니다.

그치만 이 방식은 직접 볼륨을 공유하는 것이 안이라, 볼륨을 공유한 것이 아니라 -v 옵션을 적용한 컨테이너를 통해 공유하는 것입니다.





### 도커 볼륨

docker volume을 사용해서 저장할 수도 있따!

```bash
$ sudo docker volume create --name myvolume
myvolume

```



도커 볼륨 확인!

```bash
$ sudo docker volume ls
DRIVER              VOLUME NAME
local               b47bf1825bca0e9fdb327025024b31e4bc4eeab964f5056ff9434f70ab4a5e8f
local               c8ff30abe66a2d16240e313e795b58023f296f0031fc2d9569be69b83f4e534d
local               c3032a1fd74b208626e816fe6f6cb62442df26d4f3ef275335526ed6166893a5
local  
```



도커 볼륨도 호스트 볼륨 공유와 마찬가지로 호스트에 저장함으로써 보존하지만, 파일이 실제로 어디에 저장되는지 사용자는 알 필요가 없다. 

> docker inspect를 이용하면 myvolume이 실제로 어디에 저장되어 있는지 알 수 있다. 

> ```bash
>$ sudo docker inspect --type volume myvolume
> [
>  {
>      "CreatedAt": "2020-07-26T08:27:10Z",
>         "Driver": "local",
>         "Labels": {},
>         "Mountpoint": "/var/lib/docker/volumes/myvolume/_data",
>         "Name": "myvolume",
>         "Options": {},
>         "Scope": "local"
>     }
>    ]
>    
> 
> ```

> 반대로 컨테이너가 어떤 도커 볼륨을 사용하고 있는지도 확인할 수 있다 
>
> ```bash
> $ sudo docker container inspect {$container_name}
> ```









사용법!!

```bash
$ docker run -it --name myvolume1 \
-v myvolume:/root/ \
ubuntu:16.04


```



## 도커 네트워크

도커는 컨테이너에 내부 IP를 순차적으로 할당하며, IP는 컨테이너가 재시작할 때마다 변경될 수 있따. 이 내부 IP는 외부와 연결되어야 한다. 

도커는 각 컨테이너에 외부와의 네트워크를 제공하기 위해 컨테이너마다 가상 네트워크 인터페이스를 호스트에 생성하며 이 인터페이스의 이름은 veht로 시작한다. 

```bash
$ ifconfig
docker0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
        inet6 fe80::42:43ff:fe73:e5fc  prefixlen 64  scopeid 0x20<link>
        ether 02:42:43:73:e5:fc  txqueuelen 0  (Ethernet)
        RX packets 12491  bytes 1225777 (1.2 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 15758  bytes 57780085 (57.7 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 9001
        inet 172.31.41.58  netmask 255.255.240.0  broadcast 172.31.47.255
        inet6 fe80::8e1:3cff:feee:280a  prefixlen 64  scopeid 0x20<link>
        ether 0a:e1:3c:ee:28:0a  txqueuelen 1000  (Ethernet)
        RX packets 476331  bytes 687161583 (687.1 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 66348  bytes 8089257 (8.0 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 858  bytes 89092 (89.0 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 858  bytes 89092 (89.0 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

veth0bc8bb0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::c002:76ff:fef9:8d7  prefixlen 64  scopeid 0x20<link>
        ether c2:02:76:f9:08:d7  txqueuelen 0  (Ethernet)
        RX packets 11  bytes 786 (786.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 27  bytes 2158 (2.1 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

vethec1d802: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::9411:e7ff:fe2d:35c  prefixlen 64  scopeid 0x20<link>
        ether 96:11:e7:2d:03:5c  txqueuelen 0  (Ethernet)
        RX packets 11  bytes 862 (862.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 25  bytes 1862 (1.8 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

```



도커 브릿지에 대한 개념만 알고 있으면 된다. 도커 브릿지는 말그대로 docker0()가 다른 컨테이너에 있는 네트워크 인터페이스에 연결해주는 것이다!



macVLAN 도 있따. 이건 브릿지를 이용하는 것은 아니다

https://m.blog.naver.com/PostView.nhn?blogId=alice_k106&logNo=220984112963&proxyReferer=https:%2F%2Fwww.google.com%2F





## Docker 허브 가지고 놀아보기

매우 ez



## Dockerfile만들기 

흠 2-3시간? 



## 도커 데몬

안할듯...



## 도커 컴포즈

금방 할듯//





도커 허브건드려 보고

실제로 mysql배포하고

-> 백엔드에서 DB 정보 뺴오기

-> 그리고 그걸 도커 볼륨으로 저장하기

-> 도커 볼륨이용하는게 좋긴 한데, 도커 볼륨 데이터도 이미지 처럼 간단히 서로 주고 받을 수 있을까? 

-> 도커에서 mysql 배포하기? 설정파일을 배포하는건가? 테이블 등 정보가 없다면 의미는 없을 거 같은데 msql 배포는 무슨 의미를 가질까?

백엔드, 프런트 배포해보기

-> 이제 백엔드, 프런트를 배포하려고 할떄, database랑 연결하는건 --link 옵션으로 간단히 할 수 있을듯, 그래도 실제 소스코드에서 어떻게 url을 명시해줘야 하는지도 음 확인해봐야 할듯..! 



nodejs 배포는 pm2로도 많이 한다고 함.

express를 pm2로 배포할 떄, nginx랑 도커까지 쓴다면... 음

대략 난감해지는 부분이듯



## 배포
nodjs 프로젝트 pm2, 도커 이용 배포	

[https://medium.com/extales/node-js-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%EB%A5%BC-docker%EB%A1%9C-%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0-3-ce7cf71ce874](https://medium.com/extales/node-js-프로젝트를-docker로-배포하기-3-ce7cf71ce874)



pm2를 이용한 nodejs 배포

https://engineering.linecorp.com/ko/blog/pm2-nodejs/