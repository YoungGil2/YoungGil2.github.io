---
layout: post
title: 'GitHub 프로필 꾸미기: 나만의 개발자 포트폴리오 대문 만들기'
date: 2026-01-26 18:00:00 +0900
description: >
  GitHub Profile README 기능을 활용하여 프로필을 개성 있게 꾸미는 방법과 유용한 툴들을 정리했습니다.
  GitHub 배지, 아이콘 등을 활용해 매력적인 프로필을 만들어보세요.
category: IT
hide_description: true
tags:
  [
    GitHub Profile,
    Developer Portfolio,
    Markdown,
    GitHub Stats,
    Badges,
    Customization,
  ]
sitemap: true
---

개발자에게 GitHub 프로필은 온라인상의 이력서이자 자신의 정체성을 나타내는 포트폴리오와 같습니다.
그동안 바쁘다는 핑계로 꾸며보지 못했던 GitHub 프로필을 정리하며, 개발자로서의 첫인상을 멋지게 꾸며보기로 했습니다.

이 글에서는 GitHub의 **Profile README** 기능을 활용해 프로필을 꾸미는 방법과, 제가 사용하며 유용하다고 느꼈던 몇 가지 도구들을 공유하고자 합니다.

## 1. GitHub Profile README란?

GitHub 사용자 이름과 동일한 이름의 Public Repository를 생성하면, 해당 저장소의 `README.md` 파일이 내 프로필 전면에 노출되는 기능입니다. Markdown을 활용해 자유롭게 나를 소개하는 공간으로 사용할 수 있습니다.

![GitHub Profile 예시](/assets/img/it/img_github_profile.png){:style="border: 1px solid #ccc;"}

### 설정 방법

1.  GitHub에서 **New Repository** 버튼을 클릭합니다.
2.  Repository name에 자신의 **GitHub Username**을 정확히 입력합니다. (예: `YoungGil2/YoungGil2`)
3.  저장소를 **Public**으로 설정하고, **"Add a README file"** 옵션을 체크합니다.
4.  생성된 `README.md` 파일을 수정하면 프로필에 실시간으로 반영됩니다.

## 2. 프로필 꾸미기 추천 도구

Markdown 문법만으로도 충분히 멋진 프로필을 만들 수 있지만, 몇 가지 오픈소스 도구를 활용하면 훨씬 다채롭고 정보가 풍부한 프로필을 구성할 수 있습니다.

### 기술 스택 배지 (Shields.io & Simple Icons)

가장 많이 사용되는 꾸미기 요소는 바로 기술 스택 배지입니다.

