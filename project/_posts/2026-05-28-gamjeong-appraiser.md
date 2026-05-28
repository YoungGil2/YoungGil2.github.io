---
layout: post
title: 감정 평가사 - AI로 내 문장의 온도를 측정하는 사이드 프로젝트
date: 2026-05-28 12:00:00 +0900
description: >
  Next.js, OpenAI, Supabase로 만든 커뮤니케이션 톤 분석 서비스
hide_description: true
lastmod: 2026-05-28 12:00:00 +0900
sitemap:
  changefreq: weekly
tags:
  - Next.js
  - React
  - TypeScript
  - OpenAI
  - Supabase
  - AWS Amplify
  - 사이드 프로젝트
  - 포트폴리오
  - 프론트엔드
keywords: "Next.js 사이드 프로젝트, OpenAI API 활용, Supabase 인증, AWS Amplify 배포, FSD 아키텍처, 프론트엔드 포트폴리오, gpt-4o-mini, Kakao OAuth"
---

Next.js 16, React 19, TypeScript, Tailwind CSS v4, OpenAI, Supabase, AWS Amplify
{:.note title="사용"}

## 왜 만들었나

직장생활을 하다 보면 메시지 한 줄 보내는 데 10분 넘게 고민할 때가 있습니다.

> "확인 부탁드립니다." — 너무 차갑나?  
> "혹시 가능하시면 봐주실 수 있을까요?" — 너무 소극적인가?  
> "감사합니다 😊" — 아부처럼 보이나?

나만 이런 고민을 하는 건 아닐 것 같았습니다. 비슷한 상황에서 매번 혼자 머릿속으로 검열하고 있는 사람들이 분명 있을 거라고 생각했습니다.

그래서 만들었습니다. **감정 평가사(Gamjeong Appraiser)** — 문장을 입력하면 4가지 축으로 톤을 점수화하고, 3가지 스타일로 다듬어주는 서비스입니다.

[감정 평가사 바로가기](https://main.ds2mmtlu313rd.amplifyapp.com/){:target="_blank"}
{:.note title="배포"}

![감정 평가사 메인](/assets/img/project/ic_gamjeong-appraiser-main.png){:style="border: 1px solid #e5e7eb; border-radius: 8px;"}

## 주요 기능

**1. 컨텍스트 기반 분석**  
상사·임원, 고객·클라이언트, 팀원·동료, 외부 파트너 중 대상을 먼저 선택합니다. 같은 문장이라도 누구에게 보내느냐에 따라 적절한 톤이 다르기 때문입니다.

**2. 4축 톤 점수화**

| 축              | 설명                 |
| --------------- | -------------------- |
| 따뜻함 ↔ 차가움 | 감정적 온도감        |
| 격식 ↔ 캐주얼   | 상황에 맞는 문체인지 |
| 직접적 ↔ 우회적 | 의도 전달의 명확성   |
| 자신감 수준     | 주장의 강도와 확신감 |

**3. 3가지 다듬은 버전 제안**  
프로페셔널하게, 따뜻하고 친근하게, 간결하고 직접적으로 — 각 버전을 바로 복사해서 쓸 수 있습니다.

**4. 분석 내역 저장**  
카카오 소셜 로그인 후 분석 기록을 저장하고 나중에 다시 볼 수 있습니다.

![분석 내역](/assets/img/project/ic_gamjeong-appraiser-history.png){:style="border: 1px solid #e5e7eb; border-radius: 8px;"}

## 기술 스택

| 분류      | 기술                   |
| --------- | ---------------------- |
| Framework | Next.js 16, React 19   |
| Language  | TypeScript             |
| Styling   | Tailwind CSS v4        |
| AI        | OpenAI (gpt-4o-mini)   |
| Auth / DB | Supabase (Kakao OAuth) |
| Deploy    | AWS Amplify            |

## 핵심 구현: 톤 분석 API

![분석 페이지](/assets/img/project/ic_gamjeong-appraiser-analyze.png){:style="border: 1px solid #e5e7eb; border-radius: 8px;"}

분석의 핵심은 `/api/analyze` 라우트입니다. OpenAI에 JSON 형식으로 응답을 요청하고, 결과를 Supabase에 저장합니다.

```ts
const completion = await client.chat.completions.create({
  model: 'gpt-4o-mini',
  response_format: { type: 'json_object' },
  messages: [
    {
      role: 'system',
      content:
        '당신은 한국어 비즈니스 커뮤니케이션 전문가입니다. 요청된 JSON 형식으로만 응답합니다.',
    },
    {
      role: 'user',
      content: `아래 메시지를 분석해주세요.
- 보내는 대상: ${contextLabel}
- 원문: "${text}"

{
  "scores": {
    "warmth": <0~100>,
    "formality": <0~100>,
    "directness": <0~100>,
    "confidence": <0~100>
  },
  "summary": "<톤 전반 평가>",
  "professional": "<프로페셔널 버전>",
  "warm": "<따뜻한 버전>",
  "concise": "<간결한 버전>"
}`,
    },
  ],
});
```

`response_format: { type: 'json_object' }` 옵션을 사용해 OpenAI가 항상 파싱 가능한 JSON을 반환하도록 강제했습니다. 이 옵션 없이는 가끔 마크다운 코드블록으로 감싸서 응답하는 경우가 있어 파싱이 깨졌습니다.

로그인 사용자의 경우 분석 결과를 Supabase에 저장합니다. 비로그인 사용자도 분석은 가능하되, 내역은 저장되지 않습니다.

## 아키텍처: FSD (Feature-Sliced Design)

프로젝트 구조는 FSD를 채택했습니다.

```
src/
├── app/          # Next.js 라우팅, API 라우트
├── views/        # 페이지 단위 컴포넌트 (LandingPage, AnalyzePage, HistoryPage)
├── widgets/      # 독립적인 UI 블록 (Header, Footer)
├── entities/     # 비즈니스 도메인 (analysis 관련 타입, 상수)
└── shared/       # 공통 유틸, Supabase 클라이언트
```

규모가 크지 않아서 오버엔지니어링일 수도 있지만, 포트폴리오 목적이기도 해서 의식적으로 레이어 분리를 연습했습니다. 특히 `entities/analysis`에 상수와 타입을 모아두니 API 라우트와 뷰 컴포넌트 양쪽에서 일관되게 쓸 수 있어서 좋았습니다.

## 배포: AWS Amplify

AWS Amplify를 처음 써봤는데 Next.js App Router와 궁합이 좋았습니다. GitHub 연동 후 `amplify.yml`만 작성하면 브랜치 푸시마다 자동 배포됩니다. 환경변수 관리도 콘솔에서 깔끔하게 됩니다.

## 마치며

작은 프로젝트지만 "내가 실제로 쓰고 싶어서" 만든 것이라 완성도를 높이는 데 재미가 있었습니다. 실제로 메시지 보내기 전에 종종 써보고 있습니다.

다음에는 분석 내역을 바탕으로 개인 커뮤니케이션 패턴을 시각화하는 기능을 추가해볼 생각입니다.
