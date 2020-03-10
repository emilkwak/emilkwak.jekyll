---
layout: post
cover: 'assets/images/office01.jpg'
navigation: True
title: Pandas DataFrame(Series)의 문자열 contains(), startswith() 여러 값 적용하는 방법
date: 2019-01-22 23:30:00
tags: tech python pandas
subclass: 'post tag-speeches'
logo: 'assets/images/pooh03.png'
author: emil
categories: tech
---

Pandas Series는 강력한 문자열 처리 기능을 갖고 있습니다. `Series.str.contains()`는 특정 문자열을 포함하는 요소를 찾아 주고 `Series.str.startswith()`, `Series.str.endswith()`는 특정 문자열로 시작되거나 끝나는 요소를 찾아 줍니다. Series뿐 아니라 DataFrame의 각 열(column)에 대해서도 앞서 말한 기능을 쓸 수 있습니다. `DataFrame['COL1'].str.contains()`처럼 쓰면 되지요. DataFrame에서 각 열은 Series이니 어찌 보면 당연한 얘기입니다.

가끔 작업을 하다 보면 여러 문자열 중 하나라도 포함하는 경우, 또는 여러 문자열 중 하나로라도 시작하는(끝나는) 경우를 따져야 할 경우가 생깁니다. `contains()`의 경우 '\|'(pipe)로 연결된(OR-ing) 문자열을 인자로 주면 되고, `startswith()`, `endswith()`의 경우 문자열의 tuple을 인자로 주면 됩니다. 이 방법을 알기 전까지는 `df.COL1.apply(lambda x: any(i in x for i in ['this', 'that']))`처럼 복잡한 코드 작성을 했었는데, 진작에 알았다면 이미 짠 많은 코드들이 간결하고 가독성 좋게 남아 있었겠지요.

아래는 `contains()`에 '\|'로 연결된 문자열을 인자로 준 사례입니다.

```
import pandas as pd

names = ['jack', 'bob', 'watson', 'jake', 'mike', 'don', 'george', 'natasha', 'jill', 'ann', 'john', 'johnson']
ser = pd.Series(names)

ser[ser.str.contains('oh|son')]
# 2      watson
# 10       john
# 11    johnson
# dtype: object
```

아래는 `startswith()`에 문자열의 tuple을 인자로 준 사례입니다. `endswith()`에 대해서도 응용이 가능하지요.

```
ser[ser.str.startswith(('j', 'a',))]
# 0        jack
# 3        jake
# 8        jill
# 9         ann
# 10       john
# 11    johnson
dtype: object
```
