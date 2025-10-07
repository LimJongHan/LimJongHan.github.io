---
layout: post
title: "[자료구조] 해시 테이블(Hash Table) - 기본 예제"
date: 2025-10-07 15:50:00 +0900
categories: [자료구조, 해시테이블]
tags: [자료구조, 해시테이블, HashTable, Dictionary, 해시함수, 충돌해결, Python]
author: LimJongHan
toc: true
---

# 📖 관련된 내 포스팅
- [[자료구조] 자료구조와 알고리즘 개요]({% post_url 2025-10-02-[자료구조] 자료구조와 알고리즘 개요 %})

# 📝 내용

## 개요

해시 테이블(Hash Table)은 **키(key)와 값(value)의 쌍**으로 데이터를 저장하는 자료구조입니다. 해시 함수를 사용하여 키를 해시값으로 변환하고, 이 해시값을 배열의 인덱스로 사용하여 데이터를 저장하고 검색합니다. 평균적으로 **O(1)**의 시간복잡도로 데이터를 검색, 삽입, 삭제할 수 있어 실무에서 가장 많이 사용되는 자료구조 중 하나입니다.

**해시 테이블의 주요 특징:**
- **빠른 검색**: 평균 O(1) 시간복잡도로 데이터 검색
- **키-값 쌍 저장**: 연관된 데이터를 효율적으로 관리
- **유연한 키 타입**: 문자열, 숫자 등 다양한 타입을 키로 사용
- **동적 크기**: 필요에 따라 크기를 조절 가능

이번 포스트에서는 해시 테이블의 기본 개념, 해시 함수, 충돌 해결 방법, 그리고 다양한 구현 방식에 대해 자세히 알아보겠습니다.

## 1. 해시 테이블의 추상자료형(ADT)

**추상자료형(ADT)**으로서의 해시 테이블은 다음과 같이 정의할 수 있습니다.

**데이터**:
- 키(key)와 값(value)의 쌍들의 집합
- 각 키는 고유해야 함 (중복 불가)

**연산**:
- `put(key, value)`: 키-값 쌍을 저장
- `get(key)`: 키에 해당하는 값을 반환
- `remove(key)`: 키-값 쌍을 삭제
- `contains(key)`: 키가 존재하는지 확인
- `size()`: 저장된 키-값 쌍의 개수 반환
- `isEmpty()`: 해시 테이블이 비어있는지 확인

### 1-1 해시 테이블의 동작 원리

```
키(Key) → 해시 함수 → 해시값(Hash) → 배열 인덱스 → 값(Value) 저장

예시:
"apple" → hash("apple") → 5 → array[5] = "사과"
"banana" → hash("banana") → 2 → array[2] = "바나나"
"cherry" → hash("cherry") → 7 → array[7] = "체리"
```

## 2. 해시 함수 (Hash Function)

해시 함수는 임의의 크기를 가진 데이터를 고정된 크기의 값으로 변환하는 함수입니다.

### 2-1 좋은 해시 함수의 조건

```python
def hash_function_examples():
    """다양한 해시 함수 예시"""
    
    # 1. 나눗셈 방법 (Division Method)
    def hash_division(key, table_size):
        """나눗셈을 이용한 해시 함수"""
        return key % table_size
    
    # 2. 곱셈 방법 (Multiplication Method)
    def hash_multiplication(key, table_size):
        """곱셈을 이용한 해시 함수"""
        A = 0.6180339887  # 황금비
        return int(table_size * ((key * A) % 1))
    
    # 3. 문자열 해시 함수
    def hash_string_simple(string, table_size):
        """간단한 문자열 해시 함수"""
        hash_value = 0
        for char in string:
            hash_value += ord(char)
        return hash_value % table_size
    
    # 4. 개선된 문자열 해시 함수 (다항 해시)
    def hash_string_polynomial(string, table_size):
        """다항식을 이용한 문자열 해시"""
        hash_value = 0
        p = 31  # 소수
        p_pow = 1
        
        for char in string:
            hash_value = (hash_value + ord(char) * p_pow) % table_size
            p_pow = (p_pow * p) % table_size
        
        return hash_value
    
    # 테스트
    print("=== 해시 함수 비교 ===")
    keys = ["apple", "banana", "cherry", "date", "elderberry"]
    table_size = 10
    
    print(f"\n테이블 크기: {table_size}")
    print(f"키들: {keys}")
    
    for key in keys:
        # 문자열의 ASCII 값 합계를 숫자 키로 사용
        numeric_key = sum(ord(c) for c in key)
        
        div_hash = hash_division(numeric_key, table_size)
        mult_hash = hash_multiplication(numeric_key, table_size)
        str_simple = hash_string_simple(key, table_size)
        str_poly = hash_string_polynomial(key, table_size)
        
        print(f"\n'{key}':")
        print(f"  나눗셈: {div_hash}")
        print(f"  곱셈: {mult_hash}")
        print(f"  단순 문자열: {str_simple}")
        print(f"  다항식: {str_poly}")

# 실행
hash_function_examples()
```

