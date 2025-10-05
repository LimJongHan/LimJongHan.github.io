---
layout: post
title: "[자료구조] 연결리스트(Linked List) - 활용 - 3"
date: 2025-10-04 10:50:00 +0900
categories: [자료구조, 연결리스트]
tags: [연결리스트, Linked List, 실무활용, LRU캐시, 편집기, 게임엔진, 시스템설계]
author: LimJongHan
toc: true
---

# 📖 관련된 내 포스팅
- [[자료구조] 연결리스트(Linked List) - 기본 예제]({% post_url 2025-10-04-[자료구조] 연결리스트(Linked List) - 기본 예제 %})
- [[자료구조] 연결리스트(Linked List) - 활용 - 1]({% post_url 2025-10-04-[자료구조] 연결리스트(Linked List) - 활용 - 1 %})
- [[자료구조] 연결리스트(Linked List) - 활용 - 2]({% post_url 2025-10-04-[자료구조] 연결리스트(Linked List) - 활용 - 2 %})

# 📝 내용

## 개요

이전 포스트들에서 연결리스트의 기본 개념, 문제 해결 예제, 그리고 고급 구현들을 다뤘습니다. 이번 포스트에서는 이러한 지식들을 실제 프로젝트와 실무 상황에 적용하는 방법을 알아보겠습니다.

**이번 포스트에서 다룰 실무 활용 분야:**
- 🚀 **LRU 캐시 시스템**: 이중 연결리스트를 활용한 고성능 캐시
- ✏️ **텍스트 편집기**: 이중 연결리스트로 구현한 문서 편집 시스템
- 🎮 **게임 엔진**: 연결리스트를 활용한 게임 오브젝트 관리
- 🎵 **음악 플레이리스트**: 원형 연결리스트로 구현한 미디어 플레이어
- 🔄 **실행 취소/다시 실행**: 연결리스트를 활용한 상태 관리 시스템
- 🏗️ **메모리 풀**: 효율적인 메모리 관리를 위한 노드 풀 시스템

실무에서는 단순한 자료구조 구현을 넘어서 비즈니스 요구사항과 성능을 모두 고려한 설계가 필요합니다.

## 핵심 내용

### 1. LRU 캐시 시스템 구현

#### 1-1 LRU 캐시의 개념과 설계

**LRU (Least Recently Used) 캐시**란?
- 가장 오랫동안 사용되지 않은 항목을 제거하는 캐시 정책
- 이중 연결리스트와 해시맵을 결합하여 O(1) 시간에 구현 가능
- 웹 브라우저, 데이터베이스, 운영체제 등에서 널리 사용

**설계 원칙:**
- 해시맵으로 O(1) 접근 시간 보장
- 이중 연결리스트로 O(1) 삽입/삭제 시간 보장
- 가장 최근 사용된 항목을 리스트의 앞쪽에 배치

