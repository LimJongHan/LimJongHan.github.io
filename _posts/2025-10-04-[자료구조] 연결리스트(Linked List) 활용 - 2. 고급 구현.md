---
layout: post
title: "[자료구조] 연결리스트(Linked List) 활용 - 2. 고급 구현"
date: 2025-10-04 10:45:00 +0900
categories: [자료구조, 연결리스트]
tags: [연결리스트, Linked List, 이중연결리스트, 원형연결리스트, 고급구현, 최적화]
author: LimJongHan
toc: true
---

# 📖 관련된 내 포스팅
- [[자료구조] 연결리스트(Linked List)]({% post_url 2025-10-04-[자료구조] 연결리스트(Linked List) %})
- [[자료구조] 연결리스트(Linked List) 활용 - 1. 예제]({% post_url 2025-10-04-[자료구조] 연결리스트(Linked List) 활용 - 1. 예제 %})
- [[자료구조] 연결리스트(Linked List) 활용 - 3. 실무 활용]({% post_url 2025-10-04-[자료구조] 연결리스트(Linked List) 활용 - 3. 실무 활용 %})

# 📝 내용

## 개요

이전 포스트들에서 단일 연결리스트의 기본 개념과 문제 해결 예제들을 다뤘습니다. 이번 포스트에서는 더 복잡하고 효율적인 연결리스트 구현들에 대해 알아보겠습니다.

**이번 포스트에서 다룰 고급 구현들:**
- 🔄 **이중 연결리스트 (Doubly Linked List)**: 양방향 탐색과 삭제 최적화
- ⭕ **원형 연결리스트 (Circular Linked List)**: 순환 구조와 끝없는 탐색
- 🔗 **원형 이중 연결리스트**: 양방향과 순환의 결합
- ⚡ **최적화 기법**: 메모리 풀, 노드 재사용, 성능 개선
- 🛠️ **Python 특화 최적화**: 가비지 컬렉션과 메모리 관리

이러한 고급 구현들은 특정 상황에서 단일 연결리스트보다 더 효율적이고 유용합니다.

## 핵심 내용

### 1. 이중 연결리스트 (Doubly Linked List)

#### 1-1 이중 연결리스트의 정의와 특징

**이중 연결리스트**란?
- 각 노드가 이전 노드와 다음 노드 모두를 가리키는 연결리스트
- 양방향 탐색이 가능하여 역방향 탐색과 삭제가 효율적
- 메모리 오버헤드가 더 크지만 더 유연한 조작 가능

**구조:**
```
NULL ← [데이터] ← [데이터] ← [데이터] → NULL
       prev    prev    prev
```

#### 1-2 이중 연결리스트 노드 클래스

```python
class DoublyListNode:
    """이중 연결리스트의 노드 클래스"""
    
    def __init__(self, val=0, prev=None, next=None):
        """
        이중 연결리스트 노드 초기화
        
        Args:
            val: 노드에 저장할 데이터
            prev: 이전 노드를 가리키는 포인터
            next: 다음 노드를 가리키는 포인터
        """
        self.val = val
        self.prev = prev
        self.next = next
    
    def __str__(self):
        """노드의 문자열 표현"""
        return f"DoublyListNode({self.val})"
    
    def __repr__(self):
        """노드의 디버그 표현"""
        prev_val = self.prev.val if self.prev else "None"
        next_val = self.next.val if self.next else "None"
        return f"DoublyListNode(val={self.val}, prev={prev_val}, next={next_val})"

# 노드 생성 예시
node1 = DoublyListNode(1)
node2 = DoublyListNode(2)
node3 = DoublyListNode(3)

# 양방향 연결
node1.next = node2
node2.prev = node1
node2.next = node3
node3.prev = node2

print(f"노드1: {node1}")
print(f"노드2: {node2}")
print(f"노드3: {node3}")
```

#### 1-3 이중 연결리스트 클래스 구현

