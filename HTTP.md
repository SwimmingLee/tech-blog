# HTTP

인터넷은 HTTP로 통신된다. stateless통신이라서 데이터를 저장하고 있지 않는다.

그래서 쿠키, 헤더에jwt를 추가하여 데이터를 전송하기 등이 필요



### TCP 레벨에서 속도를 더 최적화할 수 있는 요소는?

- TIME_WAITd 설정! 지터로 테스팅할 때는 time_WAIT 지연으로 포트가 고갈나서 제대로 된 성능 테스트가 안될 수도 있다. 이점을 유의해야 함!
- 네이글 알고리즘을 적용할까? 파일을 전송할 때는 오히려 네이글 알고리즘이 더 좋지 않다. 버퍼에 그냥그냥 쌓이기 때문에
- TCP의 piggyback acknowledgment을 위한 전송 지연 알고리즘! 이것도 어떤 데이터를 보낼 것인가에 따라 다르다. TCP는 Ack와 함께 데이터를 보내기 위해 전송 지연하면서 버퍼에 데이터를 모아서 보낸다. 그런데 파일 전송같은 경우에는 굳이 전송지연하지 않아도 버퍼에 데이터가 쌓이기 때문에 유리하다.
- TCP의 느린 시작(slow start). 혼잡 제어를 위한 윈도우 사이즈 변경 알고리즘! 굳이 변경할 일은 없을듯(slow start)
- 크기가 작은 HTTP 트랜잭션은 50% 이상의 시간을 TCP를 구성하는 데 쓴다. 이러한 TCP 구성으로 인한 지연을 제거하기 위해 HTTP가 이미 존재 하는 커넥션을 어떻게 활용하지 고민해봐야 한다. 



### HTTP 커넥션 연결

HTTP는 클라이언트와 서버 사이에 프락시 서버, 캐시 서버 등과 같은 중개 서버가 놓이는 것을 허락한다. 

더 빨리 보내야 한다. 위에서 말했다 시피 TCP의 느린 시작과 핸드 쉐이킹 덕분에 HTTP 커녁션을 재활용하는 것이 중요하다. 

1. 병렬 커넥션
2. 지속 커넥션
3. 병렬 + 지속 커넥션



### 병렬 커넥션

- 매번 TCP 느린 시작로 순차적으로 연결하려고 하면 너무 느리다. 그리고 유저는 화면이 동시에 나오는 것을 좀 더 선호한다. 병렬 커넥션 방식이 좀 더 빠른 것처럼 느껴진다. 병렬 커넥션은 이런 점을 해소해준다. 동시에 다운 받을 수 있으니 재빨리 데이터를 가져올 수 있다. 그렇다면 마냥 병렬 커넥션이 다 좋을 것은 아니다. 대역폭이 포화상태에 가까운데 커녁션을 늘리는 것은 의미가 없다. 커넥션이 늘어날 떄마다 감당해야 할 리소스가 많아진다. 그러므로 커넥션은 대략 4개 정도를 주로 사용한다.

### 지속 커녁션

- 지속 커넥션을 이용한 TCP의 느린 시작으로 인한 딜레이를 없앨 수 있다. 고로 무지 빠르다. 짱짱짱 그런데 문제는 얼마나 지속해야 하는지 알아야 한다. 생각보다 얼마나 커넥션을 유지해야 하는가는 어렵다. 바로 밑에서 설명하고자 한다. 

지속 커넥션도 타입이 있다. 

1. HTTP/1.0 + Keep Alive
2. HTTP/1.1 지속 커넥션

## Keep Alive

Keep Alive란 연결된 소켓에 인/아웃의 접근이 마지막으로 종료된 시점으로부터 정의된 시간까지 접근이 없더라도 대기하는 구조입니다. 즉 정의된 시간내에 접근이 이루어진다면 계속 연결된 상태를 유지할 수 있다는 것이죠.



HTTP는 connectionless 방식이기 때문에 매번 소켓을 열고 닫아야 해서 비효율적이다.

keep alive는 time out 내에 클라이언트에서 재요청을 보내면 소켓을 새로 여는 것이 아니라 기존에 사용하던 소켓을 그대로 이용하는 방식이다.

HTTP/1.0 keep-alive 커넥션을 구현한 클라이언트는 커넥셔능ㄹ 우지하기 위해서 요청에 Connection:keep-alive헤더를 포함시킨다. 이 춍ㅇ어르 받은 서버는 그 다음 요청에도 이 커넥션을 통해 받고자 한다면, 응답 메시지에 같은 헤더를 포함시켜 응답한다. 

프락시를 쓰게되면 문제가 생긴다. 프락시와 게이트웨이는 메시지를 전달하거나 캐시에 넣기 전에 Connection 헤더에 명시된 모든 헤더필드와 Connection 헤더를 제거해야한다. 그렇지만 이런 규약을 지키지 않는 프록시가 많다 (Connection 헤더는 hop by hop이여야 만한다. ) 즉, 오직 한개의 전송 링크에만 적용되며 다음 서버로 전달되어서는 안된다. 



문제는!! 멍청한 프록시가 많다는 것이다. dumb proxy

Keep alive는 Connection 헤더를 지원하지 않는 프락시와는 상호작용하지 않는다. 

그래서 proxy-Connection도 쓰지만 그래도 안된다.

### 병렬 + 지속 커넥션

정답은 하이브리드이이다. 병렬 커넥션은 동시에 다운로드할 수 있다는 장점 + 지속 커넥션은 TCP의 느린시작과 핸드쉐이킹이 잡아먹는 오버헤드를 줄일 수 있다는 장점이 버무려진다. 

### 파이프라인 커넥션

HTTP/1.1에서는 지속 커넥션을 통해서 파이프라이닝할 수 있다. 

HTTP 메시지는 순번이 없기 떄문에 HTTP응답근 요청 순서와 같게 와야 한다. POST요청같이 반복해서 보낼 경우 문제가 생기는 요청은 파이프라인으로 보내면 안된다. 에러가 발생하면 파이프라인을 통한 요청 중에 어떤 것들이 서버에서 처리되었는지 클라이언트가 알 방법이 없다. 



### 커넥션 끊기 

우아한 커넥션 끊기를 해야하는 이유는??

connection reset by peer 에러를 받으면 버퍼에 있는 것이 모두 사라진다.



## HTTP 웹 캐시


