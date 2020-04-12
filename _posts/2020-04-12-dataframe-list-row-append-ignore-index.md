---
layout: post
cover: 'assets/images/office01.jpg'
navigation: True
title: DataFrame(데이터프레임)에 list(리스트)를 행 추가하기 & ignore_index 관련 TypeError 처리
date: 2020-04-12 15:00:00
tags: tech python pandas dataframe indexing selecting mask loc
subclass: 'post tag-speeches'
logo: 'assets/images/pooh03.png'
author: emil
categories: tech
---

이미 있는 `DataFrame`(데이터프레임)에 행(row)으로 추가할 데이터가 `list`(리스트)로 주어져 있다면 어떻게 해야 할까요? 데이터 재료부터 준비해 봅시다.

참고로, 편의상 `DataFrame`을 출력하기 위해 `tabulate` 패키지를 활용한 `print_df()`를 정의해 사용하였습니다.

```python
import pandas as pd
import numpy as np
from tabulate import tabulate

# DataFrame 출력을 위해 tabulate 패키지 활용
def print_df(df):
    print(tabulate(df, headers='keys', tablefmt='psql'))

# 이미 있던 DataFrame
df = pd.DataFrame(np.arange(25).reshape(5, 5), columns=list('abcde'))
print_df(df)
'''
+----+-----+-----+-----+-----+-----+
|    |   a |   b |   c |   d |   e |
|----+-----+-----+-----+-----+-----|
|  0 |   0 |   1 |   2 |   3 |   4 |
|  1 |   5 |   6 |   7 |   8 |   9 |
|  2 |  10 |  11 |  12 |  13 |  14 |
|  3 |  15 |  16 |  17 |  18 |  19 |
|  4 |  20 |  21 |  22 |  23 |  24 |
+----+-----+-----+-----+-----+-----+
'''

# 행으로 추가할 list
row = [101, 102 103, 104, 105]
print(row)
'''
[101, 102 103, 104, 105]
'''
```

쉬운 생각으로, 다음과 같이 하면 될 것 같지요? 그런데 그렇게 하면 조금 이상한 결과가 나옵니다.

```python
df2 = df.append(row)
print_df(df2)
'''
+----+-----+-----+-----+-----+-----+-----+
|----+-----+-----+-----+-----+-----+-----|
|  0 |   0 |   1 |   2 |   3 |   4 | nan |
|  1 |   5 |   6 |   7 |   8 |   9 | nan |
|  2 |  10 |  11 |  12 |  13 |  14 | nan |
|  3 |  15 |  16 |  17 |  18 |  19 | nan |
|  4 |  20 |  21 |  22 |  23 |  24 | nan |
|  0 | nan | nan | nan | nan | nan | 101 |
|  1 | nan | nan | nan | nan | nan | 102 |
|  2 | nan | nan | nan | nan | nan | 103 |
|  3 | nan | nan | nan | nan | nan | 104 |
|  4 | nan | nan | nan | nan | nan | 105 |
+----+-----+-----+-----+-----+-----+-----+
'''
```

왜 이럴까요? `DataFrame`에 `list`가 `append()`로 주어지면 `pandas`는 이를 행이 아닌 열 데이터로 인식하기 때문입니다. 그렇다면 `list`를 `Series`(시리즈)로 만들어서 해보면 어떨까요?

```python
df3 = df.append(pd.Series(row))
# TypeError: Can only append a Series if ignore_index=True or if the Series has a name
```

잠깐! `TypeError`가 발생했군요. `TypeError: Can only append a Series if ignore_index=True or if the Series has a name`가 의미하는 것은 무엇일까요? 새로 추가되는 `Series`는 행 데이터이기는 하지만 행의 이름으로서의 인덱스(index)가 없는 데이터이기 때문입니다. 인덱스를 명시적으로 부여하거나 '아무 인덱스가 부여되도 상관 없으니 무시하라'는 의사를 표시해야 합니다.

인덱스를 명시적으로 주려면 `Series`에 `name`을 부여해야 합니다. 즉, `DataFrame`에 `Series`가 `append()`될 때 `Series`가 갖고 있는 `name`이 새로 만들어질 행의 인덱스가 됩니다.

```python
df4 = df.append(pd.Series(row, name=10))
print_df(df4)
'''
+----+-----+-----+-----+-----+-----+-----+-----+-----+-----+-----+
|    |   a |   b |   c |   d |   e |   0 |   1 |   2 |   3 |   4 |
|----+-----+-----+-----+-----+-----+-----+-----+-----+-----+-----|
|  0 |   0 |   1 |   2 |   3 |   4 | nan | nan | nan | nan | nan |
|  1 |   5 |   6 |   7 |   8 |   9 | nan | nan | nan | nan | nan |
|  2 |  10 |  11 |  12 |  13 |  14 | nan | nan | nan | nan | nan |
|  3 |  15 |  16 |  17 |  18 |  19 | nan | nan | nan | nan | nan |
|  4 |  20 |  21 |  22 |  23 |  24 | nan | nan | nan | nan | nan |
| 10 | nan | nan | nan | nan | nan | 101 | 102 | 103 | 104 | 105 |
+----+-----+-----+-----+-----+-----+-----+-----+-----+-----+-----+
'''
```