```python
class DoublyLinkedList:
    """이중 연결리스트 클래스"""
    
    def __init__(self):
        """빈 이중 연결리스트 생성"""
        self.head = None
        self.tail = None
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
        new_node = DoublyListNode(val)
        
        if self.is_empty():
            self.head = self.tail = new_node
        else:
            new_node.prev = self.tail
            self.tail.next = new_node
            self.tail = new_node
        
        self.size += 1
    
    def prepend(self, val):
        """
        연결리스트 앞에 새 노드 추가
        
        Args:
            val: 추가할 값
        """
        new_node = DoublyListNode(val)
        
        if self.is_empty():
            self.head = self.tail = new_node
        else:
            new_node.next = self.head
            self.head.prev = new_node
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
        if index == self.size:
            self.append(val)
            return
        
        new_node = DoublyListNode(val)
        
        # 삽입할 위치의 노드 찾기
        current = self.head
        for _ in range(index):
            current = current.next
        
        # 새 노드 연결
        new_node.prev = current.prev
        new_node.next = current
        current.prev.next = new_node
        current.prev = new_node
        
        self.size += 1
    
    def delete_by_value(self, val):
        """
        특정 값을 가진 첫 번째 노드 삭제
        
        Args:
            val: 삭제할 값
            
        Returns:
            bool: 삭제 성공 여부
        """
        current = self.head
        
        while current is not None:
            if current.val == val:
                self._delete_node(current)
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
        
        current = self.head
        for _ in range(index):
            current = current.next
        
        val = current.val
        self._delete_node(current)
        return val
    
    def _delete_node(self, node):
        """노드 삭제 (내부 메서드)"""
        if node.prev is not None:
            node.prev.next = node.next
        else:
            self.head = node.next
        
        if node.next is not None:
            node.next.prev = node.prev
        else:
            self.tail = node.prev
        
        self.size -= 1
    
    def find(self, val):
        """
        특정 값을 가진 노드의 인덱스 찾기 (앞에서부터)
        
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
    
    def find_reverse(self, val):
        """
        특정 값을 가진 노드의 인덱스 찾기 (뒤에서부터)
        
        Args:
            val: 찾을 값
            
        Returns:
            int: 인덱스 (없으면 -1)
        """
        current = self.tail
        index = self.size - 1
        
        while current is not None:
            if current.val == val:
                return index
            current = current.prev
            index -= 1
        
        return -1
    
    def get(self, index):
        """
        지정된 인덱스의 값 조회 (최적화: 뒤에서부터 시작할 수도 있음)
        
        Args:
            index: 조회할 인덱스
            
        Returns:
            해당 인덱스의 값
        """
        if index < 0 or index >= self.size:
            raise IndexError("인덱스가 범위를 벗어났습니다")
        
        # 뒤에서부터 시작하는 것이 더 효율적인 경우
        if index >= self.size // 2:
            current = self.tail
            for _ in range(self.size - 1 - index):
                current = current.prev
        else:
            current = self.head
            for _ in range(index):
                current = current.next
        
        return current.val
    
    def display(self):
        """연결리스트 전체 출력 (앞에서부터)"""
        if self.is_empty():
            print("연결리스트가 비어있습니다")
            return
        
        values = []
        current = self.head
        while current is not None:
            values.append(str(current.val))
            current = current.next
        
        print(" ↔ ".join(values))
    
    def display_reverse(self):
        """연결리스트 전체 출력 (뒤에서부터)"""
        if self.is_empty():
            print("연결리스트가 비어있습니다")
            return
        
        values = []
        current = self.tail
        while current is not None:
            values.append(str(current.val))
            current = current.prev
        
        print(" ↔ ".join(reversed(values)))
    
    def length(self):
        """연결리스트의 길이 반환"""
        return self.size
    
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
            return "DoublyLinkedList([])"
        return f"DoublyLinkedList({self.to_list()})"
    
    def __len__(self):
        """len() 함수 지원"""
        return self.size

# 사용 예시
if __name__ == "__main__":
    print("=== 이중 연결리스트 테스트 ===")
    
    # 이중 연결리스트 생성
    dll = DoublyLinkedList()
    
    # 값 추가
    dll.append(1)
    dll.append(2)
    dll.append(3)
    dll.prepend(0)
    
    print(f"이중 연결리스트: {dll}")
    print(f"길이: {len(dll)}")
    
    # 순방향 출력
    print("순방향:")
    dll.display()
    
    # 역방향 출력
    print("역방향:")
    dll.display_reverse()
    
    # 중간에 삽입
    dll.insert(2, 1.5)
    print(f"중간 삽입 후: {dll}")
    
    # 값 조회 (최적화된 방법)
    print(f"인덱스 0: {dll.get(0)}")
    print(f"인덱스 4: {dll.get(4)}")  # 뒤에서부터 시작
    
    # 값 찾기
    print(f"값 2의 인덱스 (앞에서): {dll.find(2)}")
    print(f"값 2의 인덱스 (뒤에서): {dll.find_reverse(2)}")
    
    # 값 삭제
    deleted = dll.delete_by_value(1.5)
    print(f"값 1.5 삭제 결과: {deleted}, 삭제 후: {dll}")
```

