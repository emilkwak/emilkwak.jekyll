---
layout: post
cover: 'assets/images/office01.jpg'
navigation: True
title: Python 딕셔너리(dict) 키(key)를 변수로 만들기
date: 2019-04-14 23:30:00
tags: tech python numpy
subclass: 'post tag-speeches'
logo: 'assets/images/pooh03.png'
author: emil
categories: tech
---

Python의 딕셔너리(`dict`)는 C++나 Java의 그것보다 훨씬 유연하고 사용하기도 편합니다. 딕셔너리가 Python에서 자주 사용되는 이유이기도 하지요. 딕셔너리를 선언하고 정의하는 방법도 매우 간단합니다.

```python
d = {'var_a': [1, 2, 3],
     'var_b': [4, 5, 6],
     'var_c': [7, 8, 9],}
```

이렇게 정의된 딕셔너리의 각 요소는 `d['var_a']`와 같이 접근할 수 있지요.

한편, 경우에 따라서 `print(d['var_a'])`처럼 사용하는 것보다 `print(var_a)`처럼 변수로 만들어 사용하는 것이 편할 때가 있지요. 작업중이던 변수들을 딕셔너리로 묶어 `pickle.dump()`했다가 다시 `pickle.load()`할 때 등이 대표적인 경우이겠죠.

```python
for k, v in d.items():
    exec('%s = %s' % (k, v))
```

위와 같이 하면 딕셔너리의 각 요소를 변수로 만들 수 있습니다. 두 줄짜리 간단한 코드지만 원하는 바를 쉽게 달성하게 해 주죠. 위 코드를 간단히 살펴 봅시다. `d.items()`는 딕셔너리를 키(key)와 값(value)의 쌍으로 나열시키고, 이를 `for`로 iteration합니다. 각 iteration에서 키를 변수명으로 삼아 변수에 값을 할당하는 구문을 문자열로 만들고 이 문자열을 `exec()`에 입력시킵니다. `exec()`은 말그대로 주어진 문자열을 Python 구문으로 여겨 실행시키지요.

생각보다 간단하고 짧은 `for` 루프만으로 딕셔너리 각 요소의 변수로의 전환이 가능하지요?