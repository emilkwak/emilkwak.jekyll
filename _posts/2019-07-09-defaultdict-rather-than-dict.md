---
layout: post
cover: 'assets/images/office01.jpg'
navigation: True
title: Python 딕셔너리(dict) 대신 쓰는 디폴트딕트(defaultdict)
date: 2019-07-09 22:30:00
tags: tech python defaultdict dict collections
subclass: 'post tag-speeches'
logo: 'assets/images/pooh03.png'
author: emil
categories: tech
---

딕셔너리 자료구조는 Python뿐 아니라 Java나 C++에서더 유용히 사용됩니다. Python의 딕셔너리는 `dict`라는 키워드로 선언되는데 Java 등에 비해 그 사용법이 간편하여 여러가지 용도로 자주 사용됩니다.

데이터 분석을 위한 데이터 처리 단계에서 튜플(`tuple`) 데이터를 다뤄야 하는 경우가 많습니다. 사실상 덩치가 큰 정형 데이터는 왠만하면 튜플의 리스트(`list`)로 주어진다고 해도 무방하지요. 이와 같이 튜플의 리스트 형태로 주어진 데이터를 능숙히 처리하는 게 데이터 분석의 중요 소양 중 하나이지요.

대표적인 경우가 딕셔너리(`dict`)에 데이터 주워 담기입니다. 딕셔너리의 키(key)로 군집화한 결과가 값(value)이 됩니다. 데이터를 잘 처리해 만든 딕셔너리 하나만으로도, Pandas 같은 라이브러리 없이도, 데이터 분석의 성과를 충분히 보여줄 수 있습니다. 그만큼 유용하게 중요하고 쓰이는 자료형이지요.

인구가 가장 많은 도시 순위 20위까지의 데이터가 주어졌을 때 어떤 국가의 도시가 순위에 많이 등장하는지 궁금할 수 있겠지요? 이 질문에 대한 대답을 찾기 위한 데이터 분석 과정을 거쳐 봅시다. 가장 간단하게는 다음과 같이 접근하겠죠.

```python
cities =\
[('Chongqing', 'China', 30165500),
 ('Shanghai', 'China', 24183300),
 ('Beijing', 'China', 21707000),
 ('Istanbul', 'Turkey', 15029231),
 ('Karachi', 'Pakistan', 14910352),
 ('Dhaka', 'Bangladesh', 14399000),
 ('Tokyo', 'Japan', 13515271),
 ('Moscow', 'Russia', 13200000),
 ('Guangzhou', 'China', 13081000),
 ('Shenzhen', 'China', 12528300),
 ('Mumbai', 'India', 12442373),
 ('São Paulo', 'Brazil', 12038000),
 ('Kinshasa', 'Democratic Republic of the Congo', 11462000),
 ('Tianjin', 'China', 11249000),
 ('Lahore', 'Pakistan', 11126000),
 ('Delhi', 'India', 11034555),
 ('Jakarta', 'Indonesia', 10624000),
 ('Dongguan', 'China', 10615000),
 ('Seoul', 'South Korea', 9806000),
 ('Foshan', 'China', 9279000)]

dict_by_country = dict()
for city in cities:
    city_name, country_name = city[0:2]
    try:
        dict_by_country[country_name].append(city_name)
    except KeyError:
        dict_by_country[country_name] = [city_name]
```

보시다시피 `KeyError`를 반드시 `except`해 줘야 합니다. 딕셔너리를 처음 선언하면 빈(empty) 상태이므로 키로 접근(access)하면 필연적으로 `KeyError`를 일으킵니다. 어쩔 수 없이 추가한 `try`/`except` 부분이 사실 참 눈에 거슬립니다. 필요한 이유는 알겠는데 정녕 저렇게 지저분하게 코드를 만들어야만 할까요?

이럴 때 사용할 수 있는 게 `defaultdict`입니다. `defaultdict`는 `collections` 라이브러리에 탑재되어 있습니다. `defaultdict`는 딕셔너리(dict)와 거의 똑같은 용법으로 사용할 수 있습니다. 다른 점은 나온 적 없는 키로 접근을 해도 `KeyError`를 일으키지 않고 무언가 초기 값(default value)를 내뱉는다는 데 있습니다. 그래서 이름이 `defaultdict`인가 보지요.

단, `defaultdict`를 선언할 때 `default_factory`라는 인자를 입력해야 하는데 일반적으로 Python의 기본 자료형을 씁니다. `int`, `float` 같은 자료형부터 `list`, `set` 같은 자료구조도 가능합니다. `defaultdict`를 이용해 위의 코드를 다시 구현해 보시죠. 훨씬 깔끔한 작성이 가능합니다.

```python
from collections import defaultdict

dict_by_country = defaultdict(list)
for city in cities:
    city_name, country_name = city[0:2]
    dict_by_country[country_name].append(city_name)
```

거추장스러운 `try`/`catch`를 사용하지 않아도 되어 훨씬 좋지요? `dict_by_country[country_name].append(city_name)`을 한번 살펴 봅시다. `defaultdict`인 `dict_by_country`는 처음 나오는(본 적 없는) 키의 접근을 필연적으로 겪을 것인데 이때 `defaultdict`는 빈 리스트(empty `list`)를 반환합니다. 여기에 `append()` 처리가 이어지는 것이고요.

또 다른 사례를 한번 보실까요? 위에서 본 인구 순위 데이터를 이용해 순위 목록 속 각 국가가 가지는 인구의 합이 어떻게 되는지 알아봅시다.

```python
# using dict
pop_by_country = dict()
for city in cities:
    country_name, pop = city[1:]
    try:
        pop_by_country[country_name] += pop
    except KeyError:
        pop_by_country[country_name] = pop

# using defaultdict
pop_by_country = defaultdict(int)
for city in cities:
    country_name, pop = city[1:]
    pop_by_country[country_name] += pop
```
역시, `defaultdict`를 사용한 코드가 훨씬 읽기 좋다는 점을 알 수 있겠죠? 어떤가요? `dict` 대신 무조건 `defaultdict`를 사용할 필요는 없습니다. 그렇지만 위에서 살표본 예시들과 비슷한 상황에서는 `dict` 대신 `defaultdict`를 사용해 훨씬 읽기 좋고 관리하기 편한 코드를 작성할 수 있습니다.
