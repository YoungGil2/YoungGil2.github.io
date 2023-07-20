---
layout: post
title: react, node.js로 만드는 채팅앱
date: 2023-07-20 12:52:00 +0900
description: >
  typescript, react, node.js, socket.io, material-UI
hide_description: true
lastmod: 2023-07-20 12:52:00 +0900
sitemap:
  changefreq: weekly
---

typescript, react, node.js, socket.io, material-UI
{:.note title="사용"}

0. 
{:toc}

## 환경세팅

패키지 관리 툴로는 yarn을 사용하였습니다. 개인적으로 yarn이 편해서 사용하였습니다. 이부분은 따로 설명없이 넘어가겠습니다.<br/>
먼저 react 프로젝트 먼저 생성합니다. 그리고 하위에 server라는 폴더를 생성

~~~markdown
npx create-react-app "이름" -template typescript
~~~

socket.io 설치

~~~markdown
yarn add socket.io
~~~

## 서버생성

앞서 만들었던 server 폴더 안에 index.js 라는 파일을 만듭니다.<br/>
express로 서버를 구성할 것 입니다. 먼저 라이브러리를 설치해 줍니다.

~~~markdown
yarn add express 
~~~

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
const port = process.env.PORT || 3005;

server.listen(port, ()=>{
  console.log(port + "포트에서 수신중");
});
~~~

~~~markdown
node server/index.js
~~~

정상적으로 작성하셨다면 "3005포트에서 수신중" 이라는 콘솔 메시지를 확인할 수 있을 것입니다.

## 클라이언트 생성

클라이언트를 작성하고 서버와 연결까지 해보겠습니다. 우선 socket.io-client를 설치해 줍니다.

~~~js
// file: "/src/app.tsx"
import React from 'react';
import logo from './logo.svg';
import './App.css';
import SocketClient from 'socket.io-client';

const SERVER = "localhost:3005";

function App() {
  const socket = SocketClient(SERVER);
  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>
          Edit <code>src/App.tsx</code> and save to reload.
        </p>
        <a
          className="App-link"
          href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer"
        >
          Learn React
        </a>
      </header>
    </div>
  );
}

export default App;
~~~

이제 socket 이라는 상수를 이용하여 백엔드에서 발생하는 이벤트를 받을수 있습니다.<br/>
예시로 클라이언트가 연결되면 콘솔 메시지를 받아봅시다.

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
const port = process.env.PORT || 3005;

io.on("connection", (socket) => {
  console.log('새로운 클라이언트가 입장하였습니다.');
  socket.emit('connection');
});

server.listen(port, ()=>{
  console.log(port + "포트에서 수신중");
});
~~~

~~~js
// file: "/src/app.tsx"
import React from 'react';
import logo from './logo.svg';
import './App.css';
import SocketClient from 'socket.io-client';

const SERVER = "localhost:3005";

function App() {
  const socket = SocketClient(SERVER);
  socket.on("connection", () => {
    console.log("백엔드와 연결되었습니다.")
  });
  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>
          Edit <code>src/App.tsx</code> and save to reload.
        </p>
        <a
          className="App-link"
          href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer"
        >
          Learn React
        </a>
      </header>
    </div>
  );
}

export default App;
~~~

브라우저 콘솔에는 "백엔드와 연결되었습니다." 메시자가 출력 되어야 합니다.
간단한 코드 몇줄로 nodejs + express로 만든 서버와 프론트(react-app)을 연결해 보았습니다.