```python
class LRUCache:
    """이중 연결리스트를 활용한 LRU 캐시"""
    
    def __init__(self, capacity: int):
        """
        LRU 캐시 초기화
        
        Args:
            capacity: 캐시의 최대 용량
        """
        self.capacity = capacity
        self.cache = {}  # key -> node 매핑
        
        # 더미 노드로 리스트 초기화 (경계 조건 처리 간소화)
        self.head = DoublyListNode(0, 0)  # 가장 최근 사용
        self.tail = DoublyListNode(0, 0)  # 가장 오래된 사용
        
        self.head.next = self.tail
        self.tail.prev = self.head
    
    def get(self, key: int) -> int:
        """
        캐시에서 값 조회
        
        Args:
            key: 조회할 키
            
        Returns:
            값 (없으면 -1)
        """
        if key in self.cache:
            # 노드를 가장 앞으로 이동 (가장 최근 사용)
            node = self.cache[key]
            self._move_to_head(node)
            return node.val
        
        return -1
    
    def put(self, key: int, value: int) -> None:
        """
        캐시에 값 저장
        
        Args:
            key: 저장할 키
            value: 저장할 값
        """
        if key in self.cache:
            # 기존 키 업데이트
            node = self.cache[key]
            node.val = value
            self._move_to_head(node)
        else:
            # 새 노드 생성
            new_node = DoublyListNode(key, value)
            
            if len(self.cache) >= self.capacity:
                # 가장 오래된 노드 제거
                tail = self._pop_tail()
                del self.cache[tail.key]
            
            # 새 노드를 앞에 추가
            self.cache[key] = new_node
            self._add_node(new_node)
    
    def _add_node(self, node):
        """노드를 헤드 바로 다음에 추가"""
        node.prev = self.head
        node.next = self.head.next
        
        self.head.next.prev = node
        self.head.next = node
    
    def _remove_node(self, node):
        """노드를 리스트에서 제거"""
        prev_node = node.prev
        next_node = node.next
        
        prev_node.next = next_node
        next_node.prev = prev_node
    
    def _move_to_head(self, node):
        """노드를 헤드로 이동 (가장 최근 사용)"""
        self._remove_node(node)
        self._add_node(node)
    
    def _pop_tail(self):
        """가장 오래된 노드 제거하고 반환"""
        last_node = self.tail.prev
        self._remove_node(last_node)
        return last_node
    
    def display(self):
        """캐시 상태 출력"""
        print("LRU 캐시 상태:")
        current = self.head.next
        while current != self.tail:
            print(f"  {current.key}: {current.val}")
            current = current.next
    
    def get_statistics(self) -> dict:
        """캐시 통계 정보"""
        return {
            'size': len(self.cache),
            'capacity': self.capacity,
            'utilization': len(self.cache) / self.capacity,
            'head_key': self.head.next.key if self.head.next != self.tail else None,
            'tail_key': self.tail.prev.key if self.tail.prev != self.head else None
        }

# LRU 캐시 노드 클래스 수정
class DoublyListNode:
    """이중 연결리스트 노드 (LRU 캐시용)"""
    
    def __init__(self, key=0, val=0, prev=None, next=None):
        self.key = key
        self.val = val
        self.prev = prev
        self.next = next

# 사용 예시
if __name__ == "__main__":
    print("=== LRU 캐시 시스템 테스트 ===")
    
    # 캐시 생성 (용량 3)
    cache = LRUCache(3)
    
    # 값 저장
    cache.put(1, "첫 번째")
    cache.put(2, "두 번째")
    cache.put(3, "세 번째")
    
    print("초기 상태:")
    cache.display()
    
    # 값 조회
    print(f"\n키 2 조회: {cache.get(2)}")
    print("조회 후 상태:")
    cache.display()
    
    # 용량 초과 시 오래된 항목 제거
    cache.put(4, "네 번째")
    print("\n용량 초과 후 상태:")
    cache.display()
    
    # 통계 정보
    stats = cache.get_statistics()
    print(f"\n캐시 통계: {stats}")
```

### 2. 텍스트 편집기 구현

#### 2-1 문서 편집 시스템

