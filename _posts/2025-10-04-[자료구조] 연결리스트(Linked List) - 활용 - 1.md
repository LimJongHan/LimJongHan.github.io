---
layout: post
title: "[자료구조] 연결리스트(Linked List) - 활용 - 1"
date: 2025-10-04 10:40:00 +0900
categories: [자료구조, 연결리스트]
tags: [연결리스트, Linked List, 예제, 문제해결, 알고리즘, 코딩테스트]
author: LimJongHan
toc: true
---

# 📖 관련된 내 포스팅
- [[자료구조] 연결리스트(Linked List) - 기본 예제]({% post_url 2025-10-04-[자료구조] 연결리스트(Linked List) - 기본 예제 %})
- [[자료구조] 연결리스트(Linked List) - 활용 - 2]({% post_url 2025-10-04-[자료구조] 연결리스트(Linked List) - 활용 - 2 %})
- [[자료구조] 연결리스트(Linked List) - 활용 - 3]({% post_url 2025-10-04-[자료구조] 연결리스트(Linked List) - 활용 - 3 %})

# 📝 내용

## 개요

이전 포스트에서 연결리스트의 기본 개념과 구현에 대해 알아보았습니다. 이번 포스트에서는 연결리스트를 활용한 기본 문제 해결 예제들을 다뤄보겠습니다.

**이번 포스트에서 다룰 예제들:**
- 🔄 **연결리스트 역순 만들기**: 반복과 재귀 방법
- 📍 **연결리스트에서 중간 노드 찾기**: 두 포인터 기법
- 🔗 **두 연결리스트 병합하기**: 정렬된 연결리스트 병합
- 🧹 **연결리스트에서 중복 제거**: 정렬된/정렬되지 않은 연결리스트
- 🔍 **연결리스트가 회문인지 확인**: 스택과 두 포인터 활용
- 🔄 **연결리스트 사이클 감지**: 플로이드 순환 감지 알고리즘

이러한 예제들은 코딩 테스트나 면접에서 자주 등장하는 문제들로, 연결리스트 조작의 핵심 패턴을 학습할 수 있습니다.

## 핵심 내용

### 1. 연결리스트 역순 만들기

**문제**: 주어진 연결리스트를 역순으로 뒤집어보세요.

#### 1-1 반복 방법 (Iterative)

```python
def reverse_list_iterative(head):
    """
    연결리스트를 반복 방법으로 역순 만들기
    
    Args:
        head: 연결리스트의 헤드 노드
        
    Returns:
        역순으로 뒤집힌 연결리스트의 헤드 노드
    """
    prev = None
    current = head
    
    while current is not None:
        next_temp = current.next  # 다음 노드 임시 저장
        current.next = prev       # 현재 노드의 next를 이전 노드로 변경
        prev = current           # prev를 현재 노드로 이동
        current = next_temp      # current를 다음 노드로 이동
    
    return prev

# 테스트 함수
def test_reverse_list():
    """역순 만들기 테스트"""
    # 연결리스트 생성: 1 → 2 → 3 → 4 → 5
    head = ListNode(1)
    head.next = ListNode(2)
    head.next.next = ListNode(3)
    head.next.next.next = ListNode(4)
    head.next.next.next.next = ListNode(5)
    
    print("원본 연결리스트:")
    print_linked_list(head)
    
    # 역순 만들기
    reversed_head = reverse_list_iterative(head)
    
    print("역순 연결리스트:")
    print_linked_list(reversed_head)

def print_linked_list(head):
    """연결리스트 출력"""
    values = []
    current = head
    while current is not None:
        values.append(str(current.val))
        current = current.next
    print(" → ".join(values) if values else "빈 연결리스트")

# 시간복잡도: O(n), 공간복잡도: O(1)
```

#### 1-2 재귀 방법 (Recursive)

