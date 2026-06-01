---
layout: post
title: 'GitHub Pages + Jekyll로 블로그 만들기: 나만의 개발 공간 직접 구축하기'
date: 2026-06-01 12:00:00 +0900
description: >
  GitHub Pages와 Jekyll을 활용해 무료로 개발 블로그를 만드는 방법을 정리했습니다.
  Ruby 설치부터 로컬 개발 환경 구성, GitHub Pages 배포까지 전체 과정을 다룹니다.
category: IT
hide_description: true
lastmod: 2026-06-01 12:00:00 +0900
sitemap:
  changefreq: weekly
tags:
  - Jekyll
  - GitHub Pages
  - 개발 블로그
  - Ruby
  - 정적 사이트
keywords: 'Jekyll 블로그 만들기, GitHub Pages 블로그, Jekyll 설치, 개발 블로그 만들기, 무료 블로그, 정적 사이트 생성기'
---

Ruby, Jekyll, GitHub Pages
{:.note title="사용"}

## GitHub Pages + Jekyll을 선택한 이유

개발 블로그를 시작하려고 보면 선택지가 꽤 됩니다. 쉽게 시작할 수 있는 플랫폼들도 있지만, 온전히 내 것인 공간을 직접 만들어보고 싶었습니다.

**GitHub Pages + Jekyll**을 선택한 이유는 세 가지입니다.

- **무료**: 호스팅 비용이 없습니다. 도메인도 `username.github.io` 형태로 기본 제공됩니다.
- **커스터마이징**: 테마부터 레이아웃까지 자유롭게 수정할 수 있습니다.
- **GitHub 연동**: `git push`만 하면 자동 배포됩니다. 별도의 배포 과정이 없습니다.

## Jekyll이란?

Jekyll은 Markdown 파일을 HTML 정적 사이트로 변환해주는 Ruby 기반의 정적 사이트 생성기입니다. GitHub Pages가 공식적으로 지원하기 때문에 별도 빌드 서버 없이 GitHub에서 바로 빌드 및 배포가 됩니다.

## 1. GitHub Repository 생성

먼저 GitHub에서 블로그용 Repository를 생성합니다.

Repository 이름은 반드시 `{username}.github.io` 형식이어야 합니다.

- Username이 `younggil2`라면 → `younggil2.github.io`
- Public으로 설정합니다.

이 이름 규칙을 따르면 `https://{username}.github.io` 주소로 배포됩니다.

Repository 생성 후 GitHub Pages를 활성화해야 합니다.

1. Repository의 **Settings** 탭으로 이동합니다.
2. 왼쪽 사이드바에서 **Pages**를 선택합니다.
3. **Source**를 `Deploy from a branch`로 설정하고, Branch는 `main`, `/(root)`로 선택 후 저장합니다.

![GitHub Pages 설정](/assets/img/it/img_github_pages_setting.png){:style="border: 1px solid #e5e7eb; border-radius: 8px;"}

설정이 완료되면 push 시 자동으로 빌드 및 배포됩니다.

## 2. Ruby 설치

Jekyll은 Ruby로 만들어졌기 때문에 Ruby를 먼저 설치해야 합니다.

Mac에는 기본 Ruby가 설치되어 있지만, 버전 관리를 위해 **rbenv**를 사용하는 것을 권장합니다.

```bash
# Homebrew로 rbenv 설치
brew install rbenv ruby-build

# 셸 초기화 파일에 rbenv 설정 추가 (.zshrc 또는 .bash_profile)
echo 'eval "$(rbenv init -)"' >> ~/.zshrc
source ~/.zshrc

# Ruby 설치 (버전은 Jekyll 공식 문서에서 권장하는 버전 확인)
rbenv install 3.2.2
rbenv global 3.2.2

# 설치 확인
ruby -v
```

## 3. Jekyll 설치

Ruby 설치가 완료되면 Jekyll과 Bundler를 설치합니다.

```bash
gem install jekyll bundler
```

설치 확인:

```bash
jekyll -v
```

## 4. Jekyll 프로젝트 생성

```bash
jekyll new my-blog
cd my-blog
```

생성된 구조는 다음과 같습니다.

```
my-blog/
├── _config.yml      # 블로그 기본 설정
├── _posts/          # 포스팅 파일 (.md)
├── _site/           # 빌드 결과물 (git에 올리지 않음)
├── Gemfile          # Ruby 의존성
└── index.md
```

## 5. 로컬 서버 실행

```bash
bundle exec jekyll serve
```

`http://localhost:4000` 에서 블로그를 미리 확인할 수 있습니다. 파일을 수정하면 자동으로 반영됩니다.

## 6. 포스팅 작성

`_posts` 폴더 안에 Markdown 파일을 생성합니다. 파일명은 반드시 `YYYY-MM-DD-제목.md` 형식이어야 합니다.

```markdown
---
layout: post
title: '첫 번째 포스팅'
date: 2026-06-01 12:00:00 +0900
---

여기에 내용을 작성합니다.
```

파일 최상단의 `---` 사이 영역을 **Front Matter**라고 하며, 레이아웃, 제목, 날짜 등 메타데이터를 정의합니다.

## 7. GitHub Pages 배포

로컬에서 확인이 끝나면 GitHub에 push합니다.

```bash
git init
git remote add origin https://github.com/{username}/{username}.github.io.git
git add .
git commit -m "init blog"
git push -u origin main
```

push가 완료되면 잠시 후 `https://{username}.github.io` 에서 블로그가 열립니다.

이후부터는 포스팅을 작성하고 push하는 것만으로 자동 배포됩니다.

```bash
git add .
git commit -m "add new post"
git push
```

## 마치며

GitHub Pages + Jekyll 조합은 설정이 간단하고, 유지 비용이 없으며, 마음대로 꾸밀 수 있다는 점에서 개발자 블로그로 최적의 선택 중 하나라고 생각합니다.

기본 테마가 다소 밋밋하게 느껴진다면, Hydejack, Minimal Mistakes, Chirpy 같은 오픈소스 테마를 적용해보는 것도 좋습니다. 테마 적용 방법은 다음 포스팅에서 다뤄보겠습니다.