```python
class TextEditor:
    """이중 연결리스트를 활용한 텍스트 편집기"""
    
    def __init__(self):
        """빈 문서로 편집기 초기화"""
        # 더미 노드로 리스트 초기화
        self.head = DoublyListNode("")
        self.tail = DoublyListNode("")
        
        self.head.next = self.tail
        self.tail.prev = self.head
        
        self.cursor = self.tail  # 커서 위치 (문자 사이)
        self.clipboard = []      # 클립보드
    
    def insert_text(self, text: str) -> None:
        """
        커서 위치에 텍스트 삽입
        
        Args:
            text: 삽입할 텍스트
        """
        for char in text:
            new_node = DoublyListNode(char)
            self._insert_before_cursor(new_node)
    
    def delete_char(self) -> str:
        """
        커서 앞의 문자 삭제
        
        Returns:
            삭제된 문자 (없으면 빈 문자열)
        """
        if self.cursor.prev != self.head:
            char = self.cursor.prev.val
            self._remove_node(self.cursor.prev)
            return char
        return ""
    
    def delete_word(self) -> str:
        """
        커서 앞의 단어 삭제
        
        Returns:
            삭제된 단어
        """
        deleted = []
        
        # 공백 문자 건너뛰기
        while (self.cursor.prev != self.head and 
               self.cursor.prev.val == ' '):
            deleted.insert(0, self.delete_char())
        
        # 단어 문자들 삭제
        while (self.cursor.prev != self.head and 
               self.cursor.prev.val != ' '):
            deleted.insert(0, self.delete_char())
        
        return ''.join(deleted)
    
    def move_cursor_left(self) -> bool:
        """커서를 왼쪽으로 이동"""
        if self.cursor.prev != self.head:
            self.cursor = self.cursor.prev
            return True
        return False
    
    def move_cursor_right(self) -> bool:
        """커서를 오른쪽으로 이동"""
        if self.cursor != self.tail:
            self.cursor = self.cursor.next
            return True
        return False
    
    def move_cursor_to_beginning(self):
        """커서를 문서 시작으로 이동"""
        self.cursor = self.head.next
    
    def move_cursor_to_end(self):
        """커서를 문서 끝으로 이동"""
        self.cursor = self.tail
    
    def copy_selection(self, start: int, end: int) -> str:
        """
        선택된 텍스트 복사
        
        Args:
            start: 시작 위치
            end: 끝 위치
            
        Returns:
            복사된 텍스트
        """
        # 임시로 커서를 시작 위치로 이동
        original_cursor = self.cursor
        self._move_cursor_to_position(start)
        
        copied_text = []
        current = self.cursor
        
        for _ in range(end - start):
            if current != self.tail:
                copied_text.append(current.val)
                current = current.next
        
        # 커서 원래 위치로 복원
        self.cursor = original_cursor
        
        copied = ''.join(copied_text)
        self.clipboard = list(copied)
        return copied
    
    def paste(self) -> None:
        """클립보드 내용 붙여넣기"""
        for char in self.clipboard:
            new_node = DoublyListNode(char)
            self._insert_before_cursor(new_node)
    
    def undo(self):
        """실행 취소 (간단한 버전)"""
        # 실제 구현에서는 명령 히스토리를 관리해야 함
        pass
    
    def redo(self):
        """다시 실행 (간단한 버전)"""
        # 실제 구현에서는 명령 히스토리를 관리해야 함
        pass
    
    def _insert_before_cursor(self, node):
        """커서 앞에 노드 삽입"""
        node.prev = self.cursor.prev
        node.next = self.cursor
        
        self.cursor.prev.next = node
        self.cursor.prev = node
    
    def _remove_node(self, node):
        """노드 제거"""
        node.prev.next = node.next
        node.next.prev = node.prev
    
    def _move_cursor_to_position(self, position: int):
        """커서를 지정된 위치로 이동"""
        self.cursor = self.head.next
        for _ in range(position):
            if self.cursor != self.tail:
                self.cursor = self.cursor.next
    
    def get_text(self) -> str:
        """전체 문서 내용 반환"""
        text = []
        current = self.head.next
        while current != self.tail:
            text.append(current.val)
            current = current.next
        return ''.join(text)
    
    def get_cursor_position(self) -> int:
        """현재 커서 위치 반환"""
        position = 0
        current = self.head.next
        while current != self.cursor:
            position += 1
            current = current.next
        return position
    
    def display_with_cursor(self) -> str:
        """커서가 표시된 문서 내용 반환"""
        text = []
        current = self.head.next
        
        while current != self.tail:
            text.append(current.val)
            current = current.next
        
        # 커서 위치에 | 삽입
        cursor_pos = self.get_cursor_position()
        result = ''.join(text)
        return result[:cursor_pos] + '|' + result[cursor_pos:]
    
    def get_line_count(self) -> int:
        """문서의 줄 수 반환"""
        line_count = 1
        current = self.head.next
        while current != self.tail:
            if current.val == '\n':
                line_count += 1
            current = current.next
        return line_count
    
    def get_word_count(self) -> int:
        """문서의 단어 수 반환"""
        word_count = 0
        in_word = False
        current = self.head.next
        
        while current != self.tail:
            if current.val.isalnum():
                if not in_word:
                    word_count += 1
                    in_word = True
            else:
                in_word = False
            current = current.next
        
        return word_count

# 사용 예시
if __name__ == "__main__":
    print("\n=== 텍스트 편집기 테스트 ===")
    
    editor = TextEditor()
    
    # 텍스트 삽입
    editor.insert_text("Hello, World!")
    print(f"텍스트 삽입 후: {editor.display_with_cursor()}")
    
    # 커서 이동
    editor.move_cursor_left()
    editor.move_cursor_left()
    editor.move_cursor_left()
    print(f"커서 왼쪽 이동 후: {editor.display_with_cursor()}")
    
    # 문자 삭제
    deleted = editor.delete_char()
    print(f"문자 삭제 ({deleted}) 후: {editor.display_with_cursor()}")
    
    # 텍스트 추가
    editor.insert_text(" Python")
    print(f"텍스트 추가 후: {editor.display_with_cursor()}")
    
    # 문서 정보
    print(f"\n문서 정보:")
    print(f"  전체 텍스트: {editor.get_text()}")
    print(f"  커서 위치: {editor.get_cursor_position()}")
    print(f"  단어 수: {editor.get_word_count()}")
    
    # 텍스트 복사 및 붙여넣기
    editor.move_cursor_to_beginning()
    copied = editor.copy_selection(0, 5)  # "Hello" 복사
    print(f"\n복사된 텍스트: {copied}")
    
    editor.move_cursor_to_end()
    editor.paste()
    print(f"붙여넣기 후: {editor.display_with_cursor()}")
```

### 3. 게임 엔진의 오브젝트 관리

#### 3-1 게임 오브젝트 관리 시스템

