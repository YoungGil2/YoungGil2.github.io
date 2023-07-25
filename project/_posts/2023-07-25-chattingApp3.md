---
layout: post
title: react, node.js, socket.io로 만드는 채팅앱 - 3
date: 2023-07-25 10:51:00 +0900
description: >
  typescript, react, node.js, socket.io, material-UI
hide_description: true
lastmod: 2023-07-25 10:51:00 +0900
sitemap:
  changefreq: weekly
---

대화방에 유저간 대화 메시지를 보내는 부분일 만들어 보겠습니다. 

0. 
{:toc}

## 대화방에 메시지 보내기

대화방 접속 유저간에 실시간 대화를 할수 있게 만들어 보겠습니다. <br/>
1, 2번 포스팅에 있는 내용을 전부 합친 완성 코드입니다.

### 서버

~~~js
// file: "/server/index.js"
const express = require("express");
const path = require("path");
const app = express();
const server = require("http").Server(app);
const io = require("socket.io")(server, {
  cors: {
    origin: "*",
    methods: ["GET", "POST"],
  },
});
const { addUser, removeUser } = require("./user");
const port = process.env.PORT || 3005;

app.use(express.static(path.join(__dirname,"../build")));

// 라우트 설정
app.get('*', (req, res) => {
  res.sendFile(path.join(__dirname,'../build/index.html'));
});

io.on("connection", function (socket) {

  socket.on("join", ({ name }) => {
    const { user, users } = addUser({ id: socket.id, name: name });
    console.log(user);
    socket.broadcast.emit("message", {
      name: "admin",
      message: `${user.name}님이 접속하였습니다.`
    });
    
    io.emit("usersInfo", users);
  
  });

  socket.on("sendMessage", (name, message, callback) => {
    console.log(message);
    io.emit("message", { name, message });

    callback();
  });

  socket.on("disconnect", () => {
    const { user, users } = removeUser({ id: socket.id});

    io.emit("usersInfo", users);
    console.log(user);
    if(user){
      socket.broadcast.emit("message", {
        name: "admin",
        message: `${user.name}님이 퇴장하셨습니다.`
      })
    }
  });
});

server.listen(port, () => {
  console.log(`Listening on port ${port}`);
});
~~~

### 클라이언트
~~~js
// file: "/src/pages/room.tsx"
import queryString from "query-string";
import {useEffect, useState, useRef} from "react";
import socketClient from "socket.io-client";
import Paper from "@material-ui/core/Paper";
import Grid from "@material-ui/core/Grid";
import List from "../component/List";
import Message from "../component/Message";
import Input from "../component/Input";

const Server = "/";
let socket;

function Room({location}) {
  const [name, setName] = useState(""); 
  const [message, setMessage] = useState("");
  const [messages, setMessages] = useState([]);
  const [users, setUsers] = useState([]);
  const messagesRef = useRef<HTMLDivElement>(null);
 
  useEffect(()=>{
    socket = socketClient(Server);
    const { name } = queryString.parse(location.search);
    setName(name);

    socket.emit("join", { name });

    socket.on("usersInfo",(userInfoArray)=> {
      setUsers(userInfoArray);
    });

    return ()=>{
      socket.disconnect();
    }

  // eslint-disable-next-line react-hooks/exhaustive-deps
  },[Server, location.search]);

  useEffect(()=>{
    socket.on("message",(message)=>{
      setMessages(messages => [ ...messages, message ]);
    });
  // eslint-disable-next-line react-hooks/exhaustive-deps
  },[]);

  useEffect(() => {
    messagesRef.current!.scrollTop = messagesRef.current!.scrollHeight;
  }, [messages]);

  const sendMessage = (e)=> {
    e.preventDefault();
    if(message){
      socket.emit("sendMessage",name, message,()=>{setMessage("")});
    }
  };
  return (
    <Grid container justifyContent="center" style={{ padding: "1em" }}>
      <Grid item xs={12} sm={9} md={7}>
        <Paper>
          <List ListData={users} />
        </Paper>
      </Grid>

      <Grid item xs={12} sm={9} md={7} style={{ marginTop: "10px"}}>
        <Paper>
          <div style={{ height: "400px", overflowY: "auto" }} ref={messagesRef}>
            {messages.map((value, key) => {
              return (
                <Message
                  key={key}
                  name={value.name}
                  text={value.message}
                  user={name}
                />
              );
            })}
          </div>
          <Input
            sendMessage={sendMessage}
            message={message}
            setMessage={setMessage}
          />
        </Paper>
      </Grid>
    </Grid>
  );
}
export default Room;
~~~

## 정리

* `input`에 메시지를 작성 후 enter나 전송 버튼을 누르면 서버 측 `sendMessage`쪽으로 메시지를 보냄, `input` 초기화
* 서버 측 `sendMessage`에서 클라이언트 측 `message`로 메시지를 보냄
* 클라이언트 측 `messages` state에 메시지가 배열에 추가됨

아주 간단한 코드로 실시간 채팅방을 만들어 보았습니다. <br/>
2년 전에 만들었을때는 만들고 뿌뜻했었는데... 지금 코드가 보면 에휴네요. 나중에 시간이 된다면 채팅 내용을 db에 기록하고 부가적으로 디벨롭해보겠습니다.<br/>

오래전 코드라서 기억을 살려서 기록을 해보았는데... 잘 생각이 안나네요ㅠ<br/>
그때는 heroku에 배포까지 해서 배포까지 포스팅하고 싶었으나, mfa(otp)키를 분실 이슈로 인해 로그인이 안되네요

