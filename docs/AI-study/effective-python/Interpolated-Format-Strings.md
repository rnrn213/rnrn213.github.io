---
layout: default
title: Interpolated Format Strings
parent: Effective Python
grand_parent: AI study
nav_order: 1
---

Interpolated Format Strings
=============

item 4: Prefer Interpolated F-Strings에서 interpolated format string(이하 f-string)에 대해서 배웠습니다.   
f-string은 string formatting할 때 다양한 이점이 있는 것을 알게 되었고 인터넷 검색을 통해서 더 공부해보았습니다.   
<br/>
f-string은 Python 3.6 이상 버전에서 사용할 수 있는 문법으로 문자열의 접두사에 f를 붙이면 사용할 수 있습니다. 

```python
>>> var = 'abc'
>>> value = 1111

>>> formatted = f'{var} = {value}'
>>> print(formatted)
abc = 1111
```
위와 같이 변수의 placeholder로 {}를 사용하면 원하는 위치에 변수를 출력할 수 있습니다.   
<br/>

## Text Alignment
string data의 양 옆에 공백을 출력할 수 있습니다.

```python
>>> text = "f-string"
>>> print(f'{text:>15}') # 1) 15 - (text의 길이)만큼 text의 왼쪽을 공백으로 채우고 출력
       f-string
>>> print(f'{text:<15}') # 2) text의 오른쪽을 공백으로 채우고 출력
f-string       
>>> print(f'{text:15}') # 3) text의 오른쪽을 공백으로 채우고 출력
f-string       
>>> print(f'{text:^15}') # 4) text의 양 옆을 공백으로 채우고 출력
   f-string    
>>> print(f'{text:>3}') # 5) text의 길이보다 작은 값일 경우 무시됨
f-string
```

## Number Format
f-string을 이용해서 수를 다양하게 출력할 수 있습니다.

```python
>>> num = 0.123456789
>>> formatted = f'{num:.3f}' # 1) 소수점 셋째자리까지 출력
>>> print(formatted)
0.123

>>> formatted = f'{num:.5%}' # 2) 소수점 다섯째자리까지의 percentage 출력
>>> print(formatted)
12.34568%

>>> num = 123456789
>>> formatted = f'{num:,}' # 3) 천의 단위마다 구분 기호를 넣어 출력
>>> print(formatted)
123,456,789

>>> formatted = f'{num:,.2f}' # 4) 1) + 3) 출력
>>> print(formatted)
123,456,789.00

>>> formatted = f'{num:e}' # 5) scientific notation 출력
>>> print(formatted)
1.234568e+08
```

## Arbitrary Expressions
f-string의 {} 안의 표현식들은 런타임에 evaluated되기 때문에 유효한 파이썬 표현식들은 f-string 안에 넣을 수 있습니다.

```python
>>> print(f'{2 * 3}')
6

# f-string 안에서 if문 사용
>>> a = 2 
>>> b = 3
>>> print(f"a is {'greater than' if a > b else ('less than' if a < b else 'equal to')} b")
a is less than b


>>> def hello():
...    return "hello"

# f-string에서 함수 호출
>>> print(f'{hello()}')
hello

# f-string에서 method 호출
>>> name = "NAME"
>>> print(f'{name.lower()}')
name
```

\_\_str()__ 와 \_\_repr()__ method가 정의된 클래스도 f-string에서 사용할 수 있습니다.   
기본값으로 f-string은 \_\_str()__ method를 사용하며, !r을 사용하여 \_\_repr()__ 을 사용할 수 있습니다.

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y
    
    def __str__(self):
        return f'({self.x}, {self.y})'
    
    def __repr__(self):
        return f'({self.x}, {self.y}) point'

>>> my_point = Point(2, 3)
>>> print(f'{my_point}') # __str()__
(2, 3)
>>> print(f'{my_point!r}') # __repr()__
(2, 3) point
```

## Combining r-string and f-string
r-string과 f-string을 결합해서 사용할 수 있습니다.   
이로 인해 파일 경로를 표현할 수 있습니다.

```python
>>> text = 'text.txt'
>>> file_path = rf'C:\Users\{text}'
>>> print(file_path)
C:\Users\text.txt
```

<hr/>

<br/>

# 참고자료
https://pythoninoffice.com/python-f-string-formatting/   
https://pythoninoffice.com/python-for-office-101/   
https://realpython.com/python-f-strings/#f-strings-a-new-and-improved-way-to-format-strings-in-python