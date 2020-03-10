---
layout: post
cover: 'assets/images/office01.jpg'
navigation: True
title: Python 별표(*) 문법(asterisk/star syntax), *args와 **kwargs
date: 2019-07-05 22:00:00
tags: tech python star asterisk args kwargs
subclass: 'post tag-speeches'
logo: 'assets/images/pooh03.png'
author: emil
categories: tech
---

Python에는 우리나라에서 흔히 별표라 부르는 asterisk(`*`)를 활용한 특이한 문법(syntax)가 있습니다. `*args`, `**kwargs`라고 해도 통합니다. C나 C++이 아닌 Python에서 `*`와 `**`를 문두에 사용하는 문법이 있다니 어색하긴 하지만, 용도를 제대로 알면 유용히 쓸 수 있습니다.

### 메서드 정의 시 파라미터(parameter)로 쓰일 때

메서드 정의 시 파라미터(parameter)로 `*args`가 사용되면 그 메서드 내에서 `args`는 메서드 호출 시 투입된 아규먼트(argument)의 튜플(tuple)이 됩니다. 파라미터의 개수가 고정적이지 않은 메서드의 정의 시 사용됩니다.

```python
def args_as_parameter(*args):
	print(type(args))
	print(args)

args_as_parameter(1, '2', 'three', 4.0)
# <class 'tuple'>
# (1, '2', 'three', 4.0)
```

메서드 정의 시 파라미터로 `**kwargs`가 사용되면 그 메서드 내에서 `kwargs`는 메서드 호출 시 투입된 아규먼트의 딕셔너리(dict)가 됩니다. 당연히 아규먼트는 key-value 형태여야 합니다.

```python
def kwargs_as_parameter(**kwargs):
	print(type(kwargs))
	print(kwargs)

kwargs_as_parameter(first=1, second='2', third='three', fourth=4.0)
# <class 'dict'>
# {'first': 1, 'second': '2', 'third': 'three', 'fourth': 4.0}
```

### 메서드 호출 시 아규먼트(argument)로 쓰일 때

메서드 호출 시 아규먼트로 `*args`를 투입한다 가정할 때, `args`는 튜플(또는 리스트, list 등)이어야 합니다. 따라서 앞서 정의한 메서드 `args_as_parameter()`를 다음과 같이 호출할 수 있습니다.

```python
input_tuple = (1, '2', 'three', 4.0)
args_as_parameter(*input_tuple)
```

메서드 호출 시 아규먼트로 `**kwargs`를 투입한다 가정할 때, `kwargs`는 딕셔너리이어야 합니다. 따라서 앞서 정의한 메서드 `kwargs_as_parameter()`를 다음과 같이 호출할 수 있습니다.

```python
input_dict = {'first': 1, 'second': '2', 'third': 'three', 'fourth': 4.0}
kwargs_as_parameter(**input_dict)
```

### `*args`와 `**kwargs`를 메서드 호출 아규먼트로 유용히 쓸 수 있는 용례

`zip()`은 리스트들을 투입받아 묶어 주는 역할을 합니다. 이 기능을 이용해서 m * n 자료를 n * m 자료로 변환하는 데 쓰이기도 하지요. 행렬의 transpose 격입니다. m * n 자료를 `zip()`에 투입하려면 그 속에 들어 있는 길이 n의 리스트를 m번 투입해야 하겠지요. 한두 개라면 별일 아니지만 m이 커진다면 가독성과 생산성, 확정성이 매우 떨어지게 됩니다. 이때 `*args`를 활용할 수 있습니다. 아래처럼 말이죠.

```python
two_col_tuple = ((1, 2, 3, 4), ('a', 'b', 'c', 'd'))
four_col_tuple1 = tuple(zip(two_col_tuple[0], two_col_tuple[1])) # long way
print(four_col_tuple1)
# ((1, 'a'), (2, 'b'), (3, 'c'), (4, 'd'))
four_col_tuple2 = tuple(zip(*two_col_tuple)) # short way
print(four_col_tuple2)
# ((1, 'a'), (2, 'b'), (3, 'c'), (4, 'd'))
```

`cx_Oracle`의 `connect()`는 데이터베이스의 접속 정보(URL, 포트, 서비스명, ID, PW 등을 아규먼트로 받습니다. 이를 모두 `connect()` 메서드에 투입하려면 헷갈려 실수하기 쉽습니다. 물론 사용에 숙달되면 일도 아닐 정도로 쉽겠지만 '나만 잘 짜고 잘 읽고 잘 고치는 코드'가 능사는 아니지요. 이 경우 아규먼트를 딕셔너리로 정의한 후 그것을 `**kwargs` 형태로 `connect()` 메서드에 투입하는 방법이 있습니다. 향후 데이터베이스 접속 정보가 바뀌어 수정해야 할 때 딕셔너리 속 요소들을 쉽게 읽어 바꾸면 되니 관리하기 좋겠지요.

```python
import cx_Oracle
# express way
dsn = cx_Oracle.makedsn('localhost', 1521, 'orcl')
conn = cx_Oracle.connect('sys', 'orange', dsn, mode=cx_Oracle.SYSDBA)
conn.close()
# tidy and readable way
conn_conf = {
	'user': 'sys',
	'password': 'orange',
	'dsn': dsn,
	'mode': cx_Oracle.SYSDBA
}
conn = cx_Oracle.connect(**conn_conf)
conn.close()
```

### 참고자료
https://www.geeksforgeeks.org/args-kwargs-python/
