---
layout: post
title: "[학습] [자료구조] 연결리스트(Linked List)"
date: 2025-10-04 10:34:00 +0900
categories: [학습, 자료구조]
tags: [연결리스트, Linked List, 자료구조, 포인터, 노드, 선형자료구조]
author: LimJongHan
toc: true
---

# 📖 관련된 내 포스팅
- [[학습] [자료구조] 배열(Array)]({% post_url 2025-10-03-[학습][자료구조] 배열(Array) %})
- [[학습] [자료구조] 연결리스트(Linked List) 활용 - 1. 예제]({% post_url 2025-10-04-[학습][자료구조] 연결리스트(Linked List) 활용 - 1. 예제 %})
- [[학습] [자료구조] 연결리스트(Linked List) 활용 - 2. 고급 구현]({% post_url 2025-10-04-[학습][자료구조] 연결리스트(Linked List) 활용 - 2. 고급 구현 %})
- [[학습] [자료구조] 연결리스트(Linked List) 활용 - 3. 실무 활용]({% post_url 2025-10-04-[학습][자료구조] 연결리스트(Linked List) 활용 - 3. 실무 활용 %})

# 📝 내용

## 개요

연결리스트(Linked List)는 배열과 함께 가장 기본적인 선형 자료구조입니다. 배열이 연속된 메모리 공간에 데이터를 저장하는 반면, 연결리스트는 각 노드가 다음 노드의 주소(포인터)를 가지고 있어 논리적으로 연결된 구조입니다.

**연결리스트가 중요한 이유:**
- 🔗 **동적 크기**: 런타임에 크기를 조절할 수 있음
- ⚡ **효율적인 삽입/삭제**: 특정 위치에서 O(1) 시간에 가능
- 💾 **메모리 효율**: 필요한 만큼만 메모리 사용
- 🏗️ **기본 구조**: 스택, 큐 등 다른 자료구조의 기반이 됨

이번 포스트에서는 연결리스트의 기본 개념과 Python 구현에 대해 알아보겠습니다.

## 핵심 내용

### 1. 연결리스트의 정의와 특징

#### 1-1 연결리스트란?

**연결리스트(Linked List)**란?
- 노드(Node)들이 포인터로 연결된 선형 자료구조
- 각 노드는 데이터와 다음 노드의 주소를 저장
- 메모리에서 연속적이지 않아도 됨
- 크기를 동적으로 조절 가능

**연결리스트의 구조:**
```
[데이터|포인터] → [데이터|포인터] → [데이터|NULL]
     노드1           노드2           노드3
```

#### 1-2 연결리스트의 주요 특징

**장점:**
- **동적 크기**: 런타임에 크기 조절 가능
- **효율적인 삽입/삭제**: 특정 위치에서 O(1) 시간
- **메모리 효율**: 필요한 만큼만 메모리 할당
- **메모리 재사용**: 삭제된 노드의 메모리 재사용 가능

**단점:**
- **순차 접근**: 특정 위치 접근 시 O(n) 시간
- **메모리 오버헤드**: 포인터 저장을 위한 추가 메모리
- **캐시 효율성**: 연속되지 않은 메모리로 인한 캐시 미스
- **역방향 탐색**: 단일 연결리스트에서는 어려움

### 2. 배열 vs 연결리스트 비교

#### 2-1 상세 비교표

| 특성 | 배열 | 연결리스트 |
|------|------|------------|
| **메모리 할당** | 연속된 메모리 | 분산된 메모리 |
| **크기** | 고정 크기 | 동적 크기 |
| **접근 시간** | O(1) - 인덱스 접근 | O(n) - 순차 접근 |
| **삽입 (시작)** | O(n) - 원소 이동 | O(1) - 포인터 변경 |
| **삽입 (끝)** | O(1) - 단순 추가 | O(n) - 끝까지 탐색 |
| **삽입 (중간)** | O(n) - 원소 이동 | O(n) - 위치 탐색 |
| **삭제 (시작)** | O(n) - 원소 이동 | O(1) - 포인터 변경 |
| **삭제 (끝)** | O(1) - 단순 제거 | O(n) - 끝까지 탐색 |
| **삭제 (중간)** | O(n) - 원소 이동 | O(n) - 위치 탐색 |
| **메모리 오버헤드** | 없음 | 포인터 저장 공간 |
| **캐시 효율성** | 높음 | 낮음 |

#### 2-2 언제 어떤 것을 사용할까?

**배열을 사용하는 경우:**
- 인덱스로 빠른 접근이 필요한 경우
- 메모리 사용량이 중요한 경우
- 데이터 크기가 고정적인 경우
- 순차적 접근이 주된 경우