```python
import time
from enum import Enum
from typing import List, Optional

class GameObjectType(Enum):
    """게임 오브젝트 타입"""
    PLAYER = "player"
    ENEMY = "enemy"
    PROJECTILE = "projectile"
    POWERUP = "powerup"
    OBSTACLE = "obstacle"

class GameObject:
    """게임 오브젝트 클래스"""
    
    def __init__(self, obj_id: str, obj_type: GameObjectType, x: float = 0, y: float = 0):
        """
        게임 오브젝트 초기화
        
        Args:
            obj_id: 오브젝트 고유 ID
            obj_type: 오브젝트 타입
            x: X 좌표
            y: Y 좌표
        """
        self.id = obj_id
        self.type = obj_type
        self.x = x
        self.y = y
        self.active = True
        self.created_at = time.time()
        self.last_updated = time.time()
        
        # 연결리스트 노드 포인터
        self.list_node = None
    
    def update(self, delta_time: float):
        """오브젝트 업데이트"""
        self.last_updated = time.time()
    
    def render(self):
        """오브젝트 렌더링"""
        print(f"{self.type.value} {self.id} at ({self.x:.1f}, {self.y:.1f})")
    
    def destroy(self):
        """오브젝트 파괴"""
        self.active = False

class GameObjectManager:
    """게임 오브젝트 관리자"""
    
    def __init__(self):
        """게임 오브젝트 관리자 초기화"""
        self.objects = {}  # id -> GameObject 매핑
        
        # 타입별 연결리스트
        self.object_lists = {
            GameObjectType.PLAYER: LinkedList(),
            GameObjectType.ENEMY: LinkedList(),
            GameObjectType.PROJECTILE: LinkedList(),
            GameObjectType.POWERUP: LinkedList(),
            GameObjectType.OBSTACLE: LinkedList()
        }
        
        # 활성 오브젝트 리스트 (모든 타입 포함)
        self.active_objects = LinkedList()
        
        self.next_id = 1
    
    def create_object(self, obj_type: GameObjectType, x: float = 0, y: float = 0) -> str:
        """
        새 게임 오브젝트 생성
        
        Args:
            obj_type: 오브젝트 타입
            x: X 좌표
            y: Y 좌표
            
        Returns:
            생성된 오브젝트의 ID
        """
        obj_id = f"{obj_type.value}_{self.next_id}"
        self.next_id += 1
        
        # 게임 오브젝트 생성
        game_object = GameObject(obj_id, obj_type, x, y)
        
        # 매핑에 추가
        self.objects[obj_id] = game_object
        
        # 타입별 리스트에 추가
        self.object_lists[obj_type].append(obj_id)
        
        # 활성 오브젝트 리스트에 추가
        self.active_objects.append(obj_id)
        
        return obj_id
    
    def destroy_object(self, obj_id: str) -> bool:
        """
        게임 오브젝트 파괴
        
        Args:
            obj_id: 파괴할 오브젝트 ID
            
        Returns:
            파괴 성공 여부
        """
        if obj_id not in self.objects:
            return False
        
        game_object = self.objects[obj_id]
        
        # 오브젝트 비활성화
        game_object.destroy()
        
        # 매핑에서 제거
        del self.objects[obj_id]
        
        # 타입별 리스트에서 제거
        self.object_lists[game_object.type].delete_by_value(obj_id)
        
        # 활성 오브젝트 리스트에서 제거
        self.active_objects.delete_by_value(obj_id)
        
        return True
    
    def get_object(self, obj_id: str) -> Optional[GameObject]:
        """오브젝트 조회"""
        return self.objects.get(obj_id)
    
    def get_objects_by_type(self, obj_type: GameObjectType) -> List[GameObject]:
        """타입별 오브젝트 목록 조회"""
        objects = []
        obj_list = self.object_lists[obj_type]
        
        for i in range(obj_list.length()):
            obj_id = obj_list.get(i)
            if obj_id in self.objects:
                objects.append(self.objects[obj_id])
        
        return objects
    
    def get_all_active_objects(self) -> List[GameObject]:
        """모든 활성 오브젝트 조회"""
        objects = []
        
        for i in range(self.active_objects.length()):
            obj_id = self.active_objects.get(i)
            if obj_id in self.objects:
                objects.append(self.objects[obj_id])
        
        return objects
    
    def update_all_objects(self, delta_time: float):
        """모든 활성 오브젝트 업데이트"""
        objects_to_remove = []
        
        for i in range(self.active_objects.length()):
            obj_id = self.active_objects.get(i)
            if obj_id in self.objects:
                game_object = self.objects[obj_id]
                if game_object.active:
                    game_object.update(delta_time)
                else:
                    objects_to_remove.append(obj_id)
        
        # 비활성화된 오브젝트들 제거
        for obj_id in objects_to_remove:
            self.destroy_object(obj_id)
    
    def render_all_objects(self):
        """모든 활성 오브젝트 렌더링"""
        for game_object in self.get_all_active_objects():
            if game_object.active:
                game_object.render()
    
    def get_statistics(self) -> dict:
        """오브젝트 관리자 통계"""
        stats = {
            'total_objects': len(self.objects),
            'active_objects': self.active_objects.length(),
            'by_type': {}
        }
        
        for obj_type, obj_list in self.object_lists.items():
            stats['by_type'][obj_type.value] = obj_list.length()
        
        return stats

# 사용 예시
if __name__ == "__main__":
    print("\n=== 게임 오브젝트 관리 시스템 테스트 ===")
    
    # 게임 오브젝트 관리자 생성
    manager = GameObjectManager()
    
    # 오브젝트 생성
    player_id = manager.create_object(GameObjectType.PLAYER, 0, 0)
    enemy1_id = manager.create_object(GameObjectType.ENEMY, 10, 5)
    enemy2_id = manager.create_object(GameObjectType.ENEMY, 15, 8)
    projectile_id = manager.create_object(GameObjectType.PROJECTILE, 5, 2)
    powerup_id = manager.create_object(GameObjectType.POWERUP, 20, 10)
    
    print("생성된 오브젝트들:")
    manager.render_all_objects()
    
    # 타입별 오브젝트 조회
    enemies = manager.get_objects_by_type(GameObjectType.ENEMY)
    print(f"\n적 오브젝트 수: {len(enemies)}")
    for enemy in enemies:
        print(f"  {enemy.id} at ({enemy.x}, {enemy.y})")
    
    # 오브젝트 업데이트
    print("\n오브젝트 업데이트:")
    manager.update_all_objects(0.016)  # 60 FPS
    
    # 통계 정보
    stats = manager.get_statistics()
    print(f"\n오브젝트 관리자 통계: {stats}")
    
    # 오브젝트 파괴
    print(f"\n오브젝트 {enemy1_id} 파괴:")
    manager.destroy_object(enemy1_id)
    
    print("파괴 후 오브젝트들:")
    manager.render_all_objects()
    
    # 최종 통계
    final_stats = manager.get_statistics()
    print(f"\n최종 통계: {final_stats}")
```