```python
def reverse_list_recursive(head):
    """
    연결리스트를 재귀 방법으로 역순 만들기
    
    Args:
        head: 연결리스트의 헤드 노드
        
    Returns:
        역순으로 뒤집힌 연결리스트의 헤드 노드
    """
    # 기본 케이스: 빈 리스트이거나 마지막 노드
    if head is None or head.next is None:
        return head
    
    # 재귀적으로 나머지 부분을 역순으로 만들기
    new_head = reverse_list_recursive(head.next)
    
    # 현재 노드의 다음 노드가 현재 노드를 가리키도록 설정
    head.next.next = head
    head.next = None
    
    return new_head

# 테스트
if __name__ == "__main__":
    print("=== 연결리스트 역순 만들기 ===")
    test_reverse_list()
    
    # 재귀 방법 테스트
    print("\n=== 재귀 방법 테스트 ===")
    head = ListNode(1)
    head.next = ListNode(2)
    head.next.next = ListNode(3)
    head.next.next.next = ListNode(4)
    
    print("원본:")
    print_linked_list(head)
    
    reversed_head = reverse_list_recursive(head)
    print("재귀 역순:")
    print_linked_list(reversed_head)

# 시간복잡도: O(n), 공간복잡도: O(n) - 재귀 스택 때문
```

### 2. 연결리스트에서 중간 노드 찾기

**문제**: 연결리스트의 중간 노드를 찾아보세요. 길이가 짝수인 경우 두 번째 중간 노드를 반환합니다.

```python
def find_middle_node(head):
    """
    연결리스트의 중간 노드 찾기 (두 포인터 기법)
    
    Args:
        head: 연결리스트의 헤드 노드
        
    Returns:
        중간 노드
    """
    if head is None:
        return None
    
    slow = head  # 한 번에 한 칸씩 이동
    fast = head  # 한 번에 두 칸씩 이동
    
    # fast가 끝에 도달하면 slow가 중간에 위치
    while fast.next is not None and fast.next.next is not None:
        slow = slow.next
        fast = fast.next.next
    
    return slow

def find_middle_node_second(head):
    """
    연결리스트의 중간 노드 찾기 (길이가 짝수일 때 두 번째 중간 노드)
    
    Args:
        head: 연결리스트의 헤드 노드
        
    Returns:
        중간 노드 (짝수 길이일 때 두 번째 중간 노드)
    """
    if head is None:
        return None
    
    slow = head
    fast = head
    
    # fast가 끝에 도달하면 slow가 중간에 위치
    while fast is not None and fast.next is not None:
        slow = slow.next
        fast = fast.next.next
    
    return slow

def test_find_middle():
    """중간 노드 찾기 테스트"""
    print("=== 중간 노드 찾기 테스트 ===")
    
    # 홀수 길이 연결리스트: 1 → 2 → 3 → 4 → 5
    head1 = ListNode(1)
    head1.next = ListNode(2)
    head1.next.next = ListNode(3)
    head1.next.next.next = ListNode(4)
    head1.next.next.next.next = ListNode(5)
    
    print("홀수 길이 연결리스트:")
    print_linked_list(head1)
    
    middle1 = find_middle_node(head1)
    print(f"첫 번째 방법 중간 노드: {middle1.val}")
    
    middle1_second = find_middle_node_second(head1)
    print(f"두 번째 방법 중간 노드: {middle1_second.val}")
    
    # 짝수 길이 연결리스트: 1 → 2 → 3 → 4
    head2 = ListNode(1)
    head2.next = ListNode(2)
    head2.next.next = ListNode(3)
    head2.next.next.next = ListNode(4)
    
    print("\n짝수 길이 연결리스트:")
    print_linked_list(head2)
    
    middle2 = find_middle_node(head2)
    print(f"첫 번째 방법 중간 노드: {middle2.val}")
    
    middle2_second = find_middle_node_second(head2)
    print(f"두 번째 방법 중간 노드: {middle2_second.val}")

# 시간복잡도: O(n), 공간복잡도: O(1)
```

### 3. 두 연결리스트 병합하기

**문제**: 두 개의 정렬된 연결리스트를 하나의 정렬된 연결리스트로 병합해보세요.

#### 3-1 기본 병합 방법

