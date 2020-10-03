---
layout: post
cover: 'assets/images/office01.jpg'
navigation: True
title: with 문(statement)으로 파일, DB 커서 등을 놓지지 않고 닫고 관리하기
date: 2020-10-03 22:00:00
tags: tech python with cursor pandas option context
subclass: 'post tag-speeches'
logo: 'assets/images/pooh03.png'
author: emil
categories: tech
---

`with` 문(statement)을 잘 쓰면 Python을 매우 간결하고 Python스럽게(Pythonic하게) 작성할 수 있습니다. `with`와 `as`에 의해 도출된 변수는 `with` 문의 스코프(scope) 안에서만 유효하게 쓰인다는 '느낌(*)'을 줍니다. 이 '느낌'을 문법적인 장치로 구현해 낸 것이죠. 이 문법적인 장치를 통해 코드의 작성자뿐 아니라 읽는 사람도 코드 속 논리의 경계를 쉽게 구분할 수 있습니다.

`as`에 의한 변수가 `with` 문의 스코프 안에서만 유효하다 단정지은 게 아니라 '유효한 느낌(\*)'이라고 한 데에는 이유가 있습니다. 사실, `as`에 의한 변수는 `with` 문 스코프 밖에서도 사용할 수 있습니다. 그렇지만 `with` 문 밖에서는 그 '변수'가 '닫혀진(\*\*)' 상태일 것입니다. 그 변수는 `with` 문 속에서 열려 있다가 밖으로 나가는 순간 닫히는 것이지요.

가장 대표적인 용례는 파일 열기입니다. `with` 문을 사용하지 않으면 파일 열기를 다음과 같이 할 수 있습니다.

```python
# 파일 열기 - with 문 미사용
f = open('/path/to/file', 'r')
fcont = f.read()
f.close()
```

한편, `with`를 사용하면 다음과 같죠.

```python
# 파일 열기 - with 문 사용
with open('/path/to/file', 'r') as f:`
    fcont = f.read()
```

`with`를 쓴 파일 열기 코드에는, 쓰지 않은 코드와 다른 점 두 가지가 있습니다. 우선, 코드의 길이가 짧습니다. 줄(line) 개수를 하나 줄일 수 있었지요. 두 번째로, `open()`으로 연 파일을 별도로 `close()`해 주지 않았습니다. `with` 문의 스코프를 벗어나면서 파일 객체 `f`가 자동으로 `close()`되기 때문입니다.

이쯤되면 `with` 문을 사용한 뒤의 코드가 보다 Python스럽다(Pythonic)는 느낌을 받으시나요? 파이썬스럽다는 느낌을 차치하고도 뒤 코드는 여러 장점들이 있습니다. 첫째, 코드의 길이가 짧고(한 줄 차이이긴 하지만), 둘째, 변수가 지니는 논리의 경계가 정규 문법 차원에서 뚜렷히 분리되며, 셋째, 열고(`open()`) 나서 까먹고 닫지(`close()`) 않을 위험이 해소됩니다.

이제까지는 이해하기 편하게 '닫다(\*\*)'라는 표현을 썼지만 엄밀히는 닫는 게 아니라 `__exit__()`한다라고 말해야 합니다. `with` 문의 `as`에 의해 변수가 될 수 있는 표현(expression)은 컨텍스트 매니저(context manager)이어야 하는데, `with` 문의 스코프가 끝날 때 컨텍스트 매니저가 스스로 지닌 멤버 메서드(method) `__exit__()`를 자동으로 호출하는 게, `with` 문의 실제 작동 원리이기 때문입니다.

파일의 `open()`의 경우 `__exit__()`가 `close()`를 호출해 주도록 되어 있기 때문에 `with` 문 밖으로 나감과 동시에 파일이 닫히게 되는 효과를 보는 것입니다. `with` 문과 컨택스트 매니저에 대해서는 보다 깊은 내용이 있으나 여기서는 상세한 부분을 생략하고 다음의 참고자료(링크)로 대신하겠습니다.

* [The Python Language Reference - The with statement](https://docs.python.org/3/reference/compound_stmts.html#the-with-statement)
* [The Python Language Reference - With Statement Context Managers](https://docs.python.org/3/reference/datamodel.html#with-statement-context-managers)

`with` 문을 파일 열기 외에도 사용할 수 있을까요? 다음처럼 데이터베이스(DB)의 커서를 관리하는 데 쓰기도 합니다.

```python
# 데이터베이스(DB) 커서 관리 - with 문 사용
import sqlite3

conn = sqlite3.connect('/path/to/file')
with conn.cursor() as cur:
    cur.execute('select * from table_name')
    rows = cur.fetchall()
```

Pandas의 옵션 컨텍스트(option context)를 임시적으로 적용할 때에도 `with` 문을 사용할 수 있습니다. 데이터프레임(DataFrame)을 표시(display)할 때 특정 옵션을 설정하고 싶으나 전역적이지 않고 지엽적으로 임시 설정하려 할 때 자주 사용하는 기법입니다.

```python
# 데이터베이스(DB) 커서 관리 - with 문 사용
import pandas as pd
import numpy as np

mat = np.random.randint(1000, size=(100, 10)) # 100행 10열 행렬
df = pd.DataFrame(mat) # 100행 10열 데이터프레임
with pd.option_context('display.max_rows', None):
    display(df)
```

위 용례에서, 행이 100개 정도로 많은 데이터프레임을 표시하려 하는데, Pandas는 데이터프레임을 표시할 때 기본적으로 중간에 있는 행들을 생략합니다. `display.max_rows`를 `None`으로 하면 생략 없이 모든 행을 보여줍니다. `with` 문의 사용 없이 `pd.set_option()`을 사용해 전역적으로 이 옵션을 적용할 수 있으나, 단발적, 일시적인 설정 효과를 내려면 `pd.option_context()`를 `with`와 함께 써 주면 됩니다.

앞서 살펴 본, 파일 열기, 데이터베이스 커서 관리하기, Pandas 옵션 컨텍스트 임시 적용하기의 용례 외에도, 자원(resource)의 락과 해제(lock, unlock) 등에도 사용할 수 있으며, 필요할 경웨 컨텍스트 매니저 자체를 직접 정의해 사용할 수도 있습니다.

* 참고자료
    - [The Python Standard Library - contextlib - Utilities for with-statement contexts](https://docs.python.org/3/library/contextlib.html?highlight=clause)
    - [GeeksforGeeks - with statement in Python](https://www.geeksforgeeks.org/with-statement-in-python/)
    - [Stack Overflow - Can I use pymysql.connect() with “with” statement?](https://stackoverflow.com/questions/31214658/can-i-use-pymysql-connect-with-with-statement)