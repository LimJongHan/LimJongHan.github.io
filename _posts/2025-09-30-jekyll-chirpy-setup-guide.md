---
layout: post
title: "Jekyll Chirpy 테마 설정 가이드"
date: 2025-09-30 18:30:00 +0900
categories: [학습]
tags: [Jekyll, Chirpy, GitHub Pages, 블로그]
author: LimJongHan
---

# Jekyll Chirpy 테마 설정하기

Jekyll Chirpy 테마를 사용하여 블로그를 설정하는 과정을 정리해보겠습니다.

## 1. 사전 준비

### 필요한 도구들
- **Ruby**: 3.1 이상 버전
- **Bundler**: Ruby gem 관리 도구
- **Git**: 버전 관리 시스템

### Ruby 설치 (macOS)
```bash
# rbenv 설치
curl -fsSL https://github.com/rbenv/rbenv-installer/raw/HEAD/bin/rbenv-installer | bash

# Ruby 3.1.4 설치
rbenv install 3.1.4
rbenv local 3.1.4
```

## 2. Chirpy 테마 다운로드

```bash
# Chirpy starter 저장소 클론
git clone https://github.com/cotes2020/chirpy-starter.git my-blog
cd my-blog
```

## 3. 의존성 설치

```bash
# Gemfile의 의존성 설치
bundle install
```

## 4. 설정 파일 수정

`_config.yml` 파일에서 다음 항목들을 수정합니다:

```yaml
# 사이트 기본 정보
title: "당신의 블로그 제목"
tagline: "블로그 부제목"
description: "블로그 설명"

# URL 설정
url: "https://yourusername.github.io"

# 소셜 정보
github:
  username: your_github_username

social:
  name: "당신의 이름"
  email: "your@email.com"
  links:
    - https://github.com/yourusername
```

## 5. 로컬 서버 실행

```bash
# Jekyll 서버 시작
bundle exec jekyll serve --livereload
```

브라우저에서 `http://localhost:4000`으로 접속하여 확인할 수 있습니다.

## 6. GitHub Pages 배포

```bash
# 원격 저장소 설정
git remote set-url origin https://github.com/yourusername/yourusername.github.io.git

# 변경사항 커밋 및 푸시
git add .
git commit -m "Initial blog setup"
git push origin main
```

## 주요 특징

### Chirpy 테마의 장점
- 🎨 **모던한 디자인**: 깔끔하고 반응형 디자인
- 🌙 **다크/라이트 모드**: 사용자 선호에 따른 테마 전환
- 📱 **모바일 친화적**: 모든 기기에서 최적화된 경험
- 🔍 **검색 기능**: 내장된 검색 기능
- 📊 **통계**: 페이지 뷰 및 방문자 통계
- 💬 **댓글 시스템**: Disqus, Utterances, Giscus 지원

### 지원하는 기능들
- 카테고리 및 태그 시스템
- 아카이브 페이지
- RSS 피드
- PWA (Progressive Web App) 지원
- 소셜 미디어 공유

## 마무리

Jekyll Chirpy 테마는 정적 블로그를 만들기에 매우 좋은 선택입니다. GitHub Pages와 함께 사용하면 무료로 전문적인 블로그를 운영할 수 있습니다.

더 자세한 정보는 [Chirpy 공식 문서](https://github.com/cotes2020/jekyll-theme-chirpy)를 참고하세요.

---

*이 포스트는 Jekyll Chirpy 테마 설정 과정을 정리한 가이드입니다.*
