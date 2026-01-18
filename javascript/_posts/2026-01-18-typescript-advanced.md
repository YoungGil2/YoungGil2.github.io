---
layout: post
title: "TypeScript 유틸리티 타입 정리"
date: 2026-01-18 13:00:00 +0900
description: >
  TypeScript의 강력한 기능인 제네릭과 유틸리티 타입에 대해 깊이 있게 다룹니다.
hide_description: true
lastmod: 2026-01-18 13:00:00 +0900
sitemap:
  changefreq: weekly
tags:
  - typescript
  - generics
  - utility types
  - frontend
---

0. 
{:toc}

이전 포스팅에서 TypeScript의 기본 개념과 장단점에 대해 다뤘다면, 이번에는 실무에서 TypeScript를 더욱 강력하게 활용할 수 있게 해주는 **제네릭(Generics)**과 **유틸리티 타입(Utility Types)**에 대해 알아보겠습니다.

## 제네릭 (Generics)

제네릭은 어떠한 클래스나 함수에서 사용할 타입을 그 함수나 클래스를 사용할 때 결정하는 프로그래밍 기법입니다.
즉, 타입을 마치 함수의 파라미터처럼 받아서 사용하는 것이라고 이해하면 쉽습니다.

### 왜 사용하는가?

제네릭을 사용하면 **재사용성**이 높은 컴포넌트를 만들 수 있습니다.
여러 가지 타입에서 동작하는 컴포넌트를 생성할 때, `any` 타입을 쓰면 타입 안전성을 잃게 되지만, 제네릭을 쓰면 타입을 보장받으면서도 유연하게 사용할 수 있습니다.

### 사용 예시

```typescript
function getSize<T>(arr: T[]): number {
  return arr.length;
}

const arr1 = [1, 2, 3];
getSize<number>(arr1); // 3

const arr2 = ["a", "b", "c"];
getSize<string>(arr2); // 3
```

`<T>`는 Type의 약자로 관용적으로 사용되며, 함수를 호출할 때 타입을 명시해줍니다.

## 유틸리티 타입 (Utility Types)

TypeScript는 일반적인 타입 변환을 쉽게 하기 위해 몇 가지 유틸리티 타입을 전역으로 제공합니다.
이를 활용하면 기존에 정의된 타입을 바탕으로 새로운 타입을 쉽게 정의할 수 있어 코드의 중복을 줄일 수 있습니다.

### Partial<T>

`T`의 모든 프로퍼티를 선택적(optional)으로 만듭니다.

```typescript
interface Todo {
  title: string;
  description: string;
}

function updateTodo(todo: Todo, fieldsToUpdate: Partial<Todo>) {
  return { ...todo, ...fieldsToUpdate };
}
```

### Pick<T, K>

`T`에서 프로퍼티 `K`의 집합을 선택해 타입을 구성합니다.

```typescript
interface Todo {
  title: string;
  description: string;
  completed: boolean;
}

type TodoPreview = Pick<Todo, "title" | "completed">;

const todo: TodoPreview = {
  title: "Clean room",
  completed: false,
};
```

### Omit<T, K>

`T`에서 모든 프로퍼티를 선택한 다음 `K`를 제거합니다. `Pick`의 반대 개념입니다.

```typescript
interface Todo {
  title: string;
  description: string;
  completed: boolean;
}

type TodoPreview = Omit<Todo, "description">;

const todo: TodoPreview = {
  title: "Clean room",
  completed: false,
};
```

### Record<K, T>

프로퍼티 키가 `K`이고 값이 `T`인 객체 타입을 구성합니다.

```typescript
interface PageInfo {
  title: string;
}

type Page = "home" | "about" | "contact";

const nav: Record<Page, PageInfo> = {
  home: { title: "Home" },
  about: { title: "About" },
  contact: { title: "Contact" },
};
```

## 마무리

제네릭과 유틸리티 타입을 적절히 활용하면, 타입 정의에 들어가는 시간을 줄이고 코드의 가독성과 유지보수성을 크게 높일 수 있습니다.
처음에는 복잡해 보일 수 있지만, 익숙해지면 TypeScript 개발의 필수적인 도구가 될 것입니다.
