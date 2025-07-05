---
layout: post
title: "리액트 랜더링 프로세스(브라우저 렌더링 프로세스)"
date: 2025-07-01 19:27:00 +0900
description: >
  리액트의 렌더링 프로세스를 설명하기 위해 브라우저 렌더링 프로세스,
  중요한 개념들을 정리합니다.
hide_description: true
lastmod: 2025-07-01 19:27:00 +0900
sitemap:
  changefreq: weekly
tags:
  - react
  - 리액트 렌더링
  - 브라우저 렌더링
  - 리렌더링 최적화
  - 가상 DOM
  - 프론트엔드 성능
keywords: "리액트 렌더링, React rendering, 브라우저 렌더링 과정, 리렌더링 최적화, React Virtual DOM, 프론트엔드 성능"
---

0. 
{:toc}

리액트 렌더링 프로세스를 포스팅하며 설명을 돕기위해 브라우저 렌더링 프로세스도 같이 작성을 해보려합니다.<br/>
리액트를 리액트스럽게, 리액트가 추구하는 방향과 매울 민접한 관계가 있는 부분이므로, 블로그에 남기려 정리해보려고 합니다.

## 브라우저 렌더링 프로세스(Critical Rendering Path)

리액트 렌더링을 설명하기 위해 가볍게 기본적인 브라우저 렌더링 프로세스를 정리해보았습니다.<br/>
구체적으로는 사이사이에 디테일 과정한 보다는 큰 분류도 정리하였습니다.

![browser_rendering-1](/assets/img/javascript/browser_rendering-1.png){:style="border: 1px solid #ccc;"}

1. HTML를 DOM으로 변환, CSS를 CSSOM으로 변환
2. 이전 단계에서 만든 DOM과 CSSOM을 가지고 Render Tree를 만듬
3. Layout(트리 배치) 웹 페이지 안에 렌더링 트리가 그려질 위치, 크기를 계산함
4. 앞선 Layout(트리 배치) 끝나면 Paint Setup, Painting을 호출하여 실제 웹 페이지에 그려냄

![browser_rendering-2](/assets/img/javascript/browser_rendering-2.png){:style="border: 1px solid #ccc;"}

여기서 JS를 사용하여 화면에 리렌더링을 일으킬수 있습니다.

예시로 DOM을 수정한다고 Render Tree를 만들고 다시 Reflow(Layout), Repaint(Painting)이 발생합니다.

그 과정에서 주의할점은 Layout과 Painting은 렌더링 과정 중에 가장 비싼 과정입니다. 즉 불필요한 과정은 사용자 경험을 떨어뜨리고 흔히 말하는 렉걸림을 발생시킵니다.

예시로 이런 극단적인 코드가 있다고 가정해보겠습니다.

좋은 방향과 안좋은 방향을 예시입니다.

```typescript
// 1000번의 리렌더링
const $box = document.getElementById("box");

for (let i = 0; i < 1000; i++) {
  $box.style.backgroundColor = i % 2 === 0 ? 'red' : 'blue';
}


// 1번의 리렌더링
const $box = document.getElementById("box");

let color = '';
for (let i = 0; i < 1000; i++) {
  color = i % 2 === 0 ? 'red' : 'blue'; // 마지막만 적용됨
}

$box.style.backgroundColor = color;
```

결론적으로 여기서 얘기하고 싶은 부분은 스크립트를 통해 DOM을 직접 조작할때는 동시에 여러번의 리렌더링을 발생시켜야할때는 <br/>
최대한 모아서 한번만 렌더링 과정을 최소화하여야 성능적으로 효과적일것입니다.

프론트엔드에서 성능 최적화를 이야기할 때,<br/>
가장 큰 부분 중 하나는 바로 브라우저의 렌더링 프로세스입니다.

## 리액트 렌더링 프로세스

React는 이러한 렌더링 과정을 기본적으로 자동으로 도와주는 구조를 가지고 있으며,<br/>
state라는 개념을 통해 렌더링 흐름을 제어하고 최적화할 수 있도록 설계되어 있습니다.

![react_rendering-1](/assets/img/javascript/react_rendering-1.png){:style="border: 1px solid #ccc;"}

![react_rendering-2](/assets/img/javascript/react_rendering-2.png){:style="border: 1px solid #ccc;"}

React는 컴포넌트를 렌더링할 때 React Element를 생성합니다. 이 React Element들은 실제 DOM이 아닌,<br/>
JavaScript 객체 형태의 Virtual DOM(가상 DOM)으로 구성됩니다.

이 과정이 우리가 알고있는 리액트에 장점에 부분입니다.

실제 DOM을 조작하는것이 아닌 가상 DOM을 메모리 상에서 관리하여 여러가지 장점을 가지고 있습니다.

1. 실제 DOM을 수정하지 않고 메모리상에 가상 DOM을 수정하기에 자유롭고 유연하다.

2. 새로 만들고 부수는 작업과 복잡한 연산을 기본 방식과 비교적 훨씬 적은 자원만으로 가능해진다.

3. 밑에서 말할 내용이지만 그 과정에서 새로 만든 가상 DOM과 이전에 가상 DOM을 비교하여 실제로 필요한 부분에 DOM만 조작하여 성능에 이점이 된다.

![react_rendering-3](/assets/img/javascript/react_rendering-3.png){:style="border: 1px solid #ccc;"}

앞서 생성된 Virtual DOM을 기반으로 Actual DOM에 변경 사항을 반영합니다.

이 과정에서 위에서 설명한 브라우저 렌더링이 시작하여 Critical Rendering Path를 따라 화면을 그리게 됩니다.

![react_rendering-4](/assets/img/javascript/react_rendering-4.png){:style="border: 1px solid #ccc;"}

리렌더링 방식은 위에서 설명한 과정 중 비교 단계에서 진행됩니다.

이전 Virtual DOM과 새로운 Virtual DOM 간의 차이점을 계산합니다. 차이점만 선별하여 Actual DOM에 적용합니다.



과정을 통해 React는 업데이트가 동시에 여러 개 발생하더라도, 내부적으로 이를 모아서 처리 합니다.<br/>
그 결과, 실제 DOM에는 최소한 한 번만 변경 작업이 수행되며, 불필요한 렌더링을 방지할 수 있습니다.

React의 궁극적인 목표는 실제 DOM 조작을 최소화하여 UI 성능을 최적화하는 것입니다.<br/>
Virtual DOM을 활용한 계산 및 비교 과정을 통해 React는 선언적인 코드 구조를 유지하면서도 효율적인 렌더링을 보장합니다.