---
layout: post
title: "Python datetime 라이브러리 마스터하기 - 기초부터 실전까지"
date: 2025-10-02 21:30:00 +0900
categories: [학습, Python]
tags: [Python, datetime, 날짜시간, 표준라이브러리, 프로그래밍]
author: LimJongHan
toc: true
---
# 📖 관련된 내 포스팅

# 📝 내용

## 개요

Python의 `datetime` 라이브러리는 날짜와 시간을 다루기 위한 내장 라이브러리입니다. 이 라이브러리는 날짜와 시간의 생성, 조작, 연산 및 포맷팅과 관련된 다양한 클래스와 함수를 제공합니다. `datetime`은 프로그램에서 시간에 기반한 기능을 구현하는 데 필요한 광범위한 도구를 갖추고 있어, 시간 데이터를 다루는 다양한 애플리케이션에 매우 유용합니다.

## 핵심 내용

### 주요 특징

#### 1. 날짜와 시간 처리
`datetime` 라이브러리는 다음과 같은 핵심 클래스들을 제공합니다:

- **`date`**: 날짜만을 다루는 클래스 (년, 월, 일)
- **`time`**: 시간만을 다루는 클래스 (시, 분, 초, 마이크로초)
- **`datetime`**: 날짜와 시간을 함께 다루는 클래스
- **`timedelta`**: 시간 간격을 나타내는 클래스

#### 2. 시간대 인식
`datetime`은 시간대를 인식하고 처리할 수 있는 기능을 제공합니다. 이를 통해 다양한 시간대의 시간을 처리하고 변환할 수 있습니다.

#### 3. 편리한 날짜/시간 연산
두 날짜/시간 간의 차이를 계산하거나, 특정 기간을 더하고 빼는 것과 같은 연산을 쉽게 수행할 수 있습니다.

#### 4. 다양한 포맷팅과 파싱
날짜와 시간을 문자열로 변환하거나, 문자열에서 날짜와 시간을 파싱하는 기능을 제공합니다.

### 실용적인 예제

```python
import datetime

# 현재 날짜와 시간
now = datetime.datetime.now()
print("Current date and time:", now)
# 출력: Current date and time: 2025-01-15 14:30:00.123456

# 특정 날짜 생성
some_date = datetime.date(2020, 1, 30)
print("Specific date:", some_date)
# 출력: Specific date: 2020-01-30

# 날짜와 시간 간격 계산 (timedelta)
ten_days_later = now + datetime.timedelta(days=10)
print("Ten days from now:", ten_days_later)
# 출력: Ten days from now: 2025-01-25 14:30:00.123456

# 날짜 포맷팅
formatted_date = now.strftime("%Y-%m-%d %H:%M:%S")
print("Formatted date and time:", formatted_date)
# 출력: Formatted date and time: 2025-01-15 14:30:00

# 문자열로부터 날짜 파싱
date_from_string = datetime.datetime.strptime("2024-01-15", "%Y-%m-%d")
print("Parsed date from string:", date_from_string)
# 출력: Parsed date from string: 2024-01-15 00:00:00

# 시간 차이 계산
birth_date = datetime.date(1990, 5, 15)
today = datetime.date.today()
age = today - birth_date
print(f"Age in days: {age.days}")
print(f"Age in years: {age.days // 365}")

# 특정 요일 찾기
import calendar
weekday_name = calendar.day_name[birth_date.weekday()]
print(f"Born on: {weekday_name}")
```

### 고급 활용 예제

```python
# 시간대 처리
from datetime import timezone, timedelta

# UTC 시간
utc_now = datetime.datetime.now(timezone.utc)
print("UTC time:", utc_now)

# 한국 시간 (UTC+9)
korea_tz = timezone(timedelta(hours=9))
korea_time = utc_now.astimezone(korea_tz)
print("Korea time:", korea_time)

# 월말 날짜 계산
def get_last_day_of_month(year, month):
    if month == 12:
        next_month = datetime.date(year + 1, 1, 1)
    else:
        next_month = datetime.date(year, month + 1, 1)
    return next_month - datetime.timedelta(days=1)

last_day = get_last_day_of_month(2025, 2)
print("Last day of February 2025:", last_day)
# 출력: Last day of February 2025: 2025-02-28

# 주간 범위 계산
def get_week_range(date):
    start_of_week = date - datetime.timedelta(days=date.weekday())
    end_of_week = start_of_week + datetime.timedelta(days=6)
    return start_of_week, end_of_week

today = datetime.date.today()
week_start, week_end = get_week_range(today)
print(f"Week range: {week_start} to {week_end}")
```

## 정리

### 활용처

1. **시간 기반 기능**: 웹 애플리케이션, 데이터베이스 관리 시스템, 로그 관리 시스템 등에서 시간 데이터를 저장, 처리 및 표시하는 데 사용됩니다.

2. **일정 관리 및 예약 시스템**: 회의, 예약, 이벤트 관리 등의 기능을 구현하는 데 사용됩니다.

3. **데이터 분석 및 보고서 작성**: 시간에 따른 데이터 추세 분석, 시계열 데이터 처리, 보고서의 타임스탬프 생성 등에 사용됩니다.

4. **시스템 모니터링**: 로그 파일의 타임스탬프 처리, 시스템 성능 측정, 백업 스케줄링 등에 활용됩니다.

### 핵심 포인트

- `datetime`은 Python의 표준 라이브러리로 별도 설치 없이 사용 가능
- 날짜, 시간, datetime, timedelta 클래스를 통한 유연한 시간 처리
- 시간대 인식 기능으로 글로벌 애플리케이션 개발 지원
- 풍부한 포맷팅과 파싱 기능으로 다양한 형태의 시간 데이터 처리 가능

`datetime` 라이브러리는 Python에서 날짜와 시간을 다루는 데 필수적인 도구입니다. 이 라이브러리의 유연성과 다양한 기능은 시간 관련 데이터의 처리와 분석을 간단하고 효율적으로 만들어 줍니다. 사용자 친화적인 API와 다양한 클래스들은 개발자가 복잡한 날짜 및 시간 연산을 쉽게 수행할 수 있도록 하며, 이는 파이썬을 시간 관련 애플리케이션 개발에 적합한 언어로 만듭니다.

# 📚 레퍼런스
- [Python 공식 문서 - datetime](https://docs.python.org/ko/3/library/datetime.html)
- [함께해요 파이썬 생태계 - datetime](https://wikidocs.net/227592)

---
*이 포스트는 Python 학습 과정을 정리한 내용입니다.*