### 2. 원형 연결리스트 (Circular Linked List)

#### 2-1 원형 연결리스트의 정의와 특징

**원형 연결리스트**란?
- 마지막 노드가 첫 번째 노드를 가리키는 연결리스트
- 끝이 없어 순환하는 구조
- 큐 구현, 라운드 로빈 스케줄링 등에 유용

#### 2-2 원형 연결리스트 구현

```python
class CircularLinkedList:
    """원형 연결리스트 클래스"""
    
    def __init__(self):
        """빈 원형 연결리스트 생성"""
        self.tail = None  # 마지막 노드 (첫 번째 노드에 접근 가능)
        self.size = 0
    
    def is_empty(self):
        """연결리스트가 비어있는지 확인"""
        return self.tail is None
    
    def append(self, val):
        """
        연결리스트 끝에 새 노드 추가
        
        Args:
            val: 추가할 값
        """
        new_node = ListNode(val)
        
        if self.is_empty():
            new_node.next = new_node  # 자기 자신을 가리킴
            self.tail = new_node
        else:
            new_node.next = self.tail.next  # 첫 번째 노드 가리킴
            self.tail.next = new_node
            self.tail = new_node
        
        self.size += 1
    
    def prepend(self, val):
        """
        연결리스트 앞에 새 노드 추가
        
        Args:
            val: 추가할 값
        """
        new_node = ListNode(val)
        
        if self.is_empty():
            new_node.next = new_node
            self.tail = new_node
        else:
            new_node.next = self.tail.next
            self.tail.next = new_node
        
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
        
        current = self.tail.next  # 첫 번째 노드부터 시작
        prev = self.tail
        
        # 한 바퀴 돌면서 찾기
        for _ in range(self.size):
            if current.val == val:
                if current == self.tail and self.size == 1:
                    # 마지막 노드인 경우
                    self.tail = None
                elif current == self.tail:
                    # 마지막 노드인 경우
                    prev.next = current.next
                    self.tail = prev
                else:
                    # 일반적인 경우
                    prev.next = current.next
                
                self.size -= 1
                return True
            
            prev = current
            current = current.next
        
        return False
    
    def find(self, val):
        """
        특정 값을 가진 노드의 인덱스 찾기
        
        Args:
            val: 찾을 값
            
        Returns:
            int: 인덱스 (없으면 -1)
        """
        if self.is_empty():
            return -1
        
        current = self.tail.next  # 첫 번째 노드부터 시작
        index = 0
        
        for _ in range(self.size):
            if current.val == val:
                return index
            current = current.next
            index += 1
        
        return -1
    
    def rotate(self, steps=1):
        """
        연결리스트를 지정된 단계만큼 회전
        
        Args:
            steps: 회전할 단계 수 (양수: 시계방향, 음수: 반시계방향)
        """
        if self.is_empty() or steps == 0:
            return
        
        steps = steps % self.size  # 사이즈로 나눈 나머지
        
        if steps > 0:
            # 시계방향 회전
            for _ in range(steps):
                self.tail = self.tail.next
        else:
            # 반시계방향 회전
            for _ in range(-steps):
                current = self.tail.next
                while current.next != self.tail:
                    current = current.next
                self.tail = current
    
    def display(self):
        """연결리스트 전체 출력"""
        if self.is_empty():
            print("원형 연결리스트가 비어있습니다")
            return
        
        values = []
        current = self.tail.next  # 첫 번째 노드부터 시작
        
        for _ in range(self.size):
            values.append(str(current.val))
            current = current.next
        
        print(" → ".join(values) + " → (처음으로)")
    
    def get_head(self):
        """첫 번째 노드 반환"""
        return self.tail.next if not self.is_empty() else None
    
    def get_tail(self):
        """마지막 노드 반환"""
        return self.tail
    
    def length(self):
        """연결리스트의 길이 반환"""
        return self.size
    
    def to_list(self):
        """연결리스트를 Python 리스트로 변환"""
        if self.is_empty():
            return []
        
        result = []
        current = self.tail.next
        
        for _ in range(self.size):
            result.append(current.val)
            current = current.next
        
        return result
    
    def __str__(self):
        """연결리스트의 문자열 표현"""
        if self.is_empty():
            return "CircularLinkedList([])"
        return f"CircularLinkedList({self.to_list()})"
    
    def __len__(self):
        """len() 함수 지원"""
        return self.size

# 사용 예시
if __name__ == "__main__":
    print("\n=== 원형 연결리스트 테스트 ===")
    
    # 원형 연결리스트 생성
    cll = CircularLinkedList()
    
    # 값 추가
    cll.append(1)
    cll.append(2)
    cll.append(3)
    cll.prepend(0)
    
    print(f"원형 연결리스트: {cll}")
    print(f"길이: {len(cll)}")
    
    # 연결리스트 출력
    cll.display()
    
    # 회전 테스트
    print("시계방향 1단계 회전:")
    cll.rotate(1)
    cll.display()
    
    print("반시계방향 2단계 회전:")
    cll.rotate(-2)
    cll.display()
    
    # 값 찾기
    print(f"값 2의 인덱스: {cll.find(2)}")
    
    # 값 삭제
    deleted = cll.delete_by_value(1)
    print(f"값 1 삭제 결과: {deleted}, 삭제 후: {cll}")
```

