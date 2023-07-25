---
layout: post
title: react, node.js, socket.io로 만드는 채팅앱 - 2
date: 2023-07-24 12:52:00 +0900
description: >
  typescript, react, node.js, socket.io, material-UI
hide_description: true
lastmod: 2023-07-24 12:52:00 +0900
sitemap:
  changefreq: weekly
---

저번 포스팅에는 서버와 클라이언트 측을 연결하는 부분을 구현해 보았는데, 이어서 대화방 입장 및 접속, 퇴장 시 메시지 노출까지 만들어보겠습니다.

0. 
{:toc}

## 방 입장

대화방 입장과 대화방에 접속중인 유저들에게 신규, 퇴장 유저가 있음을 알리는 메시지 전송까지 만들어 보겠습니다.

### 서버
~~~js
// file: "/server/index.js"
io.on('connection', function (socket) {

  socket.on('join', ({ name }) => {
    const { user, users } = addUser({ id: socket.id, name: name });

    io.emit('usersInfo', users);
    socket.broadcast.emit('message', { // broadcase 메소드는 이벤트를 보낸 유저를 제외한 모든 유저에게 전송
      name: 'admin',
      message: `${user.name}님이 접속하였습니다.`
    });
  });

  socket.on('disconnect', () => {
    const { user, users } = removeUser({ id: socket.id});

    io.emit('usersInfo', users);
    if(user){
      socket.broadcast.emit('message', {
        name: 'admin',
        message: `${user.name}님이 퇴장하셨습니다.`
      })
    }
  });

}
~~~

`addUser`와 `removeUser`부분 설명입니다. 저는 유저 리스트를 노출하고싶어 user.js를 만들었습니다.

~~~js
// file: "/server/user.js"
const users = [];

const addUser = ({id, name}) => {
  const user = {id, name};
  users.push(user);

  return { user, users };
};

const removeUser = ({ id }) => {
  const removeUserIndex = users.findIndex(element => element.id === id );
  let user;
  if(removeUserIndex > -1){ 
      user = users.splice(removeUserIndex, 1)[0];
  }
  
  return { user ,users };
};

module.exports = { addUser, removeUser };
~~~

### 클라이언트
~~~js
// file: "/src/pages/room.tsx"
  const [messages, setMessages] = useState([]);
  const [users, setUsers] = useState([]);

  useEffect(()=>{
    socket.emit('join', { '유저1' });

    socket.on('usersInfo',(userInfoArray)=> {
      setUsers(userInfoArray);
    });

    socket.on('message',(message)=>{
      setMessages(messages => [ ...messages, message ]);
    });

    return ()=>{
      socket.disconnect();
    }
  },[]);
~~~

## 정리

* room 페이지 입장시 서버쪽 `join`에 요청
* 유저 배열에 해당 유저 추가 및 유저 배열 클라이언트 측 `usersInfo`으로 보냄
* 방금 입장한 유저를 제외한 모든 유저에게 신규 유저 클라이언트 측 `message`로 보냄
* 퇴장시 퇴장한 유저를 제외한 모든 유저에게 퇴장 유저 클라이언트 측 `message`로 보냄

다음에는 대화방에서 메시지를 보내는 부분까지 구현해 보도록 하겠습니다.