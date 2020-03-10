---
layout: post
cover: 'assets/images/office01.jpg'
navigation: True
title: Python 인라인 if절(inline if statement)에 elif 표현하기
date: 2019-04-24 11:30:00
tags: tech python inline if elif lambda conditinal branch
subclass: 'post tag-speeches'
logo: 'assets/images/pooh03.png'
author: emil
categories: tech
---

Python은 컴팩트한 코드를 지향하기 때문에 다양한 장치를 제공하는데, 그중 대표적인 것이 inline if절(statement)입니다. 사실 C/C++나 Java에도 비슷한 문법이 있습니다. 아주 새롭지는 않지요.

```c
int a, b;
a = 0;
b = (a == 0) ? 0 : 1;
```

Python도 거의 유사한 문법을 제공합니다.

```python
a = 0
b = 0 if a == 0 else 1
```

Python의 inline if절이 가독성 면에서 뛰어나지요. 그리고 Python에서 손쉽게 사용되는 lambda 함수(function)와 어우러져 사용하면 용도가 더 빛나게 됩니다.

```python
func = lambda a: 0 if a == 0 else 1
```

Python inline if절을 쓰다보면 if와 else 말고 elif를 쓰는 방법은 없을까 싶지요. 물론 inline if절의 문법에 elif를 쓸 수 없게 되어 있습니다. 그럼에도 불구하고 사실상 elif를 구현하는 방법이 있습니다. 아래와 같은 inline이 아닌 if 조건 분기가 있다고 가정해 보지요.

```python
if a == 'dog':
    b = 'woof'
elif a == 'cat':
    b = 'meow'
elif a == 'pig':
    b = 'oink'
else:
    b = 'hello'
```

어떻게 이것을 inline if절로 표현할 수 있을까요? inline if절의 else 부분에 괄호로 싸인 또 다른 inline if절을 사용하면 됩니다. 위 if 조건 분기는 아래처럼 바뀔 수 있겠지요.

```python
b = 'bark' if a == 'dog' else ('meow' if a == 'cat' else ('oink' if a == 'pig' else 'hello'))
```

한 줄이 좀 길긴 하지만, 어쨌든 inline은 맞지요? 물론 이런 경우에는 inline화 시키면서 가독성이 오히려 떨어집니다. 그럼에도 불구하고 가끔 이렇게 긴 inline if절이 유용할 때가 있지요. lambda 함수로 정의할 때가 그렇습니다.

```python
func = lambda a: 'bark' if a == 'dog' else ('meow' if a == 'cat' else ('oink' if a == 'pig' else 'hello'))
```

간혹 lambda 함수에 elif를 표현하고 싶은 때가 있죠? 그럴 때 활용하면 좋은 방법입니다. 실제로 위 lambda 함수에 각 인자를 입력하면 의도된 값이 잘 나옴을 볼 수 있습니다.

```python
print(func('dog'))
# 'bark'
print(func('human'))
# 'hello'
```

앞서 보신 것처럼 elif의 표현이 많은 inline if절은 가독성이 떨어집니다. 이를 항상 유념하고 사용하면 보다 좋겠지요?