### 3. 원형 이중 연결리스트 (Circular Doubly Linked List)

#### 3-1 원형 이중 연결리스트 구현

```python
class CircularDoublyLinkedList:
    """원형 이중 연결리스트 클래스"""
    
    def __init__(self):
        """빈 원형 이중 연결리스트 생성"""
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
        new_node = DoublyListNode(val)
        
        if self.is_empty():
            new_node.next = new_node
            new_node.prev = new_node
            self.head = new_node
        else:
            tail = self.head.prev
            
            new_node.next = self.head
            new_node.prev = tail
            tail.next = new_node
            self.head.prev = new_node
        
        self.size += 1
    
    def prepend(self, val):
        """
        연결리스트 앞에 새 노드 추가
        
        Args:
            val: 추가할 값
        """
        new_node = DoublyListNode(val)
        
        if self.is_empty():
            new_node.next = new_node
            new_node.prev = new_node
            self.head = new_node
        else:
            tail = self.head.prev
            
            new_node.next = self.head
            new_node.prev = tail
            self.head.prev = new_node
            tail.next = new_node
            self.head = new_node
        
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
        
        current = self.head
        
        for _ in range(self.size):
            if current.val == val:
                if self.size == 1:
                    # 마지막 노드인 경우
                    self.head = None
                else:
                    # 이전과 다음 노드 연결
                    current.prev.next = current.next
                    current.next.prev = current.prev
                    
                    # 삭제된 노드가 head인 경우 head 업데이트
                    if current == self.head:
                        self.head = current.next
                
                self.size -= 1
                return True
            
            current = current.next
        
        return False
    
    def rotate_forward(self, steps=1):
        """앞으로 회전 (시계방향)"""
        if self.is_empty() or steps == 0:
            return
        
        steps = steps % self.size
        for _ in range(steps):
            self.head = self.head.next
    
    def rotate_backward(self, steps=1):
        """뒤로 회전 (반시계방향)"""
        if self.is_empty() or steps == 0:
            return
        
        steps = steps % self.size
        for _ in range(steps):
            self.head = self.head.prev
    
    def display(self):
        """연결리스트 전체 출력"""
        if self.is_empty():
            print("원형 이중 연결리스트가 비어있습니다")
            return
        
        values = []
        current = self.head
        
        for _ in range(self.size):
            values.append(str(current.val))
            current = current.next
        
        print(" ↔ ".join(values) + " ↔ (처음으로)")
    
    def display_reverse(self):
        """연결리스트 역방향 출력"""
        if self.is_empty():
            print("원형 이중 연결리스트가 비어있습니다")
            return
        
        values = []
        current = self.head.prev  # tail부터 시작
        
        for _ in range(self.size):
            values.append(str(current.val))
            current = current.prev
        
        print(" ↔ ".join(values) + " ↔ (처음으로)")
    
    def get_head(self):
        """첫 번째 노드 반환"""
        return self.head
    
    def get_tail(self):
        """마지막 노드 반환"""
        return self.head.prev if not self.is_empty() else None
    
    def length(self):
        """연결리스트의 길이 반환"""
        return self.size
    
    def to_list(self):
        """연결리스트를 Python 리스트로 변환"""
        if self.is_empty():
            return []
        
        result = []
        current = self.head
        
        for _ in range(self.size):
            result.append(current.val)
            current = current.next
        
        return result
    
    def __str__(self):
        """연결리스트의 문자열 표현"""
        if self.is_empty():
            return "CircularDoublyLinkedList([])"
        return f"CircularDoublyLinkedList({self.to_list()})"
    
    def __len__(self):
        """len() 함수 지원"""
        return self.size

# 사용 예시
if __name__ == "__main__":
    print("\n=== 원형 이중 연결리스트 테스트 ===")
    
    # 원형 이중 연결리스트 생성
    cdll = CircularDoublyLinkedList()
    
    # 값 추가
    cdll.append(1)
    cdll.append(2)
    cdll.append(3)
    cdll.prepend(0)
    
    print(f"원형 이중 연결리스트: {cdll}")
    print(f"길이: {len(cdll)}")
    
    # 순방향 출력
    print("순방향:")
    cdll.display()
    
    # 역방향 출력
    print("역방향:")
    cdll.display_reverse()
    
    # 회전 테스트
    print("앞으로 1단계 회전:")
    cdll.rotate_forward(1)
    cdll.display()
    
    print("뒤로 2단계 회전:")
    cdll.rotate_backward(2)
    cdll.display()
    
    # 값 삭제
    deleted = cdll.delete_by_value(2)
    print(f"값 2 삭제 결과: {deleted}, 삭제 후: {cdll}")
```