```python
def merge_two_lists(l1, l2):
    """
    두 개의 정렬된 연결리스트를 병합
    
    Args:
        l1: 첫 번째 정렬된 연결리스트
        l2: 두 번째 정렬된 연결리스트
        
    Returns:
        병합된 정렬된 연결리스트
    """
    # 더미 노드 생성 (결과 연결리스트의 시작점)
    dummy = ListNode(0)
    current = dummy
    
    # 두 연결리스트를 비교하면서 병합
    while l1 is not None and l2 is not None:
        if l1.val <= l2.val:
            current.next = l1
            l1 = l1.next
        else:
            current.next = l2
            l2 = l2.next
        current = current.next
    
    # 남은 노드들을 연결
    if l1 is not None:
        current.next = l1
    if l2 is not None:
        current.next = l2
    
    return dummy.next

def test_merge_lists():
    """연결리스트 병합 테스트"""
    print("=== 두 연결리스트 병합 테스트 ===")
    
    # 첫 번째 정렬된 연결리스트: 1 → 3 → 5
    l1 = ListNode(1)
    l1.next = ListNode(3)
    l1.next.next = ListNode(5)
    
    # 두 번째 정렬된 연결리스트: 2 → 4 → 6
    l2 = ListNode(2)
    l2.next = ListNode(4)
    l2.next.next = ListNode(6)
    
    print("첫 번째 연결리스트:")
    print_linked_list(l1)
    print("두 번째 연결리스트:")
    print_linked_list(l2)
    
    # 병합
    merged = merge_two_lists(l1, l2)
    print("병합된 연결리스트:")
    print_linked_list(merged)

# 시간복잡도: O(m + n), 공간복잡도: O(1)
```

#### 3-2 재귀 방법으로 병합

```python
def merge_two_lists_recursive(l1, l2):
    """
    재귀 방법으로 두 연결리스트 병합
    
    Args:
        l1: 첫 번째 정렬된 연결리스트
        l2: 두 번째 정렬된 연결리스트
        
    Returns:
        병합된 정렬된 연결리스트
    """
    # 기본 케이스
    if l1 is None:
        return l2
    if l2 is None:
        return l1
    
    # 재귀 케이스
    if l1.val <= l2.val:
        l1.next = merge_two_lists_recursive(l1.next, l2)
        return l1
    else:
        l2.next = merge_two_lists_recursive(l1, l2.next)
        return l2

def test_recursive_merge():
    """재귀 병합 테스트"""
    print("\n=== 재귀 방법 병합 테스트 ===")
    
    l1 = ListNode(1)
    l1.next = ListNode(2)
    l1.next.next = ListNode(4)
    
    l2 = ListNode(1)
    l2.next = ListNode(3)
    l2.next.next = ListNode(4)
    
    print("첫 번째 연결리스트:")
    print_linked_list(l1)
    print("두 번째 연결리스트:")
    print_linked_list(l2)
    
    merged = merge_two_lists_recursive(l1, l2)
    print("재귀 병합 결과:")
    print_linked_list(merged)

# 시간복잡도: O(m + n), 공간복잡도: O(m + n) - 재귀 스택
```

### 4. 연결리스트에서 중복 제거

**문제**: 연결리스트에서 중복된 값을 제거해보세요.

#### 4-1 정렬된 연결리스트에서 중복 제거

```python
def remove_duplicates_sorted(head):
    """
    정렬된 연결리스트에서 중복 제거
    
    Args:
        head: 정렬된 연결리스트의 헤드
        
    Returns:
        중복이 제거된 연결리스트의 헤드
    """
    if head is None:
        return None
    
    current = head
    
    while current.next is not None:
        if current.val == current.next.val:
            # 중복된 노드 건너뛰기
            current.next = current.next.next
        else:
            current = current.next
    
    return head

def test_remove_duplicates_sorted():
    """정렬된 연결리스트 중복 제거 테스트"""
    print("=== 정렬된 연결리스트 중복 제거 ===")
    
    # 정렬된 연결리스트: 1 → 1 → 2 → 3 → 3 → 3 → 4
    head = ListNode(1)
    head.next = ListNode(1)
    head.next.next = ListNode(2)
    head.next.next.next = ListNode(3)
    head.next.next.next.next = ListNode(3)
    head.next.next.next.next.next = ListNode(3)
    head.next.next.next.next.next.next = ListNode(4)
    
    print("원본 연결리스트:")
    print_linked_list(head)
    
    # 중복 제거
    cleaned = remove_duplicates_sorted(head)
    print("중복 제거 후:")
    print_linked_list(cleaned)

# 시간복잡도: O(n), 공간복잡도: O(1)
```

