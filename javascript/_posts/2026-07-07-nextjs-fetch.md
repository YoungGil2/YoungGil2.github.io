---
layout: post
title: 'Next.js fetch — 기본 fetch가 어떻게 확장되었나'
date: 2026-07-07 12:00:00 +0900
description: >
  브라우저/Node.js 기본 fetch와 Next.js가 확장한 fetch의 차이를 캐싱 전략,
  메모이제이션, Server/Client Component 동작 차이 중심으로 정리합니다.
hide_description: true
lastmod: 2026-07-07 12:00:00 +0900
sitemap:
  changefreq: weekly
tags:
  - nextjs
  - fetch
  - 캐싱
  - Server Component
  - App Router
  - 프론트엔드
keywords: 'Next.js fetch, Next.js 캐싱, fetch cache, revalidate, Server Component fetch, App Router data fetching'
---

0.  {:toc}

Next.js를 쓰다 보면 `fetch`를 그냥 쓰는데 캐싱이 자동으로 된다거나,<br/>
`revalidate` 옵션을 붙이면 ISR처럼 동작한다는 이야기를 듣게 됩니다.<br/>
이번 포스팅에서는 **JS 기본 fetch가 Next.js에서 어떻게 확장되었는지** 정리해보겠습니다.

## 기본 fetch vs Next.js fetch

브라우저와 Node.js(v18+)의 기본 `fetch`는 네트워크 요청을 보내고 응답을 받는 역할만 합니다.<br/>
Next.js는 이 `fetch`를 **서버 빌드 타임에 monkey-patch**하여 캐싱·재검증 동작을 내장합니다.

```
기본 fetch  →  네트워크 요청 → 응답 반환
Next.js fetch →  캐시 확인 → (HIT) 캐시 반환
                           → (MISS) 네트워크 요청 → 캐시 저장 → 응답 반환
```

Next.js 공식 문서에서는 이를 **Data Cache**라고 부르며, 서버 사이드에서 요청 결과를 영구적으로(또는 설정한 시간만큼) 보관합니다.<br/>
이 캐시는 배포 간에도 유지되며, 명시적으로 무효화하거나 재검증하기 전까지 살아있습니다.

> **주의**: 이 확장은 **Server Component(서버 환경)** 에서만 동작합니다.<br/>
> Client Component에서의 `fetch`는 기본 브라우저 fetch와 동일하게 동작합니다.

---

## cache 옵션

Next.js fetch의 핵심은 `cache` 옵션입니다.

### force-cache

```typescript
// v13~v14에서는 cache 옵션 생략 시 force-cache가 기본으로 적용됩니다.
// v15부터는 no-store가 기본값으로 변경되었으므로 명시적으로 작성해야 합니다.
const res = await fetch('https://api.example.com/data', {
  cache: 'force-cache',
});
```

- 최초 요청 시 네트워크 요청 후 결과를 캐시에 저장합니다.
- 이후 동일 URL 요청은 캐시에서 바로 반환합니다.
- **빌드 타임에 정적으로 생성되는 페이지**와 같은 동작입니다.

캐시가 히트된 경우 서버 로그에서 아래와 같이 확인할 수 있습니다.

![nextjs-fetch-cache-hit](/assets/img/javascript/nextjs-fetch-cache-hit.png){:style="border: 1px solid #ccc;"}

`17ms`라는 짧은 응답 시간에서 알 수 있듯이, 네트워크 요청 없이 캐시에서 바로 반환된 것을 확인할 수 있습니다.

### no-store (v15 기본값)

```typescript
// v15부터는 cache 옵션 생략 시 no-store가 기본으로 적용됩니다.
const res = await fetch('https://api.example.com/data');

// 명시적으로 쓰면
const res = await fetch('https://api.example.com/data', {
  cache: 'no-store',
});
```

- 캐시를 전혀 사용하지 않습니다.
- 요청마다 항상 네트워크를 통해 최신 데이터를 가져옵니다.
- **실시간 데이터**가 필요한 경우에 사용합니다.
- 이 옵션이 적용된 fetch가 있으면 해당 페이지를 동적 렌더링으로 전환합니다.

실제 Next.js 서버 로그에서 캐시가 스킵되는 상황을 확인할 수 있습니다.

![nextjs-fetch-cache-skip](/assets/img/javascript/nextjs-fetch-cache-skip.png){:style="border: 1px solid #ccc;"}

`fetchCache = default-no-store`는 fetch 옵션으로 인해 캐시가 비활성화된 경우이고,<br/>
`auto no cache`는 Next.js가 동적 함수(`cookies()`, `headers()` 등) 사용을 감지해 자동으로 캐시를 끈 경우입니다.

### next.revalidate

```typescript
const res = await fetch('https://api.example.com/data', {
  next: { revalidate: 3600 }, // 1시간(초 단위)
});
```

- 지정한 시간(초) 이후에 캐시를 재검증합니다.
- 시간이 지나면 백그라운드에서 새 데이터를 가져와 캐시를 갱신합니다.
- 기존 ISR(Incremental Static Regeneration)과 동일한 개념입니다.
- `revalidate: 0`으로 설정하면 `no-store`와 동일하게 동작합니다.

---

## Server vs Client Component에서의 fetch

```typescript
export default async function Page() {
  const res = await fetch('https://api.example.com/data', {
    next: { revalidate: 60 },
  });
  const data = await res.json();
  return <div>{data.title}</div>;
}
```

```typescript
'use client';

import { useEffect, useState } from 'react';

export default function ClientSection() {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetch('https://api.example.com/data') // 캐싱 없음, 매번 요청
      .then(res => res.json())
      .then(setData);
  }, []);

  return <div>{data?.title}</div>;
}
```

Client Component에서는 Next.js의 fetch 확장이 적용되지 않습니다.<br/>
클라이언트 사이드 데이터 패칭이 필요하다면 **TanStack Query(React Query)** 나 **SWR** 같은 라이브러리를 함께 사용하는 것이 일반적입니다.

---

## 정리

| 옵션                                  | 동작               | 언제 사용                  |
| ------------------------------------- | ------------------ | -------------------------- |
| `cache: 'force-cache'` (v13~v14 기본) | 영구 캐시          | 자주 안 바뀌는 정적 데이터 |
| `cache: 'no-store'` (v15+ 기본)       | 캐시 없음          | 실시간 데이터              |
| `next: { revalidate: N }`             | N초 후 재검증      | 주기적으로 갱신되는 데이터 |
| `next: { tags: [...] }`               | 태그로 수동 무효화 | CMS, 이벤트 기반 갱신      |

Next.js의 fetch 확장은 단순한 네트워크 요청 도구를 **서버 사이드 캐싱 레이어**로 끌어올린 것입니다.<br/>
어떤 데이터가 얼마나 자주 바뀌는지 파악하고 적절한 전략을 선택하는 것이 핵심입니다.