### 4. 성능 비교 및 최적화

#### 4-1 다양한 연결리스트 구현의 성능 비교

```python
import time
import random

def performance_comparison():
    """다양한 연결리스트 구현의 성능 비교"""
    sizes = [1000, 5000, 10000]
    
    for size in sizes:
        print(f"\n배열 크기: {size}")
        
        # 테스트 데이터 생성
        test_data = [random.randint(1, 1000) for _ in range(size)]
        
        # 단일 연결리스트 테스트
        sll = LinkedList()
        for val in test_data:
            sll.append(val)
        
        # 이중 연결리스트 테스트
        dll = DoublyLinkedList()
        for val in test_data:
            dll.append(val)
        
        # 원형 연결리스트 테스트
        cll = CircularLinkedList()
        for val in test_data:
            cll.append(val)
        
        # 접근 성능 비교 (중간 위치)
        start = time.time()
        _ = sll.get(size // 2)
        sll_access_time = time.time() - start
        
        start = time.time()
        _ = dll.get(size // 2)  # 최적화된 접근
        dll_access_time = time.time() - start
        
        start = time.time()
        # 원형 연결리스트는 직접 접근 불가, 순차 탐색
        current = cll.get_head()
        for _ in range(size // 2):
            current = current.next
        cll_access_time = time.time() - start
        
        print(f"중간 접근 - 단일: {sll_access_time:.6f}초, 이중: {dll_access_time:.6f}초, 원형: {cll_access_time:.6f}초")
        
        # 삭제 성능 비교 (중간 위치)
        start = time.time()
        sll.delete_by_index(size // 2)
        sll_delete_time = time.time() - start
        
        start = time.time()
        dll.delete_by_index(size // 2)
        dll_delete_time = time.time() - start
        
        start = time.time()
        cll.delete_by_value(test_data[size // 2])
        cll_delete_time = time.time() - start
        
        print(f"중간 삭제 - 단일: {sll_delete_time:.6f}초, 이중: {dll_delete_time:.6f}초, 원형: {cll_delete_time:.6f}초")

def memory_usage_comparison():
    """메모리 사용량 비교"""
    import sys
    
    size = 1000
    
    # 각 연결리스트 생성
    sll = LinkedList()
    dll = DoublyLinkedList()
    cll = CircularLinkedList()
    cdll = CircularDoublyLinkedList()
    
    for i in range(size):
        sll.append(i)
        dll.append(i)
        cll.append(i)
        cdll.append(i)
    
    # 메모리 사용량 측정
    sll_memory = sys.getsizeof(sll)
    dll_memory = sys.getsizeof(dll)
    cll_memory = sys.getsizeof(cll)
    cdll_memory = sys.getsizeof(cdll)
    
    print(f"\n=== 메모리 사용량 비교 (노드 {size}개) ===")
    print(f"단일 연결리스트: {sll_memory} bytes")
    print(f"이중 연결리스트: {dll_memory} bytes")
    print(f"원형 연결리스트: {cll_memory} bytes")
    print(f"원형 이중 연결리스트: {cdll_memory} bytes")
    
    # 노드당 평균 메모리 사용량
    print(f"\n=== 노드당 평균 메모리 사용량 ===")
    print(f"단일 연결리스트: {sll_memory / size:.2f} bytes/node")
    print(f"이중 연결리스트: {dll_memory / size:.2f} bytes/node")
    print(f"원형 연결리스트: {cll_memory / size:.2f} bytes/node")
    print(f"원형 이중 연결리스트: {cdll_memory / size:.2f} bytes/node")

# 성능 테스트 실행
if __name__ == "__main__":
    print("=== 성능 비교 테스트 ===")
    performance_comparison()
    
    print("\n=== 메모리 사용량 비교 ===")
    memory_usage_comparison()
```