### 4. 음악 플레이리스트 시스템

#### 4-1 미디어 플레이어 구현

```python
class Song:
    """음악 트랙 클래스"""
    
    def __init__(self, title: str, artist: str, duration: int, file_path: str):
        """
        음악 트랙 초기화
        
        Args:
            title: 곡 제목
            artist: 아티스트
            duration: 재생 시간 (초)
            file_path: 파일 경로
        """
        self.title = title
        self.artist = artist
        self.duration = duration
        self.file_path = file_path
        self.play_count = 0
        self.last_played = None
    
    def play(self):
        """곡 재생"""
        self.play_count += 1
        self.last_played = time.time()
        print(f"🎵 재생 중: {self.artist} - {self.title} ({self.duration}초)")
    
    def __str__(self):
        return f"{self.artist} - {self.title} ({self.duration}초)"

class MusicPlaylist:
    """원형 연결리스트를 활용한 음악 플레이리스트"""
    
    def __init__(self, name: str):
        """
        플레이리스트 초기화
        
        Args:
            name: 플레이리스트 이름
        """
        self.name = name
        self.playlist = CircularDoublyLinkedList()
        self.current_song = None
        self.is_playing = False
        self.play_mode = "normal"  # normal, repeat_one, repeat_all, shuffle
        self.volume = 50
    
    def add_song(self, song: Song) -> None:
        """플레이리스트에 곡 추가"""
        self.playlist.append(song)
        if self.current_song is None:
            self.current_song = self.playlist.get_head()
    
    def remove_current_song(self) -> bool:
        """현재 곡을 플레이리스트에서 제거"""
        if self.current_song is None:
            return False
        
        # 현재 곡이 플레이리스트의 첫 번째 곡인지 확인
        if self.current_song == self.playlist.get_head():
            # 다음 곡으로 이동
            self.current_song = self.current_song.next
        else:
            # 이전 곡으로 이동
            self.current_song = self.current_song.prev
        
        # 플레이리스트에서 곡 제거 (간단한 구현)
        self.playlist.delete_by_value(self.current_song.prev.val if self.current_song.prev else None)
        
        return True
    
    def play(self) -> None:
        """재생 시작"""
        if self.current_song is None:
            print("플레이리스트가 비어있습니다.")
            return
        
        self.is_playing = True
        self.current_song.val.play()
    
    def pause(self) -> None:
        """재생 일시정지"""
        self.is_playing = False
        print("⏸️ 일시정지")
    
    def stop(self) -> None:
        """재생 중지"""
        self.is_playing = False
        print("⏹️ 중지")
    
    def next_song(self) -> None:
        """다음 곡으로 이동"""
        if self.current_song is None:
            return
        
        if self.play_mode == "shuffle":
            # 셔플 모드: 랜덤 곡 선택
            import random
            random_steps = random.randint(1, self.playlist.length() - 1)
            self.playlist.rotate_forward(random_steps)
        else:
            # 일반 모드: 다음 곡
            self.playlist.rotate_forward(1)
        
        self.current_song = self.playlist.get_head()
        
        if self.is_playing:
            self.play()
    
    def previous_song(self) -> None:
        """이전 곡으로 이동"""
        if self.current_song is None:
            return
        
        self.playlist.rotate_backward(1)
        self.current_song = self.playlist.get_head()
        
        if self.is_playing:
            self.play()
    
    def set_play_mode(self, mode: str) -> None:
        """
        재생 모드 설정
        
        Args:
            mode: normal, repeat_one, repeat_all, shuffle
        """
        self.play_mode = mode
        mode_names = {
            "normal": "일반 재생",
            "repeat_one": "한 곡 반복",
            "repeat_all": "전체 반복",
            "shuffle": "랜덤 재생"
        }
        print(f"재생 모드: {mode_names.get(mode, mode)}")
    
    def set_volume(self, volume: int) -> None:
        """볼륨 설정"""
        self.volume = max(0, min(100, volume))
        print(f"🔊 볼륨: {self.volume}%")
    
    def get_current_song_info(self) -> dict:
        """현재 곡 정보 반환"""
        if self.current_song is None:
            return {}
        
        song = self.current_song.val
        return {
            'title': song.title,
            'artist': song.artist,
            'duration': song.duration,
            'play_count': song.play_count,
            'is_playing': self.is_playing,
            'volume': self.volume
        }
    
    def get_playlist_info(self) -> dict:
        """플레이리스트 정보 반환"""
        songs = self.playlist.to_list()
        total_duration = sum(song.duration for song in songs)
        
        return {
            'name': self.name,
            'song_count': len(songs),
            'total_duration': total_duration,
            'current_song_index': 0,  # 간단한 구현
            'play_mode': self.play_mode,
            'volume': self.volume
        }
    
    def display_playlist(self) -> None:
        """플레이리스트 출력"""
        print(f"\n🎵 플레이리스트: {self.name}")
        print(f"곡 수: {self.playlist.length()}")
        print(f"재생 모드: {self.play_mode}")
        print(f"볼륨: {self.volume}%")
        
        if self.current_song:
            current_info = self.get_current_song_info()
            print(f"현재 곡: {current_info['artist']} - {current_info['title']}")
            print(f"재생 상태: {'재생 중' if self.is_playing else '일시정지'}")
        
        print("\n곡 목록:")
        for i, song in enumerate(self.playlist.to_list(), 1):
            marker = " ▶ " if song == self.current_song.val else "   "
            print(f"{marker}{i}. {song}")

# 사용 예시
if __name__ == "__main__":
    print("\n=== 음악 플레이리스트 시스템 테스트 ===")
    
    # 플레이리스트 생성
    playlist = MusicPlaylist("내 즐겨찾기")
    
    # 곡들 추가
    songs = [
        Song("Bohemian Rhapsody", "Queen", 355, "queen_bohemian.mp3"),
        Song("Imagine", "John Lennon", 183, "lennon_imagine.mp3"),
        Song("Hotel California", "Eagles", 391, "eagles_hotel.mp3"),
        Song("Stairway to Heaven", "Led Zeppelin", 482, "zeppelin_stairway.mp3"),
        Song("Sweet Child O' Mine", "Guns N' Roses", 356, "gnr_sweet.mp3")
    ]
    
    for song in songs:
        playlist.add_song(song)
    
    # 플레이리스트 정보 출력
    playlist.display_playlist()
    
    # 재생 시작
    print("\n=== 재생 테스트 ===")
    playlist.play()
    
    # 볼륨 조절
    playlist.set_volume(75)
    
    # 다음 곡
    print("\n다음 곡:")
    playlist.next_song()
    
    # 재생 모드 변경
    playlist.set_play_mode("repeat_all")
    
    # 이전 곡
    print("\n이전 곡:")
    playlist.previous_song()
    
    # 셔플 모드
    print("\n셔플 모드 활성화:")
    playlist.set_play_mode("shuffle")
    playlist.next_song()
    
    # 플레이리스트 정보
    info = playlist.get_playlist_info()
    print(f"\n플레이리스트 통계: {info}")
```

