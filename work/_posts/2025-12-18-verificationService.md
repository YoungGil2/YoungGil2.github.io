---
layout: post
title: "이메일/SMS 인증 시스템 구축기"
date: 2025-12-18 14:00:00 +0900
description: >
  AWS Lambda와 Redis를 이용한 휘발성 인증 번호 관리 시스템 설계
hide_description: true
lastmod: 2025-01-19 14:00:00 +0900
sitemap:
  changefreq: weekly
tags:
  - aws
  - lambda
  - api gateway
  - redis
  - sms
  - email
  - nhn toast
---

0. 
{:toc}

최근 프로젝트에서 이벤트 프로모션 응모 시 참여자에게 이메일 및 문자 인증을 수행하는 시스템을 구축했습니다.
이번 포스팅에서는 **AWS Lambda와 Redis**를 활용하여 인증 번호를 발급하고 검증했던 경험을 기록해 보려 합니다.

## 기술 스펙 및 설계

**AWS Lambda, AWS API Gateway, Redis, NHN Toast API**<br/>
이벤트 기간 동안 순간적으로 몰릴 수 있는 트래픽을 고려하여, 빠른 응답 속도를 보장하는 것이 핵심이었습니다.

### 인증 번호 생성 및 발송

*   **난수 생성**: 보안성을 고려하여 예측 불가능한 6자리 숫자 난수를 생성합니다.
*   **인증 번호 발송**: NHN Toast API를 이용하여 생성한 인증 번호를 발송합니다.

[NHN Cloud Email API 가이드](https://docs.nhncloud.com/ko/Notification/Email/ko/api-guide/){:target="_blank"}<br/>
[NHN Cloud SMS API 가이드](https://docs.nhncloud.com/ko/Notification/SMS/ko/api-guide/){:target="_blank"}
{:.note title="참고 링크"}

#### 트러블 슈팅

NHN TOAST API 연동 시 겪었던 이슈입니다. 발송 요청이 실패하더라도 HTTP Status Code로 `200 OK`를 반환하기 때문에, 일반적인 `try-catch` 문이나 에러 핸들링 로직에서 걸러지지 않는 문제가 있었습니다.
따라서 응답 본문(Body)의 `header.isSuccessful`과 `header.resultCode`를 직접 확인하여 명시적으로 에러를 발생시키는 로직을 추가하여 해결하였습니다.

```javascript
// TOAST API에서 발송에 실패해도 statusCode 200을 내려줌, 따라서 에러 캐치에 안걸려서 검증 필요
if (!data.header.isSuccessful || data.header.resultCode !== 0) {
  console.error('TOAST 이메일 발송 실패:', data);
  throw new Error('EMAIL_SEND_FAILED');
}
```

*   **Redis 활용 (TTL)**: 생성된 인증 번호는 영구 저장할 필요가 없는 휘발성 데이터입니다. 따라서 Redis의 `SETEX` 명령어를 활용하여 데이터의 생명주기(TTL)를 관리했습니다.
    *   **Key**: `verify:{수신자정보}` (예: `verify:01012345678`)
    *   **Value**: `123456` (인증 번호)
    *   **TTL**: 180초 (3분) - 해당 시간이 지나면 자동으로 만료(삭제)되도록 설정.

### 과도한 요청 방지

SMS나 이메일 발송은 비용이 발생하는 작업이며, 악의적인 사용자가 무한정 요청을 보낼 경우 서버 부하와 과금 폭탄으로 이어질 수 있습니다. 이를 방지하기 위해 **Rate Limiting** 기능을 구현했습니다.

*   **Redis 활용**: 사용자의 식별자(핸드폰 번호, 이메일)와 날짜를 조합하여 `rate_limit:${value}:${getTimeKey()}`와 Key를 생성하고, Redis의 `INCR` 명령어를 통해 요청 횟수를 카운팅했습니다.
*   **정책**: **1일 기준 5회를 초과**하여 요청할 경우, 추가적인 발송을 차단하여 시스템을 보호했습니다.

이를 통해 무분별한 API 호출을 사전에 차단함으로써 **불필요한 리소스 낭비를 막고, 예상치 못한 비용 발생을 절감**하여 리스크를 최소화하였습니다.

### 인증 번호 검증 로직

사용자가 입력한 인증 번호를 검증하는 프로세스는 다음과 같습니다.

1.  **요청 수신**: 클라이언트로부터 `수신자 정보(이메일/전화번호)`와 `입력한 인증 번호`를 전달받습니다.
2.  **Redis 조회**: 수신자 정보를 Key로 하여 Redis에 저장된 인증 번호를 조회합니다 (`GET`).
3.  **검증**:
    *   **만료됨 (Null)**: Redis에 데이터가 없다면 유효 시간이 만료된 것으로 간주하여 실패 처리합니다.
    *   **불일치**: 저장된 값과 입력값이 다르면 실패 처리합니다.
    *   **일치**: 값이 일치하면 인증 성공으로 처리하고, 보안을 위해 해당 Key를 즉시 삭제(`DEL`)하여 재사용을 방지합니다.

## 인프라 구성 및 Lambda 도입 이유

구현된 인증 로직은 AWS Lambda 함수로 배포하였으며, **AWS API Gateway**와 연동하여 REST API 엔드포인트를 생성해 클라이언트에서 호출하도록 구성했습니다.

### 왜 Lambda(Serverless)를 선택했는가?

이번 프로젝트에서 별도의 서버 구축 대신 **Serverless 아키텍처**를 선택한 이유는 다음과 같습니다.

1.  **관리 포인트 최소화 (NoOps)**: 트래픽에 따라 자동으로 스케일링되므로 서버 용량 산정이나 인프라 관리에 신경 쓸 필요가 없었습니다.
2.  **비용 효율성**: 이벤트 기간에만 트래픽이 몰리고 평소에는 호출이 거의 없는 서비스 특성상, 24시간 서버를 띄워두는 것보다 **사용량(요청 횟수 및 실행 시간)만큼만 과금**되는 방식이 훨씬 경제적이었습니다.
3.  **빠른 개발 및 배포**: 프로젝트 일정이 매우 타이트했기 때문에, 인프라 세팅에 시간을 쏟기보다 비즈니스 로직 구현에 집중하고 빠르게 배포할 수 있는 환경이 필요했습니다.
