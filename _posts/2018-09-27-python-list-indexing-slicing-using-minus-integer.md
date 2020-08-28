---
layout: post
cover: 'assets/images/office01.jpg'
navigation: True
title: Python 리스트 인덱싱 & 슬라이싱에 음의 값(마이너스) 사용해 보기
date: 2018-09-27 00:00:00
tags: tech python list indexing slicing
subclass: 'post tag-speeches'
logo: 'assets/images/pooh03.png'
author: emil
categories: tech
---

Python의 가장 큰 매력 중 하나가 리스트 인덱싱과 슬라이싱(list slicing and slicing)입니다. 각괄호와 콜론을 이용해서 리스트의 원소나 리스트의 일부를 쉽게 취할 수 있지요. C나 Java에서 배열 인덱싱, 슬라이싱했던 것과 비교하면 가독성과 간결성 측면에서 비교가 되지 않습니다.

인덱싱과 슬라이싱 외에도 Python의 리스트 연산(list operation)은 편리하기로 유명합니다. 여기에서는 음의 값(minus integer)을 이용한 리스트 인덱싱, 슬라이싱을 살펴 보겠습니다.

우선 아래와 같이 실험용 리스트를 하나 만들어 보겠습니다. 0부터 9까지 10개의 정수로 채워진 리스트이죠. 리스트 속 각 인덱스 위치에는 인덱스와 동일한 정수 값이 들어 있는 형태입니다. `a[i] = [i]`인 형태 말이죠.

```
a = list(range(10))
print(a) # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

이 리스트의 인덱스로 음의 값을 주면 어떻게 될까요? C를 생각하면 `a[0]`의 주소값 이전의 메모리 영역을 가리키게 되겠죠. Python에서는 그렇지 않습니다. 음의 값인 -n으로 인덱싱을 하면 뒤에서 n번째 요소를 가리키는 셈이 됩니다.

```
print(a[-1], a[-2]) # 9 8
```

위를 보시면 `a[-1]`은 뒤에서 1번째 요소인 9를, `a[-2]`는 뒤에서 2번째 요소인 8을 가리키는 사실을 볼 수 있습니다.
이를 응용해 음의 값으로 슬라이싱을 할 수도 있습니다.

```
print(a[-2:]) # [8, 9]
print(a[:-2]) # [0, 1, 2, 3, 4, 5, 6, 7]
print(a[-3:-1]) # [7, 8]
```

위를 보건대, `a[-2:]`는 뒤에서 2번째 요소인 8부터 마지막 요소인 9까지를 슬라이싱하고 `a[:-2]`는 처음 요소부터 뒤에서 2번째 요소인 8까지 슬라이싱하되 8은 결과에 포함하지 않습니다. 원래 Python 리스트를 `a[start:stop]`과 같이 슬라이싱할 때 `stop` 위치에 해당하는 요소는 미포함(exclusive)하지요.

리스트 슬라이싱의 `stride`에도 음의 값을 넣을 수 있습니다. `a[start:stop:stride]`와 같이 슬라이싱할 때, `stride`는 요소를 취하는 빈도를 나타냅니다. `stride`가 2라면 하나 건너 하나를 취하겠죠. 아래처럼 말이죠.

```
print(a[::2]) # [0, 2, 4, 6, 8]
```

`stride`를 음의 값으로 하면 리스트가 역방향으로 슬라이싱됩니다. 이를 이용해 리스트의 역순 정렬(reverse sorting)도 할 수 있습니다. 아래의 `a[-1:-3:-1]`을 같이 보실까요? 뒤에서 1번째 요소인 9부터 뒤에서 3번째 요소인 7까지 슬리이싱하되 7은 결과에 포함하지 않겠지요? 따라서 결과가 `[9, 8]`이 됩니다.

```
print(a[::-1]) # [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]
print(a[-1:-3:-1]) # [9, 8]
```