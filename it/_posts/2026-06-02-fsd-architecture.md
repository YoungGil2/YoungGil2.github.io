---
layout: post
title: 'FSD 아키텍처 실무 적용기: Next.js 프로젝트에 도입한 이유와 달라진 것들'
date: 2026-06-02 12:00:00 +0900
description: >
  일반적인 React 컴포넌트 구조의 한계를 느끼고 Feature-Sliced Design을 도입한 과정과
  실제로 달라진 점을 공유합니다.
category: IT
hide_description: true
lastmod: 2026-06-02 12:00:00 +0900
sitemap:
  changefreq: weekly
tags:
  - FSD
  - Next.js
  - React
  - 아키텍처
  - 프론트엔드
  - TypeScript
keywords: "FSD 아키텍처, Feature-Sliced Design, Next.js 프로젝트 구조, React 폴더 구조, 프론트엔드 아키텍처"
---

Next.js, React, TypeScript, FSD
{:.note title="사용"}

0. 
{:toc}

## 도입 배경

기존 서비스는 Laravel Blade 기반으로 운영되고 있었습니다. 프론트엔드를 Next.js + React로 전면 리뉴얼하면서, 처음부터 구조를 잘 잡고 싶었습니다.

이전에 React 프로젝트를 경험하면서 가장 자주 마주쳤던 구조는 이런 모습이었습니다.

```
src/
├── components/
│   ├── Button.tsx
│   ├── Header.tsx
│   ├── UserCard.tsx
│   ├── ProductList.tsx
│   ├── CheckoutForm.tsx
│   └── ... (계속 늘어남)
├── pages/
├── hooks/
└── utils/
```

초반엔 깔끔해 보이지만, 프로젝트가 커질수록 문제가 생기기 시작합니다.

- `components/` 폴더 안에 페이지 전용 컴포넌트와 공통 컴포넌트가 뒤섞입니다.
- 어떤 컴포넌트가 어디서 쓰이는지 추적하기 어려워집니다.
- 비즈니스 도메인 경계가 모호해져, 기능 추가 시 어디에 파일을 놓아야 할지 고민하게 됩니다.
- 컴포넌트 간 의존성이 자연스럽게 꼬입니다.

리뉴얼하는 김에 이 문제들을 처음부터 해결하고 싶었고, FSD를 선택했습니다.

## FSD란?