#### 4-2 정렬되지 않은 연결리스트에서 중복 제거

```python
def remove_duplicates_unsorted(head):
    """
    정렬되지 않은 연결리스트에서 중복 제거 (해시셋 사용)
    
    Args:
        head: 연결리스트의 헤드
        
    Returns:
        중복이 제거된 연결리스트의 헤드
    """
    if head is None:
        return None
    
    seen = set()
    seen.add(head.val)
    
    current = head
    
    while current.next is not None:
        if current.next.val in seen:
            # 중복된 노드 제거
            current.next = current.next.next
        else:
            seen.add(current.next.val)
            current = current.next
    
    return head

def remove_duplicates_unsorted_inplace(head):
    """
    정렬되지 않은 연결리스트에서 중복 제거 (제자리 방법)
    
    Args:
        head: 연결리스트의 헤드
        
    Returns:
        중복이 제거된 연결리스트의 헤드
    """
    if head is None:
        return None
    
    current = head
    
    while current is not None:
        # current 노드와 같은 값을 가진 모든 노드 제거
        runner = current
        while runner.next is not None:
            if runner.next.val == current.val:
                runner.next = runner.next.next
            else:
                runner = runner.next
        current = current.next
    
    return head

def test_remove_duplicates_unsorted():
    """정렬되지 않은 연결리스트 중복 제거 테스트"""
    print("\n=== 정렬되지 않은 연결리스트 중복 제거 ===")
    
    # 정렬되지 않은 연결리스트: 1 → 2 → 1 → 3 → 2 → 4 → 1
    head = ListNode(1)
    head.next = ListNode(2)
    head.next.next = ListNode(1)
    head.next.next.next = ListNode(3)
    head.next.next.next.next = ListNode(2)
    head.next.next.next.next.next = ListNode(4)
    head.next.next.next.next.next.next = ListNode(1)
    
    print("원본 연결리스트:")
    print_linked_list(head)
    
    # 해시셋 사용 방법
    cleaned1 = remove_duplicates_unsorted(head)
    print("해시셋 사용 중복 제거 후:")
    print_linked_list(cleaned1)
    
    # 제자리 방법 (원본 복원 후 테스트)
    head = ListNode(1)
    head.next = ListNode(2)
    head.next.next = ListNode(1)
    head.next.next.next = ListNode(3)
    head.next.next.next.next = ListNode(2)
    head.next.next.next.next.next = ListNode(4)
    head.next.next.next.next.next.next = ListNode(1)
    
    cleaned2 = remove_duplicates_unsorted_inplace(head)
    print("제자리 방법 중복 제거 후:")
    print_linked_list(cleaned2)

# 해시셋 방법: 시간복잡도 O(n), 공간복잡도 O(n)
# 제자리 방법: 시간복잡도 O(n²), 공간복잡도 O(1)
```

### 5. 연결리스트가 회문인지 확인

**문제**: 연결리스트가 회문(앞뒤가 같은)인지 확인해보세요.

#### 5-1 스택을 사용한 방법

