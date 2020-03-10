---
layout: post
cover: 'assets/images/office01.jpg'
navigation: True
title: numpy의 argmax(), argmin() 그리고 argwhere() - 최대, 최소, 특정 값 찾기
date: 2019-04-12 23:55:00
tags: tech python numpy
subclass: 'post tag-speeches'
logo: 'assets/images/pooh03.png'
author: emil
categories: tech
---

학부 저학년 시절 첫 프로그래밍 언어를 접할 때, 정수형 array에서 최대 값의 위치를 찾기 위해 자연스럽게 loop을 사용했습니다. 아래처럼 말이죠.


```python
a = [3, 2, 1, 10, 9, 8, 4, 5, 6, 7]
maximal = -9999
for ix, i in enumerate(a):
    if i > maximal:
        maximal = i
        mx = ix
print(mx, maximal)
```

Python에 익숙해 지면서 max()와 같은 기본 메서드를 손쉽고 자연스럽게 사용하게 되었습니다. 그리고 Python을 매력적으로 만들었던 inline for loop도 자유자재로 쓰게 되면서 아래처럼 컴팩트한 코드를 작성할 수 있게 되었죠.

```python
a = [3, 2, 1, 10, 9, 8, 4, 5, 6, 7]
maximum = max(a)
print([(ix, i) for ix, i in enumerate(a) if i == maximum])
```

numpy의 존재를 알게 되면서 큰 충격에 빠지지요. 이산수학 강의에서나 볼법한 수학 기호가 그대로 메서드로 구현되어 있더군요. inline for loop조차 구질구질해 보이게 만든 간결한 코드가 아래처럼 가능합니다.

```python
import numpy as np

a = np.array([3, 2, 1, 10, 9, 8, 4, 5, 6, 7])
print(a.argmax(), a.max())
```

numpy의 argmax(), argmin()을 이용해 최대, 최소 값의 위치를 손쉽게 알 수 있습니다. argwhere()를 사용하면 특정 데이터의 위치를 매우 간편히 찾을 수 있습니다.

```python
a = np.array([3, 2, 1, 10, 9, 8, 4, 5, 6, 7])
print(a.argmin(), a.min())
print(np.argwhere(a == 5))
```

바로 위 코드에서 볼 수 있듯이 argwhere()의 용법은 argmax(), argmin()과 다소 다르니 참고하세요.
