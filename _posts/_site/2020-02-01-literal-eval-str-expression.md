---
layout: post
cover: 'assets/images/office01.jpg'
navigation: True
title: ast.literal_eval()로 문자열(str) 속 표현식(expression) 인식하기
date: 2020-02-01 22:00:00
tags: tech python eval literal literal_eval expression statement str ast
subclass: 'post tag-speeches'
logo: 'assets/images/pooh03.png'
author: emil
categories: tech
---

# 

가만히 보면 Python 표현식(expression statement)도 문자열(str)입니다. 좀 애매한 말이지요? 예를 들어 `a = 1 + 2`라는 구문(statement)가 있을 때 '1 더하기 2'를 나타낸 우변도 결국 `'1 + 2'` 형태의 문자열이라는 것이지요.

Python 작업을 하다 보면 이렇게 문자열에 담겨 있는 표현식을 꺼내 써야 할 때가 종종 있습니다. 메모리에 있는 중간 처리 결과를 문자열로 출력한 후 메모장 같은 곳에 저장해 두었다가 나중에 쓰기도 합니다. 리스트(list) 표현식을 통째로 문자열로 메모해 두었다가 쓸 일도 많구요.

가령, 동창회 멤버 10명의 생년월일 데이터를 자주 사용하는 모임 총무가 있다고 칩시다. 길이(len)가 고작 10이고 각 요소(element)도 8자리 정수(int)인 자그마한 데이터를 굳이 `pickle` 같은 것을 사용해 보관할 필요는 없습니다.(오히려 번거롭지요.)

```python
birth_day_list = ['19950415', '19950527', '19951103', '19950606', '19950717', '19950327', '19951030', '19950424', '19950815', '19950901']
print(len(birth_day_list)) # 10
print(len(birth_day_list[0])) # 8
```
대신 이 정도의 데이터는 Python 표현식 모양 그대로 메모장이나 클라우드 노트(Evernote, OneNote 따위)에 저장해 놓았다가 복사/붙여넣기로 다시 가져다 씁니다. 그런데 이렇게 문자열로 메모해 놓은 표현식이 한두 개면 몰라도 개수가 많아지면 매번 복사/붙여넣기 식으로 하기 번거로워집니다.

그렇다면 문자열로 기록된 표현식들을 일괄로 읽어 들여 와 Python 문자열 변수로 만들고 이를 이용해 원래의 Python 데이터를 얻어 내는 방법이 있을까요? 표현식의 일괄 읽기는 그렇게 어렵지 않습니다. `open()`과 `read()`, `readlines()` 등을 적절히 써 주면 됩니다. 관건은 '문자열 변수에 할당된 표현식으로부터 원래의 데이터를 얻는 방법'에 있습니다.

```python
list_in_str = '[1, 2, 3, 4, 5]'
list_data = list_in_str
print(type(list_data)) # str - wrong solution
```
위 방법은 `list_in_str`을 그대로 재할당하는 작업일 뿐입니다. 그러면 어떻게 해야 `list_in_str`에 내포된 데이터를 복구해 낼 수 있을까요? `ast.literal_eval()`을 사용하는 것입니다.
```python
import ast
list_in_str = '[1, 2, 3, 4, 5]'
list_data = ast.literal_eval(list_in_str)
print(type(list_data)) # list - correct solution
```
아래처럼 딕셔너리(dict)도 동일한 방법으로 처리가 가능합니다.
```python
import ast
list_in_str = "{'a': 1, 'b': 2, 'c': 3}"
list_data = ast.literal_eval(list_in_str)
print(type(list_data)) # dict
```
사실 `eval()`이라는 내장(built-in) 함수가 있습니다. 그렇지만 이 함수는 악용될 소지가 커 가급적 사용을 자제해야 합니다.(참고: [python eval() 함수 - 사용을 조심해야 하는 이유](https://bluese05.tistory.com/64))

한편, `ast.literal_eval()`은 위에서 우리가 하려고 했던 목적을 이루게 해 주면서도, 안전성을 보장합니다. 정확히 이야기하자면, 인자(parameter)로 주어진 표현식을 실행하기 전에 그것이 '수상한 표현식'인지 아닌지 먼저 검증합니다.

`ast.listeral_eval()`은 이처럼 안전한 대신 인자로, 제한된 리터럴(literal)로 구성된 표현식만 허용합니다. 즉, 다음과 같은 리터럴들만 표현식에 등장할 수 있다는 뜻이죠.

> 문자열, 바이트, 수, 튜플, 리스트, 딕셔너리, 셋, 불리언, None
(참고: [ast — Abstract Syntax Trees](https://docs.python.org/3/library/ast.html))

`eval()`이 내장 함수이기도 하고 Python이 아닌 다른 언어에도 보통 등장하는 기능이라, 무심코 사용하는 경향이 있습니다. 물론 개인적인 용도로 사용할 프로그램이거나 믿을 수 있는 사용자들만 사용할 프로그램이라면 괜찮겠으나, 믿을 수 있는 그룹이 아닌 불특정 다수 그룹이 사용할 것이라면, `eval()`의 사용을 최소한으로 하고 `ast.literal_eval()`과 같은 대체 기능을 사용해야 합니다.