[공식 문서](https://fsd.how/kr/docs/get-started/overview/){:target="_blank"}
{:.note title="링크"}

**Feature-Sliced Design(FSD)**은 프론트엔드 애플리케이션을 위한 아키텍처 방법론입니다. 코드를 **레이어(Layer)** 와 **슬라이스(Slice)** 단위로 나누어 관리합니다.

```
src/
├── app/        # 앱 진입점, 전역 설정, 라우터
├── pages/      # 라우팅 단위 페이지 (여러 widget/feature 조합)
├── widgets/    # 독립적인 UI 블록 (Header, Sidebar 등 페이지에 조립되는 단위)
├── features/   # 사용자 인터랙션 단위 기능 (로그인, 검색, 좋아요 등 단일 행위)
├── entities/   # 비즈니스 도메인 (User, Product 등 — 타입, 상태, UI 포함)
└── shared/     # 공통 유틸, UI 컴포넌트, 설정 (어떤 레이어도 참조 가능)
```

조금 더 풀어보면 이렇습니다.

- **widgets**: 여러 entity나 feature를 조합한 독립 UI 블록입니다. Header, Footer, Sidebar처럼 페이지에 조립되는 단위로, 자체적인 상태를 가질 수 있습니다.
- **features**: 사용자의 단일 행위 단위입니다. "로그인하기", "댓글 작성하기", "분석 결과 저장하기"처럼 하나의 인터랙션을 담당합니다. entities를 참조할 수 있지만 다른 feature를 참조하면 안 됩니다.
- **entities**: 비즈니스 도메인의 핵심입니다. `User`, `Product`, `Analysis` 같은 도메인 단위로 슬라이스를 나누며, 타입, 상수, 도메인 관련 UI를 함께 관리합니다. 이 레이어가 잘 설계될수록 타입 일관성이 올라갑니다.

각 레이어는 **단방향 의존성** 규칙을 따릅니다. 상위 레이어는 하위 레이어를 참조할 수 있지만, 하위 레이어는 상위 레이어를 참조할 수 없습니다. 이 규칙 하나가 의존성 꼬임을 구조적으로 막아줍니다.

## 트러블슈팅: Next.js와 FSD의 충돌

[Next.js와 함께 사용하기 - 공식 문서](https://fsd.how/kr/docs/guides/tech/with-nextjs/){:target="_blank"}
{:.note title="링크"}

FSD를 Next.js에 적용하면서 가장 먼저 부딪힌 문제는 `app` 레이어 충돌이었습니다.

- **FSD의 `app` 레이어**: 전역 설정, 프로바이더, 스타일 초기화 등 앱 전체를 담당
- **Next.js의 `app` 폴더**: 파일 시스템 기반 라우팅을 담당

같은 이름이지만 역할이 완전히 다릅니다. Next.js는 `app/` 폴더 구조로 라우팅을 결정하기 때문에, FSD 구조와 그대로 섞으면 충돌이 생깁니다.

### pages 레이어도 문제였다

FSD의 `pages` 레이어 역시 Next.js의 Pages Router와 이름이 겹칩니다. `src/pages`를 두면 Next.js가 이를 Pages Router로 인식해 빌드가 실패합니다.

이를 해결하기 위해 FSD의 `pages` 레이어를 **`views`로 이름을 바꿨습니다.** 의미상으로도 "페이지를 구성하는 뷰 컴포넌트"에 가까워서 오히려 더 자연스러웠습니다.

### 최종 구조

루트의 `app/`은 Next.js 라우팅 전용으로만 쓰고, 실제 컴포넌트는 `src/` 아래 FSD 구조로 분리했습니다.

```
(루트)
├── app/              # Next.js 라우팅 전용 (page.tsx, layout.tsx만)
│   └── (route)/
│       └── page.tsx  # → src/views 컴포넌트를 import해서 연결
└── src/
    ├── app/          # FSD app 레이어 (전역 설정, 프로바이더)
    ├── views/        # FSD pages 레이어 대체 (실제 페이지 컴포넌트)
    ├── widgets/
    ├── entities/
    └── shared/
```

루트의 `app/page.tsx`는 라우팅 연결만 담당합니다.

```tsx
// app/(route)/page.tsx
export { default } from '@/views/home/ui/HomePage';
```

이렇게 하면 Next.js의 라우팅 요구사항과 FSD 아키텍처를 모두 충족할 수 있습니다.

## 실제 적용 구조

적용한 프로젝트의 구조를 간략하게 보면 이렇습니다.

```
src/
├── app/
│   ├── layout.tsx
│   └── (route)/page.tsx
├── views/             # 페이지 단위 컴포넌트
│   └── home/
│       └── ui/
│           └── HomePage.tsx
├── widgets/           # 독립 UI 블록
│   ├── header/
│   └── footer/
├── entities/          # 비즈니스 도메인
│   └── user/
│       ├── model/     # 타입, 상수
│       └── ui/        # 도메인 관련 컴포넌트
└── shared/            # 공통 모듈
    ├── lib/
    ├── ui/
    └── config/
```

Next.js App Router를 사용하기 때문에 `pages` 레이어 대신 `views`를 사용했습니다. 라우팅 자체는 `app/` 폴더에서 담당하고, 실제 뷰 로직은 `views/`에 분리했습니다.

## 달라진 점

### 1. 타입(interface) 구분이 명확해졌다

기존 방식에서는 타입 파일을 어디에 두어야 할지 애매했습니다. `types/` 폴더를 따로 만들기도 하고, 컴포넌트 파일 안에 inline으로 정의하기도 했습니다.

FSD에서는 **도메인별로 `entities` 레이어 안에 타입을 모아둡니다.**

```
entities/
└── analysis/
    ├── model/
    │   ├── types.ts      # AnalysisResult, ScoreData 등
    │   └── constants.ts  # CONTEXT_OPTIONS, TONE_AXIS_LABELS 등
    └── ui/
```

API 라우트와 뷰 컴포넌트 양쪽에서 같은 타입을 import해서 쓰니, 타입 불일치 문제가 없어졌습니다. 어떤 도메인의 타입인지도 경로만 봐도 바로 알 수 있습니다.

### 2. 전역 상태 관리와 궁합이 잘 맞는다 (개인적인 생각)

개인적으로 Zustand나 Jotai 같은 상태 관리 라이브러리와 함께 쓸 때 FSD가 특히 잘 맞는다고 느꼈습니다.

기존 방식에서는 전역 상태가 어디에 있어야 하는지 기준이 없다 보니, 상태가 여기저기 분산되는 경우가 많았습니다. FSD에서는 상태도 도메인 관점으로 관리합니다. `entities/user/model/store.ts`, `entities/analysis/model/store.ts`처럼 도메인 슬라이스 안에 상태를 두면, 어떤 도메인의 상태인지 명확하고 불필요한 전역 상태 분산을 자연스럽게 막을 수 있었습니다.

### 3. 기능 추가와 확장성이 좋아졌다

새로운 기능을 추가할 때 "이 파일을 어디에 놓아야 하지?"라는 고민이 줄었습니다. 도메인과 레이어 기준이 명확하기 때문에 자연스럽게 위치가 정해집니다.

예를 들어 "분석 내역 저장" 기능을 추가한다면:
- 분석 결과 타입 → `entities/analysis/model/types.ts`
- 저장 API 호출 → `features/save-analysis/api/`
- 내역 목록 UI → `views/history/ui/`

기능이 늘어나도 각 슬라이스가 독립적이라 다른 기능에 영향을 주지 않습니다.

## 주의할 점

초반 구조 설계에 시간이 들고, 팀원이 FSD에 익숙하지 않다면 온보딩 시간이 필요합니다. **작은 프로젝트에는 과할 수 있습니다.** 단순한 페이지 하나를 만드는 데 레이어를 여러 개 거쳐야 할 때 번거롭게 느껴지기도 합니다.

다만 이 과정 자체가 **도메인 관점으로 서비스를 바라보는 연습**이 됩니다. "이 기능은 어느 레이어에 속하는가"를 팀원과 함께 논의하다 보면, 서비스 구조에 대한 이해와 방향성이 자연스럽게 맞춰집니다.

저는 이 과정이 단순히 폴더를 나누는 것보다 훨씬 값진 경험이었습니다.

## 마치며

FSD를 도입하고 나서 단점보다 장점을 훨씬 많이 느꼈습니다. 구조만 잘 잡혀 있으면 유지보수성이 크게 올라가고, 기능이 늘어나도 기존 코드를 건드릴 일이 줄어듭니다.

무엇보다 **도메인 관점으로 생각하는 습관**이 생긴 게 가장 큰 수확이었습니다. 기능 하나를 추가할 때 "어떤 도메인의 관심사인가"를 먼저 떠올리게 되니, 설계 자체가 점점 명확해지는 걸 느꼈습니다.

저도 사이드 프로젝트에 의식적으로 FSD를 적용해보고 있습니다. 규모 대비 과할 수 있다는 걸 알면서도, 작은 프로젝트일수록 전체 구조를 한눈에 보면서 감을 잡기 좋습니다. 아직 사용해본 적 없다면 사이드 프로젝트 하나에 먼저 적용해보는 걸 권장합니다.