```python
def is_palindrome_stack(head):
    """
    스택을 사용하여 연결리스트가 회문인지 확인
    
    Args:
        head: 연결리스트의 헤드
        
    Returns:
        bool: 회문이면 True, 아니면 False
    """
    if head is None:
        return True
    
    # 모든 값을 스택에 저장
    stack = []
    current = head
    
    while current is not None:
        stack.append(current.val)
        current = current.next
    
    # 스택에서 값을 꺼내면서 원본과 비교
    current = head
    while current is not None:
        if current.val != stack.pop():
            return False
        current = current.next
    
    return True

def test_palindrome_stack():
    """스택 방법 회문 확인 테스트"""
    print("\n=== 스택 방법 회문 확인 ===")
    
    # 회문 연결리스트: 1 → 2 → 3 → 2 → 1
    head1 = ListNode(1)
    head1.next = ListNode(2)
    head1.next.next = ListNode(3)
    head1.next.next.next = ListNode(2)
    head1.next.next.next.next = ListNode(1)
    
    print("연결리스트:")
    print_linked_list(head1)
    print(f"회문 여부: {is_palindrome_stack(head1)}")
    
    # 회문이 아닌 연결리스트: 1 → 2 → 3 → 4 → 5
    head2 = ListNode(1)
    head2.next = ListNode(2)
    head2.next.next = ListNode(3)
    head2.next.next.next = ListNode(4)
    head2.next.next.next.next = ListNode(5)
    
    print("\n연결리스트:")
    print_linked_list(head2)
    print(f"회문 여부: {is_palindrome_stack(head2)}")

# 시간복잡도: O(n), 공간복잡도: O(n)
```

#### 5-2 두 포인터와 역순을 사용한 방법

```python
def is_palindrome_optimized(head):
    """
    두 포인터와 역순을 사용하여 연결리스트가 회문인지 확인 (공간 최적화)
    
    Args:
        head: 연결리스트의 헤드
        
    Returns:
        bool: 회문이면 True, 아니면 False
    """
    if head is None or head.next is None:
        return True
    
    # 중간 노드 찾기
    slow = head
    fast = head
    
    while fast.next is not None and fast.next.next is not None:
        slow = slow.next
        fast = fast.next.next
    
    # 두 번째 절반을 역순으로 만들기
    second_half = reverse_list_iterative(slow.next)
    first_half = head
    
    # 두 절반 비교
    while second_half is not None:
        if first_half.val != second_half.val:
            return False
        first_half = first_half.next
        second_half = second_half.next
    
    return True

def test_palindrome_optimized():
    """최적화된 회문 확인 테스트"""
    print("\n=== 최적화된 회문 확인 ===")
    
    # 회문 연결리스트: 1 → 2 → 3 → 2 → 1
    head1 = ListNode(1)
    head1.next = ListNode(2)
    head1.next.next = ListNode(3)
    head1.next.next.next = ListNode(2)
    head1.next.next.next.next = ListNode(1)
    
    print("연결리스트:")
    print_linked_list(head1)
    print(f"회문 여부: {is_palindrome_optimized(head1)}")
    
    # 짝수 길이 회문: 1 → 2 → 2 → 1
    head2 = ListNode(1)
    head2.next = ListNode(2)
    head2.next.next = ListNode(2)
    head2.next.next.next = ListNode(1)
    
    print("\n연결리스트:")
    print_linked_list(head2)
    print(f"회문 여부: {is_palindrome_optimized(head2)}")

# 시간복잡도: O(n), 공간복잡도: O(1)
```

### 6. 연결리스트 사이클 감지

**문제**: 연결리스트에 사이클(순환)이 있는지 확인해보세요.