### 2-2 Python의 내장 해시 함수

```python
def python_hash_examples():
    """Python 내장 hash() 함수 예시"""
    
    print("=== Python 내장 hash() 함수 ===")
    
    # 다양한 타입의 해시값
    examples = [
        42,                      # 정수
        3.14,                    # 부동소수점
        "hello",                 # 문자열
        (1, 2, 3),              # 튜플
        frozenset([1, 2, 3])    # frozenset
    ]
    
    for item in examples:
        hash_value = hash(item)
        print(f"hash({repr(item)}) = {hash_value}")
    
    # 해시 불가능한 타입들
    print("\n해시 불가능한 타입들:")
    unhashable = [
        [1, 2, 3],              # 리스트
        {1, 2, 3},              # 집합
        {"key": "value"}        # 딕셔너리
    ]
    
    for item in unhashable:
        try:
            hash_value = hash(item)
        except TypeError as e:
            print(f"hash({type(item).__name__}) -> TypeError: {e}")

python_hash_examples()
```

## 3. 충돌 해결 (Collision Resolution)

서로 다른 키가 같은 해시값을 가질 때 충돌이 발생합니다. 충돌을 해결하는 두 가지 주요 방법이 있습니다.

### 3-1 체이닝 (Chaining)

각 버킷(배열의 각 인덱스)에 연결리스트를 사용하여 충돌하는 원소들을 저장합니다.

```python
class HashTableChaining:
    """체이닝 방식의 해시 테이블"""
    
    def __init__(self, capacity=10):
        self.capacity = capacity
        self.size = 0
        self.buckets = [[] for _ in range(capacity)]
    
    def _hash(self, key):
        """해시 함수"""
        return hash(key) % self.capacity
    
    def put(self, key, value):
        """
        키-값 쌍 삽입 O(1) 평균
        Args:
            key: 키
            value: 값
        """
        bucket_index = self._hash(key)
        bucket = self.buckets[bucket_index]
        
        # 키가 이미 존재하면 값 업데이트
        for i, (k, v) in enumerate(bucket):
            if k == key:
                bucket[i] = (key, value)
                return
        
        # 새로운 키-값 쌍 추가
        bucket.append((key, value))
        self.size += 1
        
        # 로드 팩터가 1을 초과하면 리해싱
        if self.size / self.capacity > 1.0:
            self._rehash()
    
    def get(self, key):
        """
        키에 해당하는 값 반환 O(1) 평균
        Args:
            key: 키
        Returns:
            any: 값
        """
        bucket_index = self._hash(key)
        bucket = self.buckets[bucket_index]
        
        for k, v in bucket:
            if k == key:
                return v
        
        raise KeyError(f"Key '{key}' not found")
    
    def remove(self, key):
        """
        키-값 쌍 삭제 O(1) 평균
        Args:
            key: 키
        Returns:
            any: 삭제된 값
        """
        bucket_index = self._hash(key)
        bucket = self.buckets[bucket_index]
        
        for i, (k, v) in enumerate(bucket):
            if k == key:
                del bucket[i]
                self.size -= 1
                return v
        
        raise KeyError(f"Key '{key}' not found")
    
    def contains(self, key):
        """키 존재 여부 확인 O(1) 평균"""
        try:
            self.get(key)
            return True
        except KeyError:
            return False
    
    def _rehash(self):
        """해시 테이블 크기 조정"""
        print(f"리해싱: {self.capacity} -> {self.capacity * 2}")
        
        old_buckets = self.buckets
        self.capacity *= 2
        self.buckets = [[] for _ in range(self.capacity)]
        self.size = 0
        
        for bucket in old_buckets:
            for key, value in bucket:
                self.put(key, value)
    
    def __str__(self):
        """해시 테이블을 문자열로 표현"""
        items = []
        for bucket in self.buckets:
            for key, value in bucket:
                items.append(f"{key}: {value}")
        return "{" + ", ".join(items) + "}"
    
    def display_buckets(self):
        """버킷 상태 시각화"""
        print(f"해시 테이블 (크기: {self.size}, 용량: {self.capacity})")
        for i, bucket in enumerate(self.buckets):
            if bucket:
                print(f"  버킷 {i}: {bucket}")

# 테스트
ht_chaining = HashTableChaining(capacity=5)

print("=== 체이닝 해시 테이블 테스트 ===")

# 데이터 삽입
ht_chaining.put("apple", "사과")
ht_chaining.put("banana", "바나나")
ht_chaining.put("cherry", "체리")
ht_chaining.put("date", "대추")
ht_chaining.put("elderberry", "엘더베리")

print(f"\n해시 테이블: {ht_chaining}")
ht_chaining.display_buckets()

# 데이터 검색
print(f"\napple: {ht_chaining.get('apple')}")
print(f"banana: {ht_chaining.get('banana')}")

# 데이터 업데이트
ht_chaining.put("apple", "사과(업데이트)")
print(f"업데이트 후 apple: {ht_chaining.get('apple')}")

# 데이터 삭제
ht_chaining.remove("cherry")
print(f"\ncherry 삭제 후: {ht_chaining}")

# 존재 여부 확인
print(f"apple 존재? {ht_chaining.contains('apple')}")
print(f"cherry 존재? {ht_chaining.contains('cherry')}")
```

