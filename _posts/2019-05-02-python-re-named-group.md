---
layout: post
cover: 'assets/images/office01.jpg'
navigation: True
title: Python 정규표현식(regular expression) 그룹 이름 붙이기(named group)
date: 2019-05-02 01:35:00
tags: tech python regularexpression regex re namedgroup backreference
subclass: 'post tag-speeches'
logo: 'assets/images/pooh03.png'
author: emil
categories: tech
---

지금의 Python은 데이터 과학(data science)의 주류 언어로 각광받으면서, 프로그래밍 언어로서 황혼기를 누리고 있습니다. 데이터 과학의 러브콜을 받기 전 Python이 대중화가 될 수 있었던 이유는 강력한 문자열 처리에 있습니다. 사실 데이터 과학 분야에서도 Python의 문자열 처리 기능이 매우 유용하게 사용되지요. Tesorflow나 PyTorch 등 기계학습(machine learning) 및 인공지능(AI)라이브러리 또한 Python의 문자열 처리 기능 덕분에 더 빛을 발하지요.

Python에서도 문자열 처리 기능으로서 정규표현식(regular expression, regex)이 매우 유용히 사용됩니다. 사실 정규표현식은 Python만의 개념이 아니지요. 정규표현식 개념의 등장은 무려 1950년대로 거슬러 올라 갑니다. Python의 문자열 처리 기능의 강력함을 정규표현식만으로 설명할 수는 없는 노릇이지요. Python이 제공하는 특유의 문자열 처리 도구들과 특화된 라이브러리들과 정규표현식이 어우려져 사용될 때 비로소 막대한 시너지가 생깁니다.

`startswith()`, `endswith()` 같은 기본 제공 모듈, `in`과 같은 키워드, `[:3]`, `[-5:]`와 같은 슬라이싱(slicing) 기능을 잘 활용하는 것도 물론 중요하겠지만, Python이 제공하는 정규표현식 라이브러리인 re를 능수능란히 다루는 능력 또한 중요합니다. 물론 정규표현식에 대한 기본 지식 또한 중요하겠지요.

여기에서는 Python의 re 라이브러리를 사용할 때 정규표현식 속 그룹에 이름을 붙여 이용하는 방법(named group)과 이름 붙여진 그룹에 직접 접근하는 방법을 살펴 보겠습니다. 이름 붙여진 그룹이라 함은, 별다른 뜻은 아니고, 정규표현식 속 일부가 되는 특정 패턴에 명시적인 이름을 붙였을 때를 말합니다.

가장 기본이 되는, 정규표현식 속 그룹에 이름을 붙이는 방법은 아래와 같습니다. `(?P<NAME>...)`은 괄호 속 패턴 부분에 NAME이라고 이름을 붙이는 방법입니다.

```python
import re

comp = re.compile('\s+(?P<NAME>\w+)\s+speaking')
result = comp.search('This is Michael speaking.')
print(result)
# <re.Match object; span=(7, 24), match=' Michael speaking'>
```

위 코드에서는 붙인 이름으로 `search()`로 찾은 패턴에 직접 접근하지 않습니다. 아래 코드처럼 하면 붙인 이름을 통해 패턴에 직접 접근할 수 있습니다.

```python
print(result.group('NAME'))
# Michael
```

하나의 정규표현식에 둘 이상의 그룹을 잡고 각각 다른 이름을 붙일 수 있습니다. 이처럼 그룹 수가 많아지면 `group(...)`을 여러번 쓰는 것보다, 이름과 패턴의 쌍들로 만들어진 딕셔너리(dictionary)를 `groupdict()`로 받아 와 쓰는 게 편리합니다.

```python
comp = re.compile('\s+(?P<NAME1>\w+)\s+and\s+(?P<NAME2>\w+)\s+speaking')
result = comp.search('This is John and Jane speaking.')
print(result.groupdict())
# {'NAME1': 'John', 'NAME2': 'Jane'}
```

한편 정규표현식에 이미 등장해 이름 붙여 놓은 그룹을 다시 참조해 같은 식에 재활용하는 방법도 있습니다. 이를 named group backreference라고 부릅니다.

```python
comp = re.compile('\s+(?P<NAME>\w+)\s+speaking\.\s+(?P=NAME)\s+is online\.')

result_a = comp.search('This is Michael speaking. Michael is online.')
print(result_a.group('NAME'))
# Michael
print(bool(result_a))
# True

result_b = comp.search('This is Thomas speaking. Paul is online.')
print(result_b.group('NAME'))
# AttributeError: 'NoneType' object has no attribute 'group'
print(bool(result_b))
# False
```

위 코드를 보면, `result_a`에서는 정규표현식의 앞부분에서 찾은 패턴 Michael이 뒤에 다시 등장하여 정상적으로 탐색(search)이 된 것을 알 수 있습니다. 반면 `result_b`에서는 앞부분 패턴은 Thomas지만 뒷부분 패턴은 Paul입니다. 앞뒤 패턴이 달라 탐색이 실패한 것이지요. 참고로, re 라이브러리의 정규표현식 결과를 `bool()`로 캐스팅(casting)하면, 패턴이 찾아진 경우 True, 아닌 경우 False를 반환합니다.