```python
def has_cycle(head):
    """
    연결리스트에 사이클이 있는지 확인 (플로이드 순환 감지 알고리즘)
    
    Args:
        head: 연결리스트의 헤드
        
    Returns:
        bool: 사이클이 있으면 True, 없으면 False
    """
    if head is None or head.next is None:
        return False
    
    slow = head  # 한 번에 한 칸씩 이동
    fast = head  # 한 번에 두 칸씩 이동
    
    # fast가 끝에 도달하거나 slow와 만날 때까지 반복
    while fast is not None and fast.next is not None:
        slow = slow.next
        fast = fast.next.next
        
        if slow == fast:
            return True
    
    return False

def find_cycle_start(head):
    """
    사이클이 있는 경우 사이클의 시작점 찾기
    
    Args:
        head: 연결리스트의 헤드
        
    Returns:
        사이클의 시작 노드 (사이클이 없으면 None)
    """
    if head is None or head.next is None:
        return None
    
    # 첫 번째 단계: slow와 fast가 만나는 지점 찾기
    slow = head
    fast = head
    
    while fast is not None and fast.next is not None:
        slow = slow.next
        fast = fast.next.next
        
        if slow == fast:
            break
    else:
        return None  # 사이클이 없음
    
    # 두 번째 단계: 사이클의 시작점 찾기
    slow = head
    while slow != fast:
        slow = slow.next
        fast = fast.next
    
    return slow

def test_cycle_detection():
    """사이클 감지 테스트"""
    print("\n=== 사이클 감지 테스트 ===")
    
    # 사이클이 없는 연결리스트: 1 → 2 → 3 → 4 → 5
    head1 = ListNode(1)
    head1.next = ListNode(2)
    head1.next.next = ListNode(3)
    head1.next.next.next = ListNode(4)
    head1.next.next.next.next = ListNode(5)
    
    print("사이클 없는 연결리스트:")
    print(f"사이클 존재: {has_cycle(head1)}")
    
    # 사이클이 있는 연결리스트: 1 → 2 → 3 → 4 → 2 (4에서 2로 연결)
    head2 = ListNode(1)
    head2.next = ListNode(2)
    head2.next.next = ListNode(3)
    head2.next.next.next = ListNode(4)
    head2.next.next.next.next = head2.next  # 사이클 생성
    
    print("사이클 있는 연결리스트:")
    print(f"사이클 존재: {has_cycle(head2)}")
    
    if has_cycle(head2):
        cycle_start = find_cycle_start(head2)
        print(f"사이클 시작점: {cycle_start.val}")

# 시간복잡도: O(n), 공간복잡도: O(1)
```

### 7. 종합 테스트

```python
def run_all_tests():
    """모든 테스트 실행"""
    print("=" * 50)
    print("연결리스트 예제 종합 테스트")
    print("=" * 50)
    
    # 각 테스트 실행
    test_reverse_list()
    test_find_middle()
    test_merge_lists()
    test_recursive_merge()
    test_remove_duplicates_sorted()
    test_remove_duplicates_unsorted()
    test_palindrome_stack()
    test_palindrome_optimized()
    test_cycle_detection()

if __name__ == "__main__":
    run_all_tests()
```

## 정리

이번 포스트에서는 연결리스트를 활용한 기본 문제 해결 예제들을 다뤘습니다:

**핵심 문제 해결 패턴**
1. **역순 만들기**: 반복과 재귀 방법으로 포인터 조작
2. **중간 노드 찾기**: 두 포인터 기법으로 효율적 탐색
3. **연결리스트 병합**: 정렬된 연결리스트들의 효율적 병합
4. **중복 제거**: 정렬된/정렬되지 않은 연결리스트에서 중복 처리
5. **회문 확인**: 스택과 역순 방법으로 공간-시간 트레이드오프
6. **사이클 감지**: 플로이드 순환 감지 알고리즘으로 효율적 탐지

**알고리즘 설계 원칙**
- 🎯 **두 포인터 기법**: slow/fast 포인터로 효율적 탐색
- 🔄 **재귀 vs 반복**: 공간 복잡도와 가독성의 트레이드오프
- 📊 **최적화**: 공간 복잡도를 줄이는 제자리(in-place) 알고리즘
- ⚡ **효율성**: 각 문제의 특성에 맞는 최적의 해결 방법

**실무 적용 포인트**
- 코딩 테스트와 면접에서 자주 등장하는 핵심 패턴
- 연결리스트 조작의 기본적인 알고리즘 설계 능력
- 메모리 효율성과 시간 복잡도의 균형 고려

**다음 학습 계획**
다음 포스트에서는 더 고급 연결리스트 구현들에 대해 알아보겠습니다:
- [[자료구조] 연결리스트(Linked List) - 활용 - 2]({% post_url 2025-10-04-[자료구조] 연결리스트(Linked List) - 활용 - 2 %}) - 이중 연결리스트, 원형 연결리스트 등

# 📚 레퍼런스
- [Linked List Problems - LeetCode](https://leetcode.com/tag/linked-list/)
- [Floyd's Cycle Detection Algorithm - GeeksforGeeks](https://www.geeksforgeeks.org/floyds-cycle-finding-algorithm/)
- [Two Pointers Technique - GeeksforGeeks](https://www.geeksforgeeks.org/two-pointers-technique/)

---
*이 포스트는 자료구조 학습 과정을 정리한 내용입니다.*