`name`을 임의로 `10`으로 줬더니 기존 `DataFrame`의 인덱스와 연결성이 깨지는 결번 현상이 발생했습니다. 다른 방법, 즉, '아무 인덱스가 부여되도 상관 없으니 무시하라'는 의사를 표시하는 방법을 쓰면 결번 현상을 막을 수 있습니다. `append()`에 `ignore_index=True`를 인자로 주면 됩니다.

```python
df5 = df.append(pd.Series(row), ignore_index=True)
print_df(df5)
'''
+----+-----+-----+-----+-----+-----+-----+-----+-----+-----+-----+
|    |   a |   b |   c |   d |   e |   0 |   1 |   2 |   3 |   4 |
|----+-----+-----+-----+-----+-----+-----+-----+-----+-----+-----|
|  0 |   0 |   1 |   2 |   3 |   4 | nan | nan | nan | nan | nan |
|  1 |   5 |   6 |   7 |   8 |   9 | nan | nan | nan | nan | nan |
|  2 |  10 |  11 |  12 |  13 |  14 | nan | nan | nan | nan | nan |
|  3 |  15 |  16 |  17 |  18 |  19 | nan | nan | nan | nan | nan |
|  4 |  20 |  21 |  22 |  23 |  24 | nan | nan | nan | nan | nan |
|  5 | nan | nan | nan | nan | nan | 101 | 102 | 103 | 104 | 105 |
+----+-----+-----+-----+-----+-----+-----+-----+-----+-----+-----+
'''
```

인덱스 결번 현상은 없어졌습니다. 자연스럽게 기존 `DataFrame`의 최대 인덱스를 1 증가(increment)시켜 새 인덱스로 삼았습니다. 그런데 해결되지 않은 문제가 남았습니다. `DataFrame`의 헤더는 a ~ e의 알파벳이었죠? 그런데 새로운 행은 0 ~ 4라는 숫자 헤더를 불필요하게 만들었습니다. 의도한 결과가 아닐 뿐더러, 쓸모 없는 `nan` 데이터도 무수히 생겨났지요. 아래처럼, `Series`에 인덱스를 설정해 `append()`하면 해결이 됩니다.

```python
df6 = df.append(pd.Series(row, index=df.columns), ignore_index=True)
print_df(df6)
'''
+----+-----+-----+-----+-----+-----+
|    |   a |   b |   c |   d |   e |
|----+-----+-----+-----+-----+-----|
|  0 |   0 |   1 |   2 |   3 |   4 |
|  1 |   5 |   6 |   7 |   8 |   9 |
|  2 |  10 |  11 |  12 |  13 |  14 |
|  3 |  15 |  16 |  17 |  18 |  19 |
|  4 |  20 |  21 |  22 |  23 |  24 |
|  5 | 101 | 102 | 103 | 104 | 105 |
+----+-----+-----+-----+-----+-----+
'''
```

깔끔하게 행 데이터로 삽입이 되었지요? 번외로 아래와 같은 트릭을 쓸 수도 있습니다. 위에서 논의한 방법과는 전혀 다른 방법입니다. 두 가지 방법 중 개인적으로 더 편리한 방법을 사용하면 될 것 같습니다.

```python
df7 = df.append(df.iloc[-1], ignore_index=True)
df7.iloc[-1] = row
print_df(df7)
'''
+----+-----+-----+-----+-----+-----+
|    |   a |   b |   c |   d |   e |
|----+-----+-----+-----+-----+-----|
|  0 |   0 |   1 |   2 |   3 |   4 |
|  1 |   5 |   6 |   7 |   8 |   9 |
|  2 |  10 |  11 |  12 |  13 |  14 |
|  3 |  15 |  16 |  17 |  18 |  19 |
|  4 |  20 |  21 |  22 |  23 |  24 |
|  5 | 101 | 102 | 103 | 104 | 105 |
+----+-----+-----+-----+-----+-----+
'''
```

제대로 된 결과가 역시 잘 나왔습니다. 마지막 방법의 원리를 간단히 설명해 볼까요? 우선 기존 `DataFrame`의 마지막 행을 그대로 복제해 덧붙입니다. 덧붙여진 마지막 행에 `list`를 할당(assign)합니다. 마지막 행을 복제한 목적은 새로운 `list`가 삽입될 공간을 마련해 준 것뿐이지요.

메커니즘은 간단하지만, 앞서 위에서 설명한 방법과 비교할 때 '코딩량'은 크게 차이나지는 않습니다. 결국, 각자 스타일에 맞는 방법을 택하면 되겠지요?
