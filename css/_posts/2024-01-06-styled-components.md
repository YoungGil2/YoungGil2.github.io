---
layout: post
title: "styled-component"
date: 2024-01-06 16:23:00 +0900
description: >
  styled-components 사용법 및 사용 후기
hide_description: true
lastmod: 2024-01-06 16:23:00 +0900
sitemap:
  changefreq: weekly
---

0. 
{:toc}

styled-components가 무엇인지, 사용법은 어떻게 되는지 그리고 사용후기 및 느낀점을 첨삭하겠습니다.

## styled-components란?

태그가 지정된 템플릿 리터럴(JavaScript에 최근 추가된 기능)과 CSS의 강력한 기능을 활용하여 styled-Components를 사용하면 실제 CSS 코드를 작성하여 구성 요소의 스타일을 지정할 수 있습니다. 또한 구성 요소와 스타일 간의 매핑을 제거합니다. 구성 요소를 하위 수준 스타일 구성으로 사용하는 것은 이보다 더 쉬울 수 없습니다!
{:.note title="공식 문서 내용"}

간단하게 말하면 스크립트 파일에서 CSS를 사용할수 있게 해주는 `CSS-in-JS` 스타일 라이브러리 입니다. 주로 react 프로젝트에서 많이 사용하는 걸로 알고 있습니다.

[styeld-components](http://https://styled-components.com/){:target="_blank"}
{:.note title="링크"}

## 사용법

~~~react
import styled from 'styled-components'

type StyledButtonProps = {
  $color: string;
};

const StyledButton = styled.button<StyledButtonProps>`
  width: 100px;
  height: 40px;
  color: ${({ $color }) => $color };
`;

function ButtonComponent() {
  return (
    <StyledButton $color="red" />
  );
}
~~~

styled.tag`` 템플릿 리터럴를 활용하여 스타일을 선언을 하면 react component를 생성합니다. props를 넘겨 동적인 선언이나 조건부 선언도 가능하다.

~~~react
import styled from 'styled-components'

type StyledButtonProps = {
  $color: string;
};

const StyledButton = styled.button<StyledButtonProps>`
  width: 100px;
  height: 40px;
  color: ${({ $color }) => $color };
`;

const StyledBlueButton = styled(StyledButton)`
  border-color: blue; 
`
~~~

styled(styled-component)``를 사용하여 기존 스타일 컴포넌트를 확장(extends)하여 새로운 컴포넌트 생성도 가능하다.

~~~react
import styled from 'styled-components'

type StyledButtonProps = {
  $color: string;
};

const StyledButton = styled.button<StyledButtonProps>`
  width: 100px;
  height: 40px;
  color: ${({ $color }) => $color };
`;

const StyledBlueButton = styled(StyledButton)`
  border-color: blue; 
`

function ButtonComponent() {
  return (
     <StyledButton $color="red" as="a" />
  );
}
~~~

`as` property를 활용하여 요소를 동적으로 교체할수도 있습니다. 주로 버튼과 앵커 태그의 스타일을 동일하나 요소가 다를 경우 사용이 가능하다.

## 장점

- css 파일을 따로 유지보수할 필요가 없어짐
- 파일 레벨이 아닌, 컴포넌트 레벨로 추상화하기 때문에 모듈화의 장점이 있다.
- 적용되는 스코프가 쉽게 예상 가능함(css 파일에 선언 할 경우 원하지 않는 부분까지 반영 되는 경우가 생기기도 함)
- javascript와 css 사이에 상수나 함수를 쉽게 공유가 가능하다.
- 짧은 길이의 유니크한 클래스를 자동으로 생성하기 때문에 코드 경량화(의도 하지 않은 class 네이밍 중복되는 이슈 극복)

## 단점

- 스크립트 파일이 다소 복잡해질 수 있다.
- 스크립트 런타임에 모든 것을 처리하기 때문에 css 파일로 관리하는 것 보다는 느린 성능을 가짐
- server side rendering시에 잠깐 스타일이 누락되어 보여지는 이슈

해당 이슈는 `CSS-in-JS` 방식에서 발생하는 이슈이다. 별도의 설정으로 해결 가능함

`CSS-in-JS` 방식은 스크립트 실행하면서 스타일이 적용되기 때문에 스크립트가 읽히기 전에 잠깐 스타일링이 누락된 화면이 보여지는 이슈가 있다.



