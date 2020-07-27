## Socket.io

소켓 io는 실시간으로 상호작용하는 웹 서비스를 만드는 기술인 웹소켓을 쉽게 사용할 수 있는 모듈이다. 

```bash
npm install --save socket.io
```



```javascript
const app = require('express')();
const http = require('http').Server(app);
const io = require('socket.io')(http);
const socket = require(`${__dirname}/middleware/socket`)

socket.init(io);
app.use(socket.middleware);
```



```javascript
let _io = null;
let _socket = null;

const middleware = async (req, res, next) => {
    req._io = _io;
    req._socket = _socket;
    next();
}
var init = (io) => {
	cosole.log('socket init');
    
    _io = io;
    io.socket.on('connection', (socket) => {
        _socket = socket;
        
        socket.on('joinChannel', (msg) => {
            //
            const channel = msg.channel;
            socket.join(channel);
            io.to(channel).emit('receive', {chat: 'hello'});
            
        });
        
       	socket.on('send', (msg) => {
            
        });
        
        socket.on('leaveChannel', (msg) => {
            //
            const channel = msg.channel;
            socket.leave(channel);
            io.to(channel).emit('receive', {chat: 'bye'})
          
        });
    })
}

const sendMsg = (msg) => {
    let channel = 'channel_name';
    
    let data = {
        user: 'swim',
        msg: msg,
        date: Date.now(),
    };
    try{
        _io.to(channel).emit('recevie', {data: data});
    } catch(e) {
		console.log(e);
    }
}

module.exports = {
    init:init,
    middleware: middleware,
    sendMsg: sendMsg
};
```



정말 간단하다!!

emit('event')로 이벤트를 전달하면 on('event')로 받는다.

그리고 해당 룸에다가 채팅을 하려고 할 떄는 

_io.to(channel).emit('sd', {});

이런 식으로 작성하면 된다. 

참고자료:



https://socket.io/get-started/chat



```javascript
import io from "socket.io-client"
let socket = null;

export const userCommentSocket = () => {
    const {driverUrlBase, setMsg} = useContext(CommonContext);
    
    const channel = "channel_name";
    
useEffect(() => {
    socket = io(driverUrlBase);

    socket.emit("joinChannel", channel);
    socket.on("receive", function (data) {
      console.log("useCommentSocket -> data", data);
      setMsg(data && data.chat && data.chat.msg ? data.chat.msg : {});
    });

    return () => {
      socket.emit("leaveChannel", channel);
    };
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, []);
}
```

