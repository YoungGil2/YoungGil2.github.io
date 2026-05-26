---
layout: post
title: "프론트엔드 개발자 VSCode 익스텐션 정리"
date: 2026-05-26 00:00:00 +0900
description: >
  새 환경 세팅할 때 참고용으로 정리한 VSCode 익스텐션 목록.
  React / TypeScript 기반 프론트엔드 개발 기준입니다.
hide_description: true
lastmod: 2026-05-26 00:00:00 +0900
sitemap:
  changefreq: weekly
tags:
  - vscode
  - vscode 익스텐션
  - 프론트엔드
  - 개발환경
keywords: "vscode 익스텐션, vscode extensions, 프론트엔드 개발환경, React TypeScript vscode"
---

0. 
{:toc}

새 맥을 세팅하거나 환경을 다시 구성할 때마다 익스텐션을 하나씩 찾아 설치하는 게 번거로워서 한 번 정리해두려고 합니다.<br/>
React / TypeScript 기반 프론트엔드 개발 기준입니다.

## 코드 품질

### ESLint
JavaScript / TypeScript 코드의 문법 오류와 스타일 문제를 실시간으로 표시해줍니다.<br/>
팀 공통 규칙(`.eslintrc`)과 연동되기 때문에 사실상 필수입니다.

### Prettier - Code formatter
저장 시 코드를 자동으로 포맷해줍니다. ESLint와 함께 사용하면 코드 스타일을 일관되게 유지할 수 있습니다.<br/>
`settings.json`에 `"editor.formatOnSave": true` 설정을 같이 해두는 게 편합니다.

### Error Lens
에러와 경고를 해당 줄 오른쪽에 인라인으로 바로 표시해줍니다. 하단 Problems 탭을 열지 않아도 돼서 흐름이 끊기지 않습니다.

---

## 생산성

### Highlight Matching Tag
커서가 태그 위에 있을 때 여는/닫는 태그를 하이라이트해줍니다. 중첩이 깊은 JSX에서 태그 쌍을 빠르게 파악할 수 있습니다.

### Auto Close Tag
여는 태그를 입력하면 닫는 태그를 자동으로 생성해줍니다. JSX 작업 시 태그 입력 속도가 빨라집니다.

### Auto Rename Tag
여는 태그를 수정하면 닫는 태그가 자동으로 함께 바뀝니다. JSX에서 특히 유용합니다.

### Path Intellisense
파일 경로를 입력할 때 자동완성을 제공합니다. import 경로 오타를 줄여줍니다.

### Import Cost
import한 패키지의 번들 사이즈를 코드 옆에 바로 표시해줍니다. 라이브러리 선택이나 트리쉐이킹 여부를 확인할 때 유용합니다.

### TODO Highlight
코드 내 `TODO:`, `FIXME:` 주석을 눈에 띄게 하이라이트해줍니다.

---

## React / TypeScript

### ES7+ React/Redux/React-Native snippets
`rafce`, `useState` 등 자주 쓰는 React 보일러플레이트를 스니펫으로 빠르게 입력할 수 있습니다.

### vscode-styled-components
styled-components 문법에서 CSS 자동완성, 신택스 하이라이팅, 호버 미리보기를 제공합니다.

### Tailwind CSS IntelliSense
Tailwind CSS 클래스 자동완성, 호버 시 실제 CSS 값 미리보기, 오타 경고를 제공합니다.

---

## Git

### GitLens
`git blame`을 인라인으로 보여주고, 커밋 히스토리 탐색, 파일 변경 비교 등 Git 관련 기능을 대폭 강화해줍니다.

### Git Graph
브랜치 트리를 시각적으로 확인할 수 있습니다. 복잡한 브랜치 구조를 파악할 때 유용합니다.

---

## API / 네트워크

### Thunder Client
VSCode 안에서 REST API를 테스트할 수 있는 Postman 대체 도구입니다. 컬렉션 저장도 됩니다.

---

## 데이터

### Rainbow CSV
CSV / TSV 파일의 각 컬럼을 색상별로 구분해서 표시해줍니다. 컬럼이 많아도 한눈에 파악하기 쉽고, 쿼리 기능도 지원합니다.

---

## 언어

### Korean Language Pack for Visual Studio Code
VSCode UI를 한국어로 표시해줍니다. 설치 후 재시작하면 메뉴, 설정, 알림 등이 한국어로 바뀝니다.

---

## UI / 테마

### Material Icon Theme
파일 확장자에 따라 아이콘을 다르게 표시해줍니다. 파일 탐색기에서 파일 종류를 빠르게 구분할 수 있습니다.

### Color Highlight
CSS / HEX / RGB 등 색상 코드 위에 실제 색상을 배경으로 표시해줍니다. 스타일 작업 시 색상을 바로 확인할 수 있어 편합니다.

### indent-rainbow
들여쓰기 레벨마다 색을 다르게 표시해줍니다. 중첩이 깊은 코드에서 구조 파악이 쉬워집니다.