### 3-2 개방 주소법 (Open Addressing)

충돌이 발생하면 다른 빈 버킷을 찾아 저장합니다.

#### 3-2-1 선형 탐사 (Linear Probing)

```python
class HashTableLinearProbing:
    """선형 탐사를 사용하는 해시 테이블"""
    
    def __init__(self, capacity=10):
        self.capacity = capacity
        self.size = 0
        self.keys = [None] * capacity
        self.values = [None] * capacity
    
    def _hash(self, key):
        """해시 함수"""
        return hash(key) % self.capacity
    
    def put(self, key, value):
        """
        키-값 쌍 삽입 (선형 탐사)
        Args:
            key: 키
            value: 값
        """
        if self.size >= self.capacity * 0.7:
            self._rehash()
        
        index = self._hash(key)
        
        # 빈 슬롯이나 같은 키를 찾을 때까지 선형 탐사
        while self.keys[index] is not None:
            if self.keys[index] == key:
                # 키가 이미 존재하면 값 업데이트
                self.values[index] = value
                return
            
            index = (index + 1) % self.capacity
        
        # 빈 슬롯에 저장
        self.keys[index] = key
        self.values[index] = value
        self.size += 1
    
    def get(self, key):
        """
        키에 해당하는 값 반환
        Args:
            key: 키
        Returns:
            any: 값
        """
        index = self._hash(key)
        
        # 키를 찾을 때까지 선형 탐사
        while self.keys[index] is not None:
            if self.keys[index] == key:
                return self.values[index]
            
            index = (index + 1) % self.capacity
        
        raise KeyError(f"Key '{key}' not found")
    
    def remove(self, key):
        """
        키-값 쌍 삭제
        Args:
            key: 키
        Returns:
            any: 삭제된 값
        """
        index = self._hash(key)
        
        while self.keys[index] is not None:
            if self.keys[index] == key:
                value = self.values[index]
                self.keys[index] = None
                self.values[index] = None
                self.size -= 1
                
                # 재배치 필요 (삭제 후 빈 공간으로 인한 탐사 중단 방지)
                self._reposition(index)
                return value
            
            index = (index + 1) % self.capacity
        
        raise KeyError(f"Key '{key}' not found")
    
    def _reposition(self, start_index):
        """삭제 후 원소들 재배치"""
        index = (start_index + 1) % self.capacity
        
        while self.keys[index] is not None:
            key = self.keys[index]
            value = self.values[index]
            
            self.keys[index] = None
            self.values[index] = None
            self.size -= 1
            
            self.put(key, value)
            index = (index + 1) % self.capacity
    
    def _rehash(self):
        """해시 테이블 크기 조정"""
        print(f"리해싱: {self.capacity} -> {self.capacity * 2}")
        
        old_keys = self.keys
        old_values = self.values
        
        self.capacity *= 2
        self.keys = [None] * self.capacity
        self.values = [None] * self.capacity
        self.size = 0
        
        for i in range(len(old_keys)):
            if old_keys[i] is not None:
                self.put(old_keys[i], old_values[i])
    
    def __str__(self):
        """해시 테이블을 문자열로 표현"""
        items = []
        for i in range(self.capacity):
            if self.keys[i] is not None:
                items.append(f"{self.keys[i]}: {self.values[i]}")
        return "{" + ", ".join(items) + "}"
    
    def display_table(self):
        """테이블 상태 시각화"""
        print(f"해시 테이블 (크기: {self.size}, 용량: {self.capacity})")
        for i in range(self.capacity):
            if self.keys[i] is not None:
                print(f"  인덱스 {i}: {self.keys[i]} = {self.values[i]}")
            else:
                print(f"  인덱스 {i}: (빈 슬롯)")

# 테스트
ht_linear = HashTableLinearProbing(capacity=7)

print("\n=== 선형 탐사 해시 테이블 테스트 ===")

# 데이터 삽입
ht_linear.put("apple", "사과")
ht_linear.put("banana", "바나나")
ht_linear.put("cherry", "체리")
ht_linear.put("date", "대추")

print(f"\n해시 테이블: {ht_linear}")
ht_linear.display_table()

# 충돌 확인
print(f"\n해시값 비교:")
for key in ["apple", "banana", "cherry", "date"]:
    print(f"{key}: hash = {hash(key) % 7}")
```

