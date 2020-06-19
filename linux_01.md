# 리눅스 쉘 & 커널 프로그래밍

쥐앤유와 리눅스

쥐앤유

- 80년대 초반 리차드 스톨만에 의하여 시작



특징

1. 대부분의 플랫폼에서 사용가능
2. 강력한 네트워크 지원
3. 다양한 파일 시스템 지원
4. 낮은 하드웨어 사양에서도 사용가능



구조

1. 운영체제
2. 커널
3. 시스템 호출
4. 쉘



모놀리식 커널- 단일형 커널이라고도 한다. 입출력가능, 네트워크 기능, 장치 지원등 운영 체제의 일반적인 기능을 커널과 동일한 메모리 공간에 적재, 실행하는 기법을 말한다.



리눅스 배포한

- 데비안, 우분투, 레드햇, 페도라, 센트오에스, 수세, 아치 리눅스



디스크 파티션

리눅스를 설치할 디스크에 파티션을 나눠서 실행한다. 특별한 이유가 없는 한 다음 5개 정도의 파티션으로 나누어 설치하게 된다. 

- 부트 파티션 : ext3, ext4, xfs 파일 시스템으로 포맷하고 /boot 디렉토리로 사용

  - 부트 파티션은 커널과 초기 RAM 디스크를 포함, GRUB에 관련된 파일을 저장하기 떄문에 100MB 정도면 충분

- 루트 파티션 : ext3, ext4, xfs 파일 시스템으로 포맷하고 / 디렉토리로 사용

  - 인스톨 예정의 패키지와 애플리케이션 소트프웨어 인스톨할 수 있는 정도의 사이즈가 필요

- swap 파티션 : swap 영역으로 포맷 

  - 메모기가 2GB 이하: 물리 메모리의 2배
  - 메모리가 2GB 이상: 물리 메모리 + 2GB 

- kdump : ext3, ext4, xfs 파일 시스템으로 포맷하고 마운트하지 않음

- 데이터용 파티션 : 애플리케이션으로 데이터영역으로서 사용

  

### 리눅스 커맨드

사용자 정보, 터미널 정보를 확인해보자.

![img](/home/swim/Pictures/Screenshot from 2020-06-19 11-24-10.png)





![Screenshot from 2020-06-19 11-25-08](/home/swim/Pictures/Screenshot from 2020-06-19 11-25-08.png)



커널 정보, 리눅스 정보를 확인해보자.

![Screenshot from 2020-06-19 11-25-08](/home/swim/Pictures/Screenshot from 2020-06-19 11-25-52.png)

uname은 마이크로 네임이다!! 이렇게 이해하는 것이 좋다. 그래서 이걸 치면 커널, 리눅스 정보를 확인할 수 있다.

우리는 개발자니까 롱 옵션, 숏 옵션 들을 구분하자!! 짝대기 두개 대신에 롱 옵션, 숏 옵션으로 말하자



시스템에 대한 상황을 한번에 볼 수 있는 명령어!!top

![Screenshot from 2020-06-19 13-32-32](/home/swim/Pictures/Screenshot from 2020-06-19 13-32-32.png)

load average: 가 중요하다. 이걸보면 서버가 얼마나 일하고 있는지 알 수 있다.

KiB

MiB가 붙으면 키비, 미비 바이트 이렇다. 이런건 1024 단위로 한 것을 말한다. 



vmstat 1

I/O가 놓다... 무슨 말이지? cpu bound or io bound냐를 설득해야 한다. 1초마다 계속 보는 것이다. 

r, b를 보면 된다. 런이랑 블락인듯...

buffer (to write), cache(to read)를 사용하기 위해 다른 것이다. 

```
vmstat 1
```



![Screenshot from 2020-06-19 13-35-28](/home/swim/Pictures/Screenshot from 2020-06-19 13-35-28.png)





![Screenshot from 2020-06-19 11-26-24](/home/swim/Pictures/Screenshot from 2020-06-19 11-26-24.png)



os정보 확인

![Screenshot from 2020-06-19 11-26-45](/home/swim/Pictures/Screenshot from 2020-06-19 11-26-45.png)



disk

![Screenshot from 2020-06-19 11-28-23](/home/swim/Pictures/Screenshot from 2020-06-19 11-28-23.png)





사용 memory를 확인해보자.![Screenshot from 2020-06-19 11-30-08](/home/swim/Pictures/Screenshot from 2020-06-19 11-30-08.png)