**연결리스트를 사용하는 경우:**
- 삽입/삭제가 빈번한 경우
- 데이터 크기가 동적으로 변하는 경우
- 메모리 크기가 제한적이지 않은 경우
- 중간 위치의 삽입/삭제가 많은 경우

### 3. 연결리스트의 기본 구현

#### 3-1 노드 클래스 정의

```python
class ListNode:
    """연결리스트의 노드를 나타내는 클래스"""
    
    def __init__(self, val=0, next=None):
        """
        노드 초기화
        
        Args:
            val: 노드에 저장할 데이터
            next: 다음 노드를 가리키는 포인터
        """
        self.val = val
        self.next = next
    
    def __str__(self):
        """노드의 문자열 표현"""
        return f"ListNode({self.val})"
    
    def __repr__(self):
        """노드의 디버그 표현"""
        return f"ListNode(val={self.val}, next={'→' if self.next else 'None'})"

# 노드 생성 예시
node1 = ListNode(1)
node2 = ListNode(2)
node3 = ListNode(3)

# 노드 연결
node1.next = node2
node2.next = node3

print(f"노드1: {node1}")
print(f"노드2: {node2}")
print(f"노드3: {node3}")
print(f"연결 상태: {node1} → {node1.next} → {node1.next.next}")
```

#### 3-2 연결리스트 클래스 구현

```python
class LinkedList:
    """단일 연결리스트 클래스"""
    
    def __init__(self):
        """빈 연결리스트 생성"""
        self.head = None
        self.size = 0
    
    def is_empty(self):
        """연결리스트가 비어있는지 확인"""
        return self.head is None
    
    def append(self, val):
        """
        연결리스트 끝에 새 노드 추가
        
        Args:
            val: 추가할 값
        """
        new_node = ListNode(val)
        
        if self.is_empty():
            self.head = new_node
        else:
            current = self.head
            while current.next is not None:
                current = current.next
            current.next = new_node
        
        self.size += 1
    
    def prepend(self, val):
        """
        연결리스트 앞에 새 노드 추가
        
        Args:
            val: 추가할 값
        """
        new_node = ListNode(val)
        new_node.next = self.head
        self.head = new_node
        self.size += 1
    
    def insert(self, index, val):
        """
        지정된 인덱스에 새 노드 삽입
        
        Args:
            index: 삽입할 위치
            val: 삽입할 값
        """
        if index < 0 or index > self.size:
            raise IndexError("인덱스가 범위를 벗어났습니다")
        
        if index == 0:
            self.prepend(val)
            return
        
        new_node = ListNode(val)
        current = self.head
        
        # 삽입 위치의 이전 노드까지 이동
        for _ in range(index - 1):
            current = current.next
        
        new_node.next = current.next
        current.next = new_node
        self.size += 1
    
    def delete_by_value(self, val):
        """
        특정 값을 가진 첫 번째 노드 삭제
        
        Args:
            val: 삭제할 값
            
        Returns:
            bool: 삭제 성공 여부
        """
        if self.is_empty():
            return False
        
        # 첫 번째 노드가 삭제할 값인 경우
        if self.head.val == val:
            self.head = self.head.next
            self.size -= 1
            return True
        
        current = self.head
        while current.next is not None:
            if current.next.val == val:
                current.next = current.next.next
                self.size -= 1
                return True
            current = current.next
        
        return False
    
    def delete_by_index(self, index):
        """
        지정된 인덱스의 노드 삭제
        
        Args:
            index: 삭제할 인덱스
            
        Returns:
            삭제된 노드의 값
        """
        if index < 0 or index >= self.size:
            raise IndexError("인덱스가 범위를 벗어났습니다")
        
        if index == 0:
            val = self.head.val
            self.head = self.head.next
            self.size -= 1
            return val
        
        current = self.head
        for _ in range(index - 1):
            current = current.next
        
        val = current.next.val
        current.next = current.next.next
        self.size -= 1
        return val
    
    def find(self, val):
        """
        특정 값을 가진 노드의 인덱스 찾기
        
        Args:
            val: 찾을 값
            
        Returns:
            int: 인덱스 (없으면 -1)
        """
        current = self.head
        index = 0
        
        while current is not None:
            if current.val == val:
                return index
            current = current.next
            index += 1
        
        return -1
    
    def get(self, index):
        """
        지정된 인덱스의 값 조회
        
        Args:
            index: 조회할 인덱스
            
        Returns:
            해당 인덱스의 값
        """
        if index < 0 or index >= self.size:
            raise IndexError("인덱스가 범위를 벗어났습니다")
        
        current = self.head
        for _ in range(index):
            current = current.next
        
        return current.val
    
    def set(self, index, val):
        """
        지정된 인덱스의 값 수정
        
        Args:
            index: 수정할 인덱스
            val: 새로운 값
        """
        if index < 0 or index >= self.size:
            raise IndexError("인덱스가 범위를 벗어났습니다")
        
        current = self.head
        for _ in range(index):
            current = current.next
        
        current.val = val
    
    def length(self):
        """연결리스트의 길이 반환"""
        return self.size
    
    def display(self):
        """연결리스트 전체 출력"""
        if self.is_empty():
            print("연결리스트가 비어있습니다")
            return
        
        values = []
        current = self.head
        while current is not None:
            values.append(str(current.val))
            current = current.next
        
        print(" → ".join(values))
    
    def to_list(self):
        """연결리스트를 Python 리스트로 변환"""
        result = []
        current = self.head
        while current is not None:
            result.append(current.val)
            current = current.next
        return result
    
    def __str__(self):
        """연결리스트의 문자열 표현"""
        if self.is_empty():
            return "LinkedList([])"
        return f"LinkedList({self.to_list()})"
    
    def __len__(self):
        """len() 함수 지원"""
        return self.size

# 사용 예시
if __name__ == "__main__":
    # 연결리스트 생성
    ll = LinkedList()
    
    print("빈 연결리스트 생성:")
    print(f"길이: {len(ll)}")
    print(f"비어있음: {ll.is_empty()}")
    
    # 값 추가
    ll.append(1)
    ll.append(2)
    ll.append(3)
    print(f"\n값 추가 후: {ll}")
    print(f"길이: {len(ll)}")
    
    # 앞에 값 추가
    ll.prepend(0)
    print(f"앞에 0 추가 후: {ll}")
    
    # 중간에 값 삽입
    ll.insert(2, 1.5)
    print(f"인덱스 2에 1.5 삽입 후: {ll}")
    
    # 값 조회
    print(f"인덱스 0의 값: {ll.get(0)}")
    print(f"값 2의 인덱스: {ll.find(2)}")
    
    # 값 수정
    ll.set(1, 0.5)
    print(f"인덱스 1을 0.5로 수정 후: {ll}")
    
    # 값 삭제
    deleted = ll.delete_by_value(1.5)
    print(f"값 1.5 삭제 결과: {deleted}, 삭제 후: {ll}")
    
    deleted_val = ll.delete_by_index(0)
    print(f"인덱스 0 삭제 (값: {deleted_val}), 삭제 후: {ll}")
    
    # 전체 출력
    print(f"\n최종 연결리스트:")
    ll.display()
```