#### 4-2 각 구현의 장단점 정리

| 구현 | 장점 | 단점 | 사용 사례 |
|------|------|------|-----------|
| **단일 연결리스트** | 메모리 효율적, 구현 간단 | 역방향 탐색 불가, 삭제 비효율 | 스택, 큐 구현, 단순한 리스트 |
| **이중 연결리스트** | 양방향 탐색, 삭제 효율적 | 메모리 오버헤드, 구현 복잡 | 편집기, 브라우저 히스토리 |
| **원형 연결리스트** | 순환 구조, 끝없는 탐색 | 끝 찾기 어려움, 복잡한 삭제 | 라운드 로빈, 음악 플레이리스트 |
| **원형 이중 연결리스트** | 모든 장점 결합 | 높은 메모리 오버헤드, 복잡성 | 고급 편집기, 게임 엔진 |

## 정리

이번 포스트에서는 연결리스트의 고급 구현들을 다뤘습니다:

**고급 연결리스트 구현**
1. **이중 연결리스트**: 양방향 탐색과 효율적인 삭제
2. **원형 연결리스트**: 순환 구조와 끝없는 탐색
3. **원형 이중 연결리스트**: 모든 장점을 결합한 고급 구현

**성능 특성**
- 🎯 **접근**: 이중 연결리스트의 최적화된 접근 (뒤에서부터 시작 가능)
- ⚡ **삭제**: 이중 연결리스트의 O(1) 삭제 (위치를 알고 있는 경우)
- 🔄 **회전**: 원형 구조의 효율적인 회전 연산
- 💾 **메모리**: 각 구현의 메모리 오버헤드 트레이드오프

**실무 적용 가이드**
- **단일 연결리스트**: 간단한 스택, 큐 구현
- **이중 연결리스트**: 편집기, 브라우저 히스토리 관리
- **원형 연결리스트**: 라운드 로빈 스케줄링, 음악 플레이리스트
- **원형 이중 연결리스트**: 고급 편집기, 게임 엔진의 노드 관리

**다음 학습 계획**
다음 포스트에서는 이러한 연결리스트들을 실제 프로젝트에 적용하는 방법을 알아보겠습니다:
- [[자료구조] 연결리스트(Linked List) 활용 - 3. 실무 활용]({% post_url 2025-10-04-[자료구조] 연결리스트(Linked List) 활용 - 3. 실무 활용 %}) - LRU 캐시, 편집기, 게임 엔진 등

# 📚 레퍼런스
- [Doubly Linked List - GeeksforGeeks](https://www.geeksforgeeks.org/doubly-linked-list/)
- [Circular Linked List - GeeksforGeeks](https://www.geeksforgeeks.org/circular-linked-list/)
- [Data Structures and Algorithms in Python - Goodrich](https://www.amazon.com/Data-Structures-Algorithms-Python-Goodrich/dp/1118290275)

---
*이 포스트는 자료구조 학습 과정을 정리한 내용입니다.*
