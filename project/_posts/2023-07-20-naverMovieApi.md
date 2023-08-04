---
layout: post
title: 네이버 영화 API를 사용해보자
date: 2023-07-20 12:52:00 +0900
description: >
  Naver Open Api를 이용하여 영화 검색해보자!
lastmod: 2023-07-20 12:52:00 +0900
sitemap:
  changefreq: weekly
---

0. 
{:toc}

## 네이버 영화 API

[네이버 OPEN API 영화](https://developers.naver.com/docs/serviceapi/search/movie/movie.md)
{:.note title="링크"}

네이버에는 여러가지 OPEN API가 있습니다. 그 중에 영화 API를 사용해 보겠습니다.

react, typescript, node.js
{:.note title="환경"}

나는 시작하기전에 이런 생각이 들었다. 그냥 js에서 API 호출하면 되지 않는가? 

NAVER 영화 API는 서버프로그래밍(python, jsp, php, node.js 등)을 통해서만 호출하도록 되어 있습니다. 이유는 다른 사람의 클라이언트 id, secret을 도용해서 api를 호출하는 것을 막기 위해서입니다.라고 하네요 ㅎㅎ node.js 써야겠지?

## node.js(express)

server 폴더를 만들고 안에 index.js 라는 파일을 생성합니다.<br/>
express로 서버를 구성할 것 입니다. 먼저 라이브러리를 설치해 줍니다.

~~~markdown
yarn add express 
~~~

~~~js
// file: "/server/index.js"
const express = require('express');
const app = express();
const path = require("path");
const port = process.env.PORT || 5000;
const axios = require('axios');

const client_id = process.env.CLIENT_ID;
const client_secret = process.env.CLIENT_SECRET;

app.use(express.static(path.join(__dirname,"../build")));
app.get('/', (req, res) => {
  res.sendFile(path.join(__dirname,'../build/index.html'));
});

app.get('/api', async (req, res) => {
  const page = req.query.page;
  const query = req.query.query;
  try {
    const resp = await axios.get('https://openapi.naver.com/v1/search/movie.json', {
      params: {
        query: query,
        display: 12,
        start: page
      },
      headers: {
        'X-Naver-Client-Id': client_id,
        'X-Naver-Client-Secret': client_secret
      }
    });
    if(resp && resp.status === 200){
      const { data } = resp;
      console.log(query);
      return res.json(data);
    }
  } catch (error) {
    return res.json(error);
  }
})

app.listen(port, ()=> {
  console.log(`서버: ${port}`)
});
~~~

이렇게 네이버 API와 통신할 서버를 만들었습니다. `/api`에 get 요청을 할 프론트 부분를 만들겠습니다.

## react

~~~js
// file: "/src/app.tsx"
import React, { useState, useEffect, useCallback } from 'react';
import MovieCard from './component/MovieCard'
import axios from 'axios';
import './assets/scss/movie/Movie.scss';

function App() {
  const [search, setSearch] = useState('');
  const [movies, setMovies] = useState([]);
  const [startCount, setStartCount] = useState(1);

  useEffect(()=>{
    MovieSearch(startCount);
  // eslint-disable-next-line react-hooks/exhaustive-deps
  },[startCount])

  const MovieSearch = useCallback( async (page)=> {
    console.log(page);
    try {
      const resp = await axios.get('/api',
      {
        params: {
          query: search,
          page: page
        }  
      });
      if(resp && resp.status === 200){
        const { data } = resp;
        console.log(data.items);
        if(data.items){
          setMovies(data.items);
        }
      }    
    } catch (error) {
      console.log(error);
    }
  // eslint-disable-next-line react-hooks/exhaustive-deps
  },[startCount, search])
  
  return (
    <div>
      <div className="movie__search-container">
        <input className="movie__search" placeholder="입력해주세요." type='text' onChange={(e) => setSearch(e.target.value)} onKeyPress={(e) => (e.key === "Enter" ? (MovieSearch(1),setStartCount(1)) : null)}/>
        <button className="button default" onClick={() => {MovieSearch(1); setStartCount(1);}} >검색</button>
      </div>
     
      <div className="movie__container">
        {movies.map((value, key)=> {
          return (
            <MovieCard movies={value} key={key} />
          );
        })}
      </div>
      {movies.length === 0 &&
        <div className="movie__noData">검색된 내용이 없습니다.</div>
      }
      <div className="movie__paging">
        {startCount > 11 && <button className="button default" onClick={()=>{ setStartCount((prevState) => (prevState - 12)) }}>이전</button>}
        {movies.length > 11 && <button className="button default" onClick={()=> { setStartCount((prevState) => (prevState + 12)) }} >다음</button>}
      </div>
      
    </div>
  );
}

export default App;
~~~

axios를 사용하였습니다. 이제 input에 검색할 영화 이름을 넣고 검색을 누르면 영화 리스트를 가져올 수 있습니다.