### 4. 연결리스트의 시간복잡도 분석

#### 4-1 주요 연산의 시간복잡도

| 연산 | 시간복잡도 | 설명 |
|------|------------|------|
| **접근 (Access)** | O(n) | 특정 인덱스에 접근하기 위해 순차 탐색 필요 |
| **검색 (Search)** | O(n) | 특정 값을 찾기 위해 순차 탐색 필요 |
| **삽입 (Insert)** | O(1) | 시작 위치에 삽입 (포인터 변경만 필요) |
| **삽입 (Insert)** | O(n) | 중간/끝 위치에 삽입 (위치 탐색 필요) |
| **삭제 (Delete)** | O(1) | 시작 위치에서 삭제 (포인터 변경만 필요) |
| **삭제 (Delete)** | O(n) | 중간/끝 위치에서 삭제 (위치 탐색 필요) |
| **길이 조회** | O(1) | size 변수로 즉시 조회 |
| **비어있음 확인** | O(1) | head 포인터 확인만 필요 |

#### 4-2 성능 비교 예시

```python
import time

def performance_comparison():
    """배열과 연결리스트의 성능 비교"""
    sizes = [1000, 5000, 10000]
    
    for size in sizes:
        print(f"\n배열 크기: {size}")
        
        # 배열 생성
        arr = list(range(size))
        
        # 연결리스트 생성
        ll = LinkedList()
        for i in range(size):
            ll.append(i)
        
        # 접근 성능 비교
        start = time.time()
        _ = arr[size // 2]  # 배열 중간 접근
        arr_access_time = time.time() - start
        
        start = time.time()
        _ = ll.get(size // 2)  # 연결리스트 중간 접근
        ll_access_time = time.time() - start
        
        print(f"중간 접근 - 배열: {arr_access_time:.6f}초, 연결리스트: {ll_access_time:.6f}초")
        
        # 삽입 성능 비교 (중간 위치)
        start = time.time()
        arr.insert(size // 2, 999)  # 배열 중간 삽입
        arr_insert_time = time.time() - start
        
        start = time.time()
        ll.insert(size // 2, 999)  # 연결리스트 중간 삽입
        ll_insert_time = time.time() - start
        
        print(f"중간 삽입 - 배열: {arr_insert_time:.6f}초, 연결리스트: {ll_insert_time:.6f}초")
        
        # 시작 위치 삽입 성능 비교
        start = time.time()
        arr.insert(0, 999)  # 배열 시작 삽입
        arr_prepend_time = time.time() - start
        
        start = time.time()
        ll.prepend(999)  # 연결리스트 시작 삽입
        ll_prepend_time = time.time() - start
        
        print(f"시작 삽입 - 배열: {arr_prepend_time:.6f}초, 연결리스트: {ll_prepend_time:.6f}초")

# 성능 테스트 실행
if __name__ == "__main__":
    performance_comparison()
```

