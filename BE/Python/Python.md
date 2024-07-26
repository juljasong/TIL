# 자료형

## 숫자
- **정수형** (1, 2, -2) : int
- **실수형** (1.24, -34.56) : float
- 컴퓨터식 지수 표현 방식
- 8진수
- 16진수

## 연산자
- 사칙연산
  - a + b
  - a - b
  - a * b
  - a / b
- 제곱근
  - a ** b
- 나머지
  - a % b
- 몫
  - a // b 

## 문자열
```python
a = "text"
b = 'text'
c = """
        text
    """
d = '''
        text
    '''
```
- 줄 바꿈
  - ```\n``` 
  - ```"""text"""``` or ```'''text'''``` 쓰기
- 이스케이프 코드
    - ```\n``` : new line
    - ```\t``` : tab
    - ```\\``` : \
    - ```\'``` : '
    - ```\"``` : "
    - ```\r``` : 캐리지 리던 (줄 바꿈, 커서를 현재 줄의 가장 앞으로 이동)
    - ```\f``` : 폼 피드 (줄 바꿈, 커서를 현재 줄의 다음줄로 이동)
    - ```\a``` : 벨 소리 (출력 시 삑)
    - ```\b``` : back space
    - ```\000``` : null
- 함수 ```a = "Life is too short"```
  - ```len(a) == 17``` : 문자열 개수 구하기
  - ```a[3] == e``` : 문자열 인덱싱 (0부터)
  - ```a[이상:미만:간격]``` : 문자열 슬라이싱
- 문자열 포맷팅
  - ```print("I eat %d apples." % 10)```
  - ```print("I eat %s apples." % five)```
    - ```%s``` : string
    - ```%c``` : character
    - ```%d``` : integer
    - ```%f``` : float
    - ```%o``` : 8진수
    - ```%x``` : 16wlstn
    - ```%%``` : Literal % (문자 % 자체)
  - ```a = "I eat {0} apple".format("five")```
  - ```f'My name is {name}. My age is {age + 1}'``` : 변수 집어넣기 가능. 연산도 가능
  - ```python
        y = 3.141592
        a = f'{y:0.4f}
        print(a) # 3.1416
    ```
  - 기타 함수
    - count() : ```"abcd".count() # 4```
    - find() : ```"abcd".find(x) # -1```
    - index() : ```"abcd".index(x) # error```
    - join() : ```"to the".join('abcd') # a to b to the c to the d```
    - upper/lower() : ```"small".upper() # SMALL```
    - lstrip() : ```" hi ".lstrip() # "hi "```
    - rstrip() : ```" hi ".rstrip() # " hi"```
    - strip() : ```" hi ".strip() # "hi"```
    - replace() : ```"abcd".replace('b', 'x') # axcd```
    - split() : ```"a b c d".split() # ['a', 'b', 'c', 'd']```

## 불

## 변수

## 리스트

## 튜플

## 딕셔너리

## 집합