### 런레벨

0: 시스템 정지

1: 싱글 유저 모드로 기동(시스템 복구시 사용)

2: 멀티 유저(네트워크를 사용하지 않는 텍스트 멀티 유저 모드)

3: 멀티 유저(일반적인 쉘 기반의 텍스트 멀티 유저 모드)

4: 사용하지 않음

5: GUI 멀티 유저 모드

6: 시스템 재가동



일반적으로 서버를 운용할 때 3, 5를 지정한다.

보통 서버에서는 gui가 필요가 없다면 3을 지정

systemd 사용시 숫자 기반의 런레벨이 아니라 각 런레벨에 대한 설정 세트를 통해서 런레벨을 변경한다.

일반적으로 시스템을 종료시키거나 재부팅을 위해서 0과 6도 종종 사용한다.



![Screenshot from 2020-06-19 11-36-09](/home/swim/Pictures/Screenshot from 2020-06-19 11-36-09.png)



따라서 부팅 시 런레벨은 다음 명령으로 설정하고 현재 설정 상태를 알아볼 수 있다.

![Screenshot from 2020-06-19 11-38-50](/home/swim/Pictures/Screenshot from 2020-06-19 11-38-50.png)



시스템 재시작

```bah
$ sudo reboot
$ sudo init 6
$ sudo shutdown -r now

```





### 원격 접속 

ssh server

```bash
sudo systemctl is-enabled sshd.service

sudo systemctl enable sshd.service

```



ssh - authorized keys

RSA algorithm으로 한다. 

client에서 ssh-keygen실행한다. 

![Screenshot from 2020-06-19 11-52-39](/home/swim/Pictures/Screenshot from 2020-06-19 11-52-39.png)







![Screenshot from 2020-06-19 11-53-44](/home/swim/Pictures/Screenshot from 2020-06-19 11-53-44.png)





![Screenshot from 2020-06-19 11-54-14](/home/swim/Pictures/Screenshot from 2020-06-19 11-54-14.png)



이걸 복사해서 서버에게 넣어준다.



cat >> .ssh/authorized_keys

이제부터는 인증없이 접근할 수 있다.



배운거는 써먹자!!!



### Shell

쉘이란 명령어 해석기이다.(command interpreter)

shell select

시스템 관리자가 사용자 계정 파일에 설정한다. 

/etc/passwd



출력 리디렉션 >, >>

cat > text.txt

cat >> text.txt



입력 리디렉션 <, << 

ls -l < /usr/bin

ls -l <<

> /usr/bin
>
> ^D



<< 입력을 버터로 받는다.

파이프 | 

find / | grep ttd



홈 디렉토리 

chmod -R permission {filename}



diff 파일간의 모든 차이점과 비슷한 점을 보여줌

find

파일 묶기(타르)

​	tar cvf source.tar source:



gzip source.tar:



gunzip source.tar.gz

## 링크 만들기 hard, soft

하드링크

- 동일한 파일 시스템 내에서 링크

- 여러 개의 레이블 생성이 가능하나, 물리적 파일은 동일함

- 하드 링크가 추가될 때마다 링크 계수 항목이 증가



소프트 링크(심볼릭 링크)

- 다른 파일 시스템들간의 링크
- 링크 계수 항목이 증가하는 대신에 permission |--- 식으로 기록됨
- ls -F로 참조할 때 파일 이름 뒤에 @가 표시됨



ln -s /usr/include/stdio.h

ln -f /usr/include/stdlib.h 





## 시스템 로그 관리

시스템 디버깅 또는 시스템의 사용을 추적하는데 유용

시스템 로그 또는 콘솔 화면 또는 /var/log/

- 터미널에서 tail -f /var/log/message 실행

- 터미널에서 tail, head 명령 이용

- 터미널에서 grep 이용

  

## 작업 스케쥴

crontab

cron에 의하여 일련의 작업이 주기적으로 실행

cron은 단일 프로세스로 시스템 시작부터 종료까지 동작하는데, /var/spool/cron/crontabs/ 디렉토리에 복사 등록된 crontab 파일들을 수행함



at

지정된 시간에 작업이 실행되도록 함



crontab crontabName

- cron 시스템에 의하여 사용되는 crontabName의 crontab파일을 등록

- crontabName 없으면 표준 입려거으로 입력하고 ctrl + D



crontab -l -e -r userName





사용자가 crontab at을 사용/비허용