### 5. 연결리스트의 메모리 관리

#### 5-1 Python에서의 메모리 관리

```python
import gc
import sys

def memory_usage_example():
    """연결리스트의 메모리 사용량 예시"""
    
    # 작은 연결리스트 생성
    ll = LinkedList()
    for i in range(10):
        ll.append(i)
    
    print(f"연결리스트: {ll}")
    print(f"연결리스트 크기: {sys.getsizeof(ll)} bytes")
    
    # 노드들의 메모리 사용량
    current = ll.head
    total_node_memory = 0
    node_count = 0
    
    while current is not None:
        node_memory = sys.getsizeof(current)
        total_node_memory += node_memory
        node_count += 1
        print(f"노드 {node_count}: {node_memory} bytes")
        current = current.next
    
    print(f"총 노드 메모리: {total_node_memory} bytes")
    print(f"노드 개수: {node_count}")
    print(f"평균 노드 크기: {total_node_memory / node_count:.2f} bytes")

def memory_cleanup_example():
    """메모리 정리 예시"""
    
    # 큰 연결리스트 생성
    ll = LinkedList()
    for i in range(1000):
        ll.append(f"data_{i}")
    
    print(f"생성 후 연결리스트 길이: {len(ll)}")
    
    # 모든 노드 삭제
    while not ll.is_empty():
        ll.delete_by_index(0)
    
    print(f"삭제 후 연결리스트 길이: {len(ll)}")
    
    # 가비지 컬렉션 강제 실행
    collected = gc.collect()
    print(f"가비지 컬렉션으로 정리된 객체 수: {collected}")

# 메모리 테스트 실행
if __name__ == "__main__":
    print("=== 메모리 사용량 예시 ===")
    memory_usage_example()
    
    print("\n=== 메모리 정리 예시 ===")
    memory_cleanup_example()
```

## 정리

이번 포스트에서는 연결리스트의 기본 개념과 Python 구현을 다뤘습니다:

**핵심 개념 정리**
1. **연결리스트의 정의**: 노드들이 포인터로 연결된 선형 자료구조
2. **배열과의 차이점**: 메모리 할당, 접근 방식, 삽입/삭제 성능의 차이
3. **노드 구조**: 데이터와 다음 노드를 가리키는 포인터로 구성
4. **기본 연산**: 삽입, 삭제, 검색, 접근의 시간복잡도

**연결리스트의 장단점**
- ✅ **장점**: 동적 크기, 효율적인 삽입/삭제, 메모리 효율성
- ❌ **단점**: 순차 접근, 메모리 오버헤드, 캐시 비효율성

**실무 적용 포인트**
- 🎯 **적절한 사용**: 삽입/삭제가 빈번한 경우에 유리
- ⚡ **성능 고려**: 접근 패턴에 따라 배열과 연결리스트 선택
- 💾 **메모리 관리**: Python의 가비지 컬렉션과 함께 사용

**다음 학습 계획**
다음 포스트에서는 연결리스트를 활용한 기본 문제 해결 예제들을 알아보겠습니다:
- [[학습] [자료구조] 연결리스트(Linked List) 활용 - 1. 예제]({% post_url 2025-10-04-[학습][자료구조] 연결리스트(Linked List) 활용 - 1. 예제 %}) - 역순 만들기, 병합, 중복 제거 등

# 📚 레퍼런스
- [Linked List Data Structure - GeeksforGeeks](https://www.geeksforgeeks.org/data-structures/linked-list/)
- [Python Data Structures - Official Documentation](https://docs.python.org/3/tutorial/datastructures.html)
- [Array vs Linked List - Programiz](https://www.programiz.com/dsa/linked-list-vs-array)

---
*이 포스트는 자료구조 학습 과정을 정리한 내용입니다.*
