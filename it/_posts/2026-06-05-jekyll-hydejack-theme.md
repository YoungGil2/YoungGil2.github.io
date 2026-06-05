---
layout: post
title: 'Jekyll 블로그에 Hydejack 테마 적용하기: 나만의 블로그 꾸미기'
date: 2026-06-05 12:00:00 +0900
description: >
  Jekyll 블로그에 Hydejack 테마를 적용하고 사이드바, 메뉴, 파비콘 등을 설정하는 방법을 정리했습니다.
category: IT
hide_description: true
lastmod: 2026-06-05 12:00:00 +0900
sitemap:
  changefreq: weekly
tags:
  - Jekyll
  - Hydejack
  - GitHub Pages
  - 개발 블로그
  - 블로그 테마
keywords: 'Jekyll 테마, Hydejack 테마 적용, Jekyll 블로그 꾸미기, GitHub Pages 테마, Hydejack 설정'
---

Jekyll, Hydejack, GitHub Pages
{:.note title="사용"}

지난 글에서 Jekyll + GitHub Pages로 블로그를 만드는 기본 과정을 다뤘습니다. 이번에는 테마를 적용해서 블로그를 꾸며보겠습니다.

[이전 글: GitHub Pages + Jekyll로 블로그 만들기](/it/2026-06-01-jekyll-github-pages/){:target="\_blank"}
{:.note title="이전 글"}

## 테마 선택

Jekyll은 기본 테마(Minima)가 있지만 솔직히 좀 심심합니다. 다양한 오픈소스 테마들이 있는데, 저는 그 중에서 **Hydejack**을 선택했습니다.

깔끔하고 사이드바 기반의 레이아웃이 보기 좋았습니다. 카테고리 목록이 사이드에 딱 정리되는 구조가 개발 블로그에 잘 어울린다고 생각했습니다.

[Hydejack 테마 보러가기](https://hydejack.com/){:target="\_blank"}
{:.note title="링크"}

무료 버전과 PRO 버전이 있는데, 무료 버전으로도 충분합니다.

## 테마 설치

Hydejack을 적용하는 가장 간단한 방법은 **GitHub 릴리즈에서 소스코드를 직접 다운받는 것**입니다.

[Hydejack GitHub Releases](https://github.com/hydecorp/hydejack/releases){:target="\_blank"}
{:.note title="링크"}

릴리즈 페이지에서 최신 버전의 `Source code (zip)`을 다운받아 압축을 풀면, 바로 쓸 수 있는 프로젝트 구조가 들어있습니다. `jekyll new`로 빈 프로젝트를 만들고 테마를 나중에 얹는 것보다 훨씬 빠르게 시작할 수 있습니다.

이후 패키지를 설치하고 로컬 서버를 띄우면 됩니다.

```bash
bundle install
bundle exec jekyll serve
```

GitHub Pages에 배포할 때는 `_config.yml`에서 `theme`을 주석 처리하고 `remote_theme`으로 바꿔야 합니다. GitHub Pages는 외부 gem 테마를 직접 지원하지 않기 때문입니다.

```yml
# 로컬 개발 시
theme: jekyll-theme-hydejack

# GitHub Pages 배포 시 (주석 교체)
remote_theme: hydecorp/hydejack@v9
```

## 기본 설정: \_config.yml

테마를 적용했으면 `_config.yml`에서 블로그 기본 정보를 설정합니다.

```yml
title: Younggil Kim # 사이드바 상단에 표시되는 블로그 이름
tagline: 개발하며 습득한 지식을 정리하는 곳 # 짧은 소개 문구
description: >
  프론트엔드(React, Vue)와 백엔드(PHP, Laravel) 개발 경험과 지식을 공유하는 기술 블로그입니다.
# 메타 설명 (SEO용)
url: https://younggil2.github.io
```

## 사이드바 이미지 설정

사이드바 배경 이미지와 색상을 설정합니다.

```yml
accent_image: /assets/img/sidebar-bg.jpg # 사이드바 배경 이미지
accent_color: rgb(79,177,186) # 포인트 컬러
theme_color: rgb(25,55,71) # 브라우저 UI 색상
```

이미지는 `assets/img/` 폴더에 넣어두고 경로를 지정하면 됩니다.

## 로고 및 파비콘 설정

사이드바 상단에 표시되는 로고와 브라우저 탭의 파비콘을 설정합니다.

```yml
logo: /assets/img/me/profile.jpg # 사이드바 상단 로고 이미지
favicon: /assets/img/me/favicon.ico # 브라우저 탭 아이콘
apple_touch_icon: /assets/img/me/profile.jpg
```

파비콘은 `.ico` 형식이 가장 호환성이 좋습니다. PNG를 변환할 때는 [favicon.io](https://favicon.io/){:target="\_blank"} 같은 온라인 도구를 활용하면 편합니다.

## 사이드바 메뉴 구성

메뉴는 `_config.yml`의 `menu` 항목에서 관리합니다.

```yml
menu:
  - title: JavaScript
    url: /javascript/
  - title: IT
    url: /it/
  - title: Resume
    url: /resume/
  - title: About
    url: /about/
```

카테고리별로 `url`을 지정하면 사이드바에 메뉴가 생깁니다. 순서도 여기서 조정할 수 있습니다.

## 작성자 정보 설정

`_data/authors.yml` 파일에서 블로그 작성자 정보를 설정합니다. 사이드바 하단과 포스팅 하단의 about 카드에 표시됩니다.

```yml
author1:
  name: 이름
  email: 이메일
  about: |
    안녕하세요. 블로그 소개를 작성합니다.
  picture:
    srcset:
      1x: /assets/img/me/profile.jpg
      2x: /assets/img/me/profile.jpg
  social:
    github: { username }
```

## 마치며

Jekyll + Hydejack 조합으로 블로그를 만들어봤습니다. 설치와 기본 설정만 해도 꽤 그럴듯한 블로그가 완성됩니다.

블로그 구축 관련 글은 이번으로 마무리하고, 앞으로는 개발하면서 배운 내용들을 꾸준히 채워나갈 예정입니다.