- **[Shields.io](https://shields.io/){:target="\_blank"}**: 다양한 종류의 정적 배지를 생성해 주는 서비스입니다.
- **[Simple Icons](https://simpleicons.org/){:target="\_blank"}**: 기술 스택, 서비스 등의 로고와 공식 색상 코드를 찾아볼 수 있습니다.

`Shields.io`를 통해 배지 스타일을 만들고, `Simple Icons`에서 찾은 로고 이름과 색상 코드를 조합하여 아래와 같이 나만의 기술 배지를 만들 수 있습니다.

![Simple Icons 웹사이트 스크린샷](/assets/img/it/img_simple_icons.png){:style="border: 1px solid #ccc;"}

#### 사용법

아래 형식에 맞춰 URL을 작성하면 배지 이미지가 생성됩니다.

```markdown
<img src="https://img.shields.io/badge/{뱃지에 표시할 텍스트}-{배경 색상}?style={스타일}&logo={로고 이름}&logoColor={로고 색상}">

<!-- 예: PHP 배지 -->
<img src="https://img.shields.io/badge/php-777BB4?style=for-the-badge&logo=php&logoColor=white">
```

이렇게 만든 배지를 적용하면 프로필이 조금 더 정성이 담겨져보입니다. 그동안 너무 관리 안한거 같네요..ㅎ

![적용된 배지 예시](/assets/img/it/img_badge.png){:style="border: 1px solid #ccc;"}

또한, **[Awesome Badges](https://github.com/envoy1084/awesome-badges){:target="\_blank"}** 저장소에는 자주 사용되는 유용한 배지 코드들이 잘 정리되어 있어, 이를 참고하거나 복사하여 사용하는 것도 좋은 방법입니다.

## 3. 블로그 최신 글 자동 목록화 (Blog Post Workflow)

GitHub Actions를 활용하면 자신의 블로그 최신 글을 받아와 프로필 README에 자동으로 목록을 만들어 줄 수 있습니다. 이를 위해 가장 널리 쓰이는 도구는 [gautamkrishnar/blog-post-workflow](https://github.com/gautamkrishnar/blog-post-workflow){:target="\_blank"}입니다.

### 적용 방법

아래 단계를 따라하면 프로필에 최신 글 목록을 손쉽게 추가할 수 있습니다.

**1. 워크플로우 권한 설정**

가장 먼저, 워크플로우가 Repository의 파일을 수정할 수 있도록 쓰기 권한을 부여해야 합니다.

1.  프로필 Repository의 **Settings** > **Actions** > **General** 메뉴로 이동합니다.
2.  **Workflow permissions** 섹션을 찾아 **Read and write permissions** 옵션을 선택하고 저장합니다.

**2. 워크플로우 파일 생성 및 설정**

프로필 Repository에 `.github/workflows/` 디렉토리를 생성한 후, 그 안에 `blog-post-workflow.yml`과 같은 이름으로 파일을 만들고 아래 코드를 붙여넣습니다.

```yml
name: Latest blog post workflow
on:
  schedule:
    # 매일 자정(UTC)에 워크플로우를 실행합니다.
    # 리눅스 cron 문법과 동일하며, '0 0 * * *'는 매일 00:00 UTC를 의미합니다.
    - cron: '0 0 * * *'
  workflow_dispatch:
    # GitHub Actions 페이지에서 수동으로 실행할 수 있게 합니다.

# 워크플로우가 README 파일을 수정할 수 있도록 권한을 부여합니다.
permissions:
  contents: write

jobs:
  update-readme-with-blog:
    name: Update this repo's README with latest blog posts
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Pull in blog posts
        uses: gautamkrishnar/blog-post-workflow@v1
        with:
          # 블로그의 RSS 피드 주소를 입력합니다.
          feed_list: 'https://younggil2.github.io/feed.xml'
```

**3. README.md에 목록 위치 지정**

워크플로우가 글 목록을 추가할 위치를 `README.md` 파일에 지정해주어야 합니다. 목록을 표시하고 싶은 곳에 아래와 같이 주석으로 된 시작점과 끝점을 추가합니다.

```markdown
<!-- BLOG-POST-LIST:START -->
<!-- 이 주석 사이에 최신 글 목록이 자동으로 추가됩니다. -->
<!-- BLOG-POST-LIST:END -->
```

**4. 실행 및 확인**

이제 모든 설정이 완료되었습니다. 설정한 `cron` 주기에 맞춰 워크플로우가 자동으로 실행되지만, 즉시 확인하고 싶다면 아래 방법으로 수동 실행할 수 있습니다.

1.  Repository의 **Actions** 탭으로 이동합니다.
2.  왼쪽 사이드바에서 **Latest blog post workflow**를 선택합니다.
3.  **Run workflow** 드롭다운 버튼을 클릭하여 워크플로우를 실행합니다.

![액션 워크플로우 수동 실행](/assets/img/it/img_workflows.png){:style="border: 1px solid #ccc;"}

잠시 후 워크플로우 실행이 완료되면, 자신의 프로필 README에 블로그 최신 글 목록이 아래와 같이 표시되는 것을 확인할 수 있습니다.

![블로그 최신글 프로필 업데이트 결과](/assets/img/it/img_github_profile_blog.png){:style="border: 1px solid #ccc;"}

## 마치며..

이렇게 프로필을 꾸며보았는데요. 깃허브를 정비하며, '관리하는 즐거움'을 오랜만에 느꼈습니다.<br/>
그리고 자동화된 파이프라인을 통해 블로그 새 글이 프로필에 업데이트되는 모습을 보니 조금 더 자주 개발 블로그를 포스팅해야겠다는 생각이네요. 허허
