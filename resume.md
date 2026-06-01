---
layout: page
title: Resume
description: >
  프론트엔드 개발자 김영길의 이력서입니다.
hide_description: true
sitemap: false
---

# 김영길

### 프론트엔드 개발자

**Email** shfjrk1868@gmail.com · **GitHub** [YoungGil2](https://github.com/YoungGil2){:target="\_blank"} · **Blog** [younggil2.github.io](https://younggil2.github.io){:target="\_blank"}

---

## About me

**꼼꼼함과 안정성을 바탕으로 오래 살아남는 코드를 만드는 프론트엔드 개발자입니다.**

기능 구현에 그치지 않고, 코드가 어떻게 유지되고 확장될지를 먼저 고민합니다. FSD 패턴 도입으로 프로젝트 구조를 개선하고, Sentry 기반 에러 트래킹 체계를 구축하는 등 팀이 오래 편하게 쓸 수 있는 환경을 만드는 데 관심이 많습니다.

Next.js · TypeScript 기반의 프론트엔드 개발을 주로 담당하며, 레거시 시스템의 현대화와 서비스 안정성 개선 작업을 꾸준히 이어오고 있습니다. PHP(Laravel) 백엔드 경험을 바탕으로 프론트-백엔드 간 협업도 폭넓게 수행하고 있습니다. UI/UX 완성도와 성능 최적화에 집중하며, 사용자가 체감할 수 있는 차이를 만드는 것을 중요한 기준으로 삼고 있습니다.

---

## Skills

| 분류       | 기술                                      |
| ---------- | ----------------------------------------- |
| Language   | JavaScript, TypeScript, PHP, HTML, CSS    |
| Framework  | Next.js, React.js, Vue.js, Laravel        |
| Styling    | Tailwind CSS, styled-components           |
| State      | Zustand, Jotai, Vuex, React Query         |
| Graphics   | Fabric.js, Konva                          |
| DB / Infra | MySQL, Redis, AWS S3, AWS Lambda, Node.js |

---

## Experience

### 디앤디랩 · 기업부설 연구소 / DPromotion 개발 팀원

**2021.03 ~**

- SI 이커머스 플랫폼 구축 및 이벤트 프로모션 제작 플랫폼(디프로모션) 운영
- 디프로모션 홈페이지 프론트엔드 아키텍처 개편 및 리뉴얼
- 디프로모션 서비스 안정성 강화, 레거시 시스템 개선
- 웹 기반 그래픽 에디터 유지보수, 레거시 청산 및 리뉴얼

---

### 에코마이스 · IT기술연구소 연구원

**2020.08 ~ 2020.12**

- 2020 솔직챌린지 공모전 홈페이지 퍼블리싱
- 2020 K-Dementia 전시회 홈페이지 퍼블리싱
- 2020 K-Global@China 전시회 홈페이지 퍼블리싱

---

## Projects

### 디프로모션 웹 기반 그래픽 에디터 리뉴얼

**2026.02 ~ 2026.05**

- 기존 `Vue2` / `Vuex` / `Fabric.js` 기반 레거시 에디터를 `Next.js` + `React-Konva` 스택으로 전면 리뉴얼, 아키텍처 설계 및 구현 주도
- Gemini AI 이미지 생성, Remove.bg 배경 제거 등 AI 특화 기능 통합으로 에디터 고도화
- AI 부가기능 유료화를 통한 신규 수익 모델 확보

`Next.js` `TypeScript` `Konva` `Zustand` `React Query`

---

### 디프로모션 보안 패키지 에센셜 부가 서비스 개발

**2026.01 ~ 2026.02**

- 기업 공용 계정 공유로 인한 보안 리스크를 최소화하기 위해 접속자 IP 및 이메일 기반의 2단계 인증 시스템 설계 및 구현
- 인증 성공 시에만 세션을 부여하는 보안 로직을 적용하여 비인가자의 접근을 원천 차단하고 유료 구독 모델의 기술적 기반 마련
- 개인정보가 포함된 민감 데이터 열람 및 다운로드 시, 담당자별 접근 이력 및 행동 로그를 정교하게 수집하여 보안 사고 예방 체계 구축

`PHP Laravel` `Redis` `JavaScript`

---

### 프로모션 참여용 인증 서비스 및 화이트리스트 개발

**2025.11 ~ 2025.12**

- 특정 휴대폰 번호 및 이메일 주소 기반의 화이트리스트 필터링 기능을 구현하여, 지정된 대상만 참여 가능한 프라이빗 이벤트 프로모션 환경 구축
- 다채널(Email, SMS) 외부 API(NHN Cloud) 연동을 통한 인증 번호 발송 및 검증 로직 개발
- Redis TTL을 활용한 휘발성 인증번호 관리 및 카운팅 기반의 요청 제한으로 비용 절감 및 시스템 안정성 확보

`Node.js` `AWS Lambda` `Redis`

---

### 디프로모션 홈페이지 리뉴얼

**2025.01 ~ 2025.05**

- 기존 스택(Laravel Blade, jQuery)을 Next.js 기반으로 마이그레이션
- FSD(Feature-Sliced Design) 패턴을 도입해 비즈니스 로직, 뷰 계층을 분리하여 프로젝트 확장성 및 유지보수 효율성 확보
- 다국어 지원(i18n) 라우팅 및 번역 시스템을 구축하여 국문·영문 서비스 환경 구축

`Next.js` `TypeScript` `Tailwind` `Jotai` `React Query`

---

### 웹 기반 그래픽 에디터 개발 및 고도화

- 에러 로깅 부재 환경에 Sentry를 도입하여 실시간 에러 트래킹 환경 구축
- Canvas 에디터 내 GIF 업로드 및 프레임 단위 조작 기능을 위한 렌더링 환경 구축

`Vue.js` `Vuex` `TypeScript` `Fabric.js`

---

### 대형 이커머스 플랫폼 구축

**2021.03 ~ 2021.05**

- **아식스 코리아 공식 스토어**: 상품 상세 페이지부터 결제/주문 프로세스까지 핵심 구매 동선의 UI 마크업 및 크로스 브라우징 최적화
- **락앤락 공식 온라인몰**: 커뮤니티 게시판 및 고객센터 웹 인터페이스 구축

---

## Education

### 한양사이버대학교 · 컴퓨터공학과

**2023.03 ~**