### 5. 실행 취소/다시 실행 시스템

#### 5-1 명령 패턴을 활용한 실행 취소 시스템

```python
from abc import ABC, abstractmethod
from typing import Any, List

class Command(ABC):
    """명령 인터페이스"""
    
    @abstractmethod
    def execute(self) -> bool:
        """명령 실행"""
        pass
    
    @abstractmethod
    def undo(self) -> bool:
        """명령 실행 취소"""
        pass

class TextCommand(Command):
    """텍스트 편집 명령"""
    
    def __init__(self, editor, action: str, **kwargs):
        """
        텍스트 명령 초기화
        
        Args:
            editor: 텍스트 편집기
            action: 실행할 액션 (insert, delete, replace)
            **kwargs: 액션별 추가 매개변수
        """
        self.editor = editor
        self.action = action
        self.kwargs = kwargs
        self.backup_data = None
    
    def execute(self) -> bool:
        """명령 실행"""
        try:
            if self.action == "insert":
                self.backup_data = {
                    'position': self.editor.get_cursor_position(),
                    'text': self.kwargs.get('text', '')
                }
                self.editor.insert_text(self.kwargs['text'])
                return True
            
            elif self.action == "delete":
                self.backup_data = {
                    'position': self.editor.get_cursor_position(),
                    'deleted_text': self.editor.delete_char()
                }
                return True
            
            elif self.action == "replace":
                self.backup_data = {
                    'position': self.editor.get_cursor_position(),
                    'old_text': self.kwargs.get('old_text', ''),
                    'new_text': self.kwargs.get('new_text', '')
                }
                # 실제 구현에서는 더 복잡한 교체 로직 필요
                return True
            
            return False
        except Exception:
            return False
    
    def undo(self) -> bool:
        """명령 실행 취소"""
        try:
            if not self.backup_data:
                return False
            
            if self.action == "insert":
                # 삽입한 텍스트 삭제
                self.editor.move_cursor_to_position(self.backup_data['position'])
                for _ in range(len(self.backup_data['text'])):
                    self.editor.delete_char()
                return True
            
            elif self.action == "delete":
                # 삭제한 텍스트 복원
                self.editor.move_cursor_to_position(self.backup_data['position'])
                self.editor.insert_text(self.backup_data['deleted_text'])
                return True
            
            return False
        except Exception:
            return False

class CommandManager:
    """명령 관리자 (실행 취소/다시 실행 시스템)"""
    
    def __init__(self, max_history: int = 50):
        """
        명령 관리자 초기화
        
        Args:
            max_history: 최대 히스토리 크기
        """
        self.max_history = max_history
        self.undo_stack = LinkedList()  # 실행 취소 스택
        self.redo_stack = LinkedList()  # 다시 실행 스택
        self.current_command = None
    
    def execute_command(self, command: Command) -> bool:
        """
        명령 실행
        
        Args:
            command: 실행할 명령
            
        Returns:
            실행 성공 여부
        """
        if command.execute():
            # 실행 취소 스택에 추가
            self.undo_stack.append(command)
            
            # 다시 실행 스택 초기화
            self.redo_stack = LinkedList()
            
            # 히스토리 크기 제한
            if self.undo_stack.length() > self.max_history:
                self.undo_stack.delete_by_index(0)
            
            return True
        return False
    
    def undo(self) -> bool:
        """
        마지막 명령 실행 취소
        
        Returns:
            실행 취소 성공 여부
        """
        if self.undo_stack.is_empty():
            return False
        
        # 마지막 명령 가져오기
        last_command = self.undo_stack.get(self.undo_stack.length() - 1)
        
        if last_command.undo():
            # 실행 취소 스택에서 제거
            self.undo_stack.delete_by_index(self.undo_stack.length() - 1)
            
            # 다시 실행 스택에 추가
            self.redo_stack.append(last_command)
            
            return True
        return False
    
    def redo(self) -> bool:
        """
        마지막 취소된 명령 다시 실행
        
        Returns:
            다시 실행 성공 여부
        """
        if self.redo_stack.is_empty():
            return False
        
        # 다시 실행할 명령 가져오기
        command_to_redo = self.redo_stack.get(self.redo_stack.length() - 1)
        
        if command_to_redo.execute():
            # 다시 실행 스택에서 제거
            self.redo_stack.delete_by_index(self.redo_stack.length() - 1)
            
            # 실행 취소 스택에 추가
            self.undo_stack.append(command_to_redo)
            
            return True
        return False
    
    def can_undo(self) -> bool:
        """실행 취소 가능 여부"""
        return not self.undo_stack.is_empty()
    
    def can_redo(self) -> bool:
        """다시 실행 가능 여부"""
        return not self.redo_stack.is_empty()
    
    def get_history_info(self) -> dict:
        """히스토리 정보 반환"""
        return {
            'undo_stack_size': self.undo_stack.length(),
            'redo_stack_size': self.redo_stack.length(),
            'max_history': self.max_history,
            'can_undo': self.can_undo(),
            'can_redo': self.can_redo()
        }
    
    def clear_history(self):
        """히스토리 초기화"""
        self.undo_stack = LinkedList()
        self.redo_stack = LinkedList()

# 사용 예시
if __name__ == "__main__":
    print("\n=== 실행 취소/다시 실행 시스템 테스트 ===")
    
    # 텍스트 편집기와 명령 관리자 생성
    editor = TextEditor()
    command_manager = CommandManager(max_history=10)
    
    # 텍스트 삽입 명령들 실행
    commands = [
        TextCommand(editor, "insert", text="Hello, "),
        TextCommand(editor, "insert", text="World!"),
        TextCommand(editor, "insert", text=" This is a test.")
    ]
    
    print("명령 실행:")
    for i, cmd in enumerate(commands, 1):
        success = command_manager.execute_command(cmd)
        print(f"  명령 {i} 실행: {success}")
        print(f"  현재 텍스트: {editor.get_text()}")
    
    print(f"\n최종 텍스트: {editor.get_text()}")
    print(f"히스토리 정보: {command_manager.get_history_info()}")
    
    # 실행 취소 테스트
    print("\n실행 취소:")
    for i in range(3):
        can_undo = command_manager.can_undo()
        print(f"  실행 취소 {i+1}: {can_undo}")
        if can_undo:
            success = command_manager.undo()
            print(f"    성공: {success}")
            print(f"    현재 텍스트: {editor.get_text()}")
    
    # 다시 실행 테스트
    print("\n다시 실행:")
    for i in range(2):
        can_redo = command_manager.can_redo()
        print(f"  다시 실행 {i+1}: {can_redo}")
        if can_redo:
            success = command_manager.redo()
            print(f"    성공: {success}")
            print(f"    현재 텍스트: {editor.get_text()}")
    
    # 최종 히스토리 정보
    print(f"\n최종 히스토리 정보: {command_manager.get_history_info()}")
```