## 4. 해시 테이블의 시간복잡도 분석

해시 테이블의 주요 연산별 시간복잡도는 다음과 같습니다.

| 연산 | 평균 시간복잡도 | 최악 시간복잡도 | 설명 |
|------|----------------|----------------|------|
| `put()` | O(1) | O(n) | 삽입 (리해싱 제외) |
| `get()` | O(1) | O(n) | 검색 |
| `remove()` | O(1) | O(n) | 삭제 |
| `contains()` | O(1) | O(n) | 존재 여부 확인 |

**로드 팩터(Load Factor)**:
- 로드 팩터 = 저장된 원소 개수 / 테이블 크기
- 로드 팩터가 높을수록 충돌 가능성 증가
- 일반적으로 0.7~0.75를 넘으면 리해싱 수행

## 5. Python의 딕셔너리 (Dictionary)

Python의 딕셔너리는 해시 테이블로 구현되어 있습니다.

```python
def python_dict_examples():
    """Python 딕셔너리 활용 예시"""
    
    print("=== Python 딕셔너리 ===")
    
    # 딕셔너리 생성
    person = {
        "name": "홍길동",
        "age": 30,
        "city": "서울"
    }
    
    print(f"딕셔너리: {person}")
    
    # 데이터 접근
    print(f"이름: {person['name']}")
    print(f"나이: {person.get('age')}")
    print(f"직업: {person.get('job', '없음')}")  # 기본값 사용
    
    # 데이터 추가/업데이트
    person["job"] = "개발자"
    person["age"] = 31
    print(f"업데이트 후: {person}")
    
    # 데이터 삭제
    del person["city"]
    print(f"삭제 후: {person}")
    
    # 키 존재 여부 확인
    print(f"'name' in person: {'name' in person}")
    print(f"'city' in person: {'city' in person}")
    
    # 모든 키, 값, 키-값 쌍 가져오기
    print(f"키들: {list(person.keys())}")
    print(f"값들: {list(person.values())}")
    print(f"키-값 쌍들: {list(person.items())}")
    
    # 딕셔너리 순회
    print("\n순회:")
    for key, value in person.items():
        print(f"  {key}: {value}")

python_dict_examples()
```

## 정리

이번 포스트에서는 해시 테이블의 기본 개념과 구현 방법에 대해 알아보았습니다.

**핵심 개념 정리**
1. **해시 함수**: 키를 해시값으로 변환하여 배열 인덱스 결정
2. **충돌 해결**: 체이닝(연결리스트)과 개방 주소법(선형/이차 탐사)
3. **시간복잡도**: 평균 O(1)의 빠른 검색, 삽입, 삭제
4. **로드 팩터**: 성능 유지를 위한 리해싱 메커니즘
5. **Python 딕셔너리**: 해시 테이블 기반의 내장 자료구조

**다음 학습 계획**
다음 포스트에서는 해시 테이블을 활용한 다양한 문제 해결 방법을 알아보겠습니다:
- [[자료구조] 해시 테이블 활용 - 1]({% post_url 2025-10-07-[자료구조] 해시 테이블(Hash Table) - 활용 - 1 %}) - 중복 체크, 빈도 계산, 두 수의 합 등
- [[자료구조] 해시 테이블 활용 - 2]({% post_url 2025-10-07-[자료구조] 해시 테이블(Hash Table) - 활용 - 2 %}) - 블룸 필터, 일관성 해싱 등
- [[자료구조] 해시 테이블 활용 - 3]({% post_url 2025-10-07-[자료구조] 해시 테이블(Hash Table) - 활용 - 3 %}) - 캐싱, 데이터베이스 인덱싱 등

# 📚 레퍼런스
- [Hash Table - Wikipedia](https://en.wikipedia.org/wiki/Hash_table)
- [Python Dictionary Implementation](https://docs.python.org/3/tutorial/datastructures.html#dictionaries)
- [Introduction to Algorithms - Thomas H. Cormen](https://mitpress.mit.edu/books/introduction-algorithms-third-edition)

---
*이 포스트는 자료구조 학습 과정을 정리한 내용입니다.*
