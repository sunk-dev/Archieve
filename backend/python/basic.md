# 파이썬 기본
## 리스트 추가
- append
```python
numbers=[1,2,3,4,5]
numbers.append(6)
````
- insert
  - 원하는 위치에 데이터 삽입
  ```python
  numbers=[1,2,3,4,5]
  numbers.insert(1,6) #리스트 1번 요소에 6 삽입
  ```
## 리스트 삭제
- `pop(인덱스위치)` :인덱스위치의 요소를 꺼내면서 삭제
- `del 리스트[인덱스위치]` :인덱스 요소를 꺼내지 않고 바로 삭제
  ```python
  numbers=[1,2,3,4,5]
  numbers.pop(0) #리스트 0번째 요소삭제 
  del numbers[2] #리스트의 2번째 요소 삭제
  ```

  ## 문자열 포맷팅
  - %d : 10진수
  - %x : 16진수
  - %o : 8진수
  - %f : 실수
  - %s : 문자열
    
## enumerate()
## id()
## filter
## lambda
## map