## 정리

이번 포스트에서는 연결리스트를 실제 프로젝트와 실무 상황에 적용하는 방법을 다뤘습니다:

**실무 적용 분야**
1. **LRU 캐시 시스템**: 이중 연결리스트와 해시맵의 결합으로 O(1) 성능
2. **텍스트 편집기**: 이중 연결리스트로 구현한 문서 편집 시스템
3. **게임 엔진**: 연결리스트를 활용한 게임 오브젝트 관리
4. **음악 플레이리스트**: 원형 연결리스트로 구현한 미디어 플레이어
5. **실행 취소/다시 실행**: 명령 패턴과 연결리스트를 활용한 상태 관리

**설계 원칙**
- 🏗️ **모듈화**: 기능별로 분리된 클래스와 인터페이스 설계
- ⚡ **성능 최적화**: 자료구조의 특성을 활용한 효율적인 구현
- 🔒 **데이터 무결성**: 일관성 있는 상태 관리와 오류 처리
- 📊 **확장성**: 새로운 기능 추가를 고려한 유연한 설계

**연결리스트의 실무 장점**
- **동적 크기**: 런타임에 크기 조절 가능
- **효율적인 삽입/삭제**: 특정 위치에서 O(1) 시간
- **메모리 효율**: 필요한 만큼만 메모리 사용
- **유연한 구조**: 다양한 비즈니스 로직에 적합

**연결리스트 학습 완성**
이제 연결리스트에 대한 완전한 학습 경로를 완성했습니다:
1. **기본 개념**: 연결리스트의 정의와 특징
2. **예제 문제**: 실전 문제 해결 방법
3. **고급 구현**: 이중, 원형 연결리스트 등
4. **실무 활용**: 실제 프로젝트 적용 방법

**다음 학습 계획**
연결리스트 학습을 완료했으니, 다음 자료구조로 넘어갈 수 있습니다:
- **스택 (Stack)**: LIFO 구조와 함수 호출 관리
- **큐 (Queue)**: FIFO 구조와 작업 스케줄링
- **해시 테이블 (Hash Table)**: 빠른 검색과 키-값 저장

# 📚 레퍼런스
- [LRU Cache Implementation - GeeksforGeeks](https://www.geeksforgeeks.org/lru-cache-implementation/)
- [Command Pattern - Refactoring Guru](https://refactoring.guru/design-patterns/command)
- [Game Programming Patterns - Robert Nystrom](https://gameprogrammingpatterns.com/)

---
*이 포스트는 자료구조 학습 과정을 정리한 내용입니다.*
