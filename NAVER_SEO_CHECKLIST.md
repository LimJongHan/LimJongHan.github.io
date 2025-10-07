# 네이버 검색엔진 최적화 체크리스트

이 문서는 네이버 웹마스터 도구 가이드를 기반으로 작성되었습니다.

## ✅ 완료된 항목

### 1. 웹마스터도구 사이트 등록 및 소유 확인
- [x] 네이버 웹마스터도구에 사이트 등록 (`https://limjonghan.github.io`)
- [x] HTML 파일 업로드로 소유 확인 (`naver33258d49dd92d01bed3c9a9b63714f6b.html`)

### 2. 검색로봇 접근 허용
- [x] `robots.txt` 파일 생성 및 설정
- [x] 네이버 검색로봇(Yeti) 허용 설정
- [x] 모든 검색엔진 접근 허용
- [x] Sitemap 위치 명시

**robots.txt 내용:**
```
User-agent: *
Allow: /

# Naverbot (네이버 검색엔진)
User-agent: Yeti
Allow: /

Sitemap: https://limjonghan.github.io/sitemap.xml
```

### 3. 중복 콘텐츠 관리
- [x] 페이지별 고유한 제목 설정
- [x] Front Matter에 title 명시
- [x] 메타 설명(description) 추가

### 4. 사이트맵 및 RSS 제출
- [x] XML 사이트맵 자동 생성 (`sitemap.xml`)
- [x] 올바른 URL 형식 (https://limjonghan.github.io)
- [x] RSS 피드 자동 생성 (`feed.xml`)
- [ ] 웹마스터도구에 사이트맵 제출 필요
- [ ] 웹마스터도구에 RSS 제출 필요

### 5. HTML 마크업 최적화
- [x] 표준 HTML5 마크업 사용
- [x] SEO 메타 태그 (jekyll-seo-tag)
- [x] Open Graph 태그
- [x] Twitter Cards
- [x] 구조화된 데이터 (JSON-LD)
- [x] noindex 태그 없음

### 6. 페이지 구조 최적화
- [x] frame 태그 미사용
- [x] 자바스크립트 redirect 미사용
- [x] 표준 HTML 링크 사용 (`<a href="...">`)
- [x] HTTP redirect 방식 사용

## 📋 진행해야 할 항목

### 1. 웹마스터도구에서 직접 수행해야 할 작업

#### A. 사이트맵 제출
1. [네이버 웹마스터도구](https://searchadvisor.naver.com/) 로그인
2. "요청 > 사이트맵 제출" 메뉴 클릭
3. 사이트맵 URL 입력: `https://limjonghan.github.io/sitemap.xml`
4. 제출 완료

#### B. RSS 제출
1. "요청 > RSS 제출" 메뉴 클릭
2. RSS URL 입력: `https://limjonghan.github.io/feed.xml`
3. 제출 완료

#### C. 사이트 연관 채널 등록 (선택사항)
- GitHub: `https://github.com/LimJongHan`
- 기타 소셜 미디어 계정 추가 가능

### 2. 검색 결과 모니터링

#### 네이버 검색으로 확인
```
site:limjonghan.github.io
```

#### 웹마스터도구에서 확인
- "통계 > 검색 반영 현황"
- "통계 > 유입 키워드"
- "통계 > 노출 및 클릭"

### 3. 주요 체크 포인트

#### 사이트가 검색에 노출되지 않는 경우
- [ ] robots.txt 설정 재확인
- [ ] 방화벽에서 Yeti 차단 여부 확인
- [ ] noindex 메타 태그 확인
- [ ] 사이트맵 제출 완료 여부

#### 특정 페이지가 검색에 노출되지 않는 경우
- [ ] noindex 메타 태그 확인
- [ ] 자바스크립트로만 콘텐츠 로딩하는지 확인
- [ ] frame 태그 사용 여부 확인
- [ ] 페이지 내 링크 확인

## 🎯 권장 사항

### 1. 콘텐츠 최적화
- **제목 태그**: 페이지당 고유하고 명확한 제목 (50-60자)
- **메타 설명**: 페이지 내용을 정확히 설명 (150-160자)
- **키워드**: 자연스럽게 본문에 포함
- **제목 구조**: H1 > H2 > H3 계층 구조 유지

### 2. 링크 구조
- **내부 링크**: 관련 포스트 간 연결
- **표준 링크**: `<a href="...">` 사용
- **앵커 텍스트**: 명확하고 설명적인 텍스트

### 3. 이미지 최적화
- **alt 속성**: 모든 이미지에 설명 추가
- **파일명**: 의미 있는 이름 사용
- **크기 최적화**: 적절한 크기로 압축

### 4. 모바일 최적화
- **반응형 디자인**: 모든 화면 크기 대응
- **빠른 로딩**: 페이지 속도 최적화
- **터치 친화적**: 충분한 터치 영역

## 📊 SEO 성과 지표

### 주요 지표
- **검색 노출 수**: 검색 결과에 나타난 횟수
- **클릭 수**: 실제 사이트 방문 횟수
- **CTR**: 클릭률 (클릭 / 노출)
- **평균 순위**: 검색 결과에서의 평균 위치

### 목표 설정
- 초기 1개월: 사이트 인덱싱 완료
- 2-3개월: 주요 키워드 검색 결과 노출
- 6개월: 지속적인 트래픽 증가

## 🔧 기술적 세부사항

### 현재 설정 상태

#### _config.yml
```yaml
url: "https://limjonghan.github.io"
title: JongHan's Blog
tagline: AI, Data, Dev, Physics, etc.
description: AI, Data, Dev, Physics, etc.
```

#### sitemap.xml
- 자동 생성: jekyll-sitemap 플러그인
- 업데이트 주기: 포스트 발행 시 자동
- 우선순위: 0.5 (기본값)
- 변경 빈도: weekly

#### feed.xml
- 형식: Atom XML
- 최신 포스트 자동 포함
- 전체 콘텐츠 포함

## 📅 다음 단계

1. **즉시 수행**:
   - [ ] 웹마스터도구에 사이트맵 제출
   - [ ] 웹마스터도구에 RSS 제출
   - [ ] 검색 결과 확인 (1주일 후)

2. **정기적 수행**:
   - [ ] 월 1회: 검색 통계 확인
   - [ ] 주 1회: 새 포스트 발행
   - [ ] 분기 1회: SEO 성과 분석

3. **지속적 개선**:
   - [ ] 콘텐츠 품질 향상
   - [ ] 내부 링크 구조 개선
   - [ ] 페이지 속도 최적화
   - [ ] 사용자 경험 개선

## 📚 참고 자료

- [네이버 웹마스터 가이드](https://searchadvisor.naver.com/guide)
- [네이버 검색 최적화 가이드](https://searchadvisor.naver.com/guide/seo-basic-intro)
- [Jekyll SEO 태그 문서](https://github.com/jekyll/jekyll-seo-tag)
- [구조화된 데이터 가이드](https://developers.google.com/search/docs/advanced/structured-data/intro-structured-data)

---

*이 체크리스트는 정기적으로 업데이트하여 최신 SEO 권장사항을 반영합니다.*
*최종 업데이트: 2025-10-07*

