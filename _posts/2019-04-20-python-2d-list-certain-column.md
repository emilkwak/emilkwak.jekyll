---
layout: post
cover: 'assets/images/office01.jpg'
navigation: True
title: Python 2차원 리스트(list)의 특정 열(column) 골라 취하기
date: 2019-04-20 12:30:00
tags: tech python list array zip numpy pandas transpose
subclass: 'post tag-speeches'
logo: 'assets/images/pooh03.png'
author: emil
categories: tech
---

C나 C++에서 2차원 배열(2-dimension array) 개념을 많이 사용합니다. 배열을 요소(element)로 하는 배열로 표현을 하지요. Python에서도 리스트(list)를 2차원으로 사용하여 2차원 배열과 동일한 효과를 낼 수 있습니다.

```python
a = [
    [0, 1],
    [2, 3],
    [4, 5],
    [6, 7],
    [8, 9],
]
```

2차원 리스트의 특정 열(column)을 취해 처리해야 할 경우에는 어떻게 해야 할까요? Python의 기본 문법을 이용한 가장 C/C++적인 방법은 루프(loop)를 돌면서 처리하는 것이겠지요.

```python
b = []
for i in a:
    b.append(i[0])
print(b)
# [0, 2, 4, 6, 8]
```

Python답게 inline for loop를 사용해 봅시다.

```python
b = [i[0] for i in a]
print(b)
# [0, 2, 4, 6, 8]
```

zip을 이용한 방법도 있습니다.

```python
b = list(zip(*a))[0]
print(b)
# (0, 2, 4, 6, 8)
```

Python 기본 문법을 넘어서 numpy나 pandas 등 라이브러리를 사용하면 훨씬 우아한 코드가 가능합니다. 아래는 numpy를 사용한 방법입니다. numpy의 array로 전환한 후 전치(transpose)시킨 후 인덱싱하는 순서입니다.

```python
import numpy as np
b = np.array(a).T[0]
print(b)
# [0 2 4 6 8]
```

위의 numpy 코드를 보면 매우 수학적인 느낌이 들지요? 2차원 리스트를 행렬로 인식한 후 전치행렬로 바꾼 다음 행 번호로 접근하는 과정이지요. 아래와 같이 pandas를 이용한 방법도 있습니다.

```python
import pandas as pd
b = pd.DataFrame(a)[0]
print(b.to_list())
# [0, 2, 4, 6, 8]
```

pandas에 익숙한 분은 아시겠지만 pandas는 Excel 같은 스프레드시트 관점으로 접근합니다. 위 코드로는 정확히 느낌이 오지 않을 수 있지만, 2차원 리스트를 스프레드시트라 볼 수 있는 데이터프레임으로 바꾼 후 0열을 취하는 과정입니다.

Python 2차원 리스트의 특정 열만 골라서 선택적으로 취하는 방법을 알아 봤습니다. C/C++식 루프 돌리는 방법, inline for loop 이용한 방법, zip 이용한 방법, numpy나 pandas를 통한 방법이 있었습니다.