---
layout: post
title: "브라우저 렌더링, 리액트 렌더링 프로세스"
date: 2025-07-01 19:27:00 +0900
description: >
  렌더링 프로세스
hide_description: true
lastmod: 2025-07-01 19:27:00 +0900
sitemap:
  changefreq: weekly
---

0.  {:toc}

<!-- 리액트 렌더링 프로세스를 포스팅하며 설명을 돕기위해 브라우저 렌더링 프로세스도 같이 작성을 해보려합니다.<br/>
리액트를 리액트스럽게, 리액트가 추구하는 방향과 매울 민접한 관계가 있는 부분이므로, 블로그에 남기면 좋을것 같아서 쓰려고합니다. -->

## 브라우저 렌더링 프로세스(Critical Rendering Path)

리액트 렌더링을 설명하기 위해 가볍게 기존적인 브라우저 렌더링 프로세스를 정리해보았습니다.
구체적으로는 사이사이에 디테일 과정한 보다는 큰 분류도 정리하였습니다.

이미지

1. HTML를 DOM으로 변환, CSS를 CSSOM으로 변환
2. 이전 단계에서 만든 DOM과 CSSOM을 가지고 Render Tree를 만듬
3. Layout(트리 배치) 웹 페이지 안에 렌더링 트리가 그려질 위치, 크기를 계산함
4. 앞선 Layout(트리 배치) 끝나면 Paint Setup, Painting을 호출하여 실제 웹 페이지에 그려냄

이미지

여기서 JS를 사용하여 화면에 리렌더링을 일으킬수 있습니다.

예시로 DOM을 수정한다고 Render Tree를 만들고 다시 Reflow(Layout), Repaint(Painting)이 발생합니다.

그 과정에서 주의할점은 Layout과 Painting은 렌더링 과정 중에 가장 비싼 과정입니다. 즉 불필요한 과정은 사용자 경험을 떨어뜨리고 흔히 말하는 렉걸림을 발생시킵니다.
