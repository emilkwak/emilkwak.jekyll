---
layout: post
cover: 'assets/images/office01.jpg'
navigation: True
title: Python과 궁합 맞는 설정 파일 4총사 - ini, json, yaml, py
date: 2020-08-29 12:00:00
tags: tech python conf config configuration setting preference ini json yaml py
subclass: 'post tag-speeches'
logo: 'assets/images/pooh03.png'
author: emil
categories: tech
---

디스크에 설정(configuration)을 파일로 저장할 때 어떤 포맷(확장자)을 사용해야 하는지는 서비스를 개발할 때 누구나 고민하는 문제입니다. Python을 이용해 개발할 때도 당연한 일이지요.

서비스를 구현하는 데 꼭 필요하지만 프로그램 소스코드라고 하기에는 애매한 정보들이 이에 해당하는데, 영어 표현으로는 configuration, setting 또는 preference 등으로 표현합니다.

* 설정에 해당할 수 있는 사항의 예
    - 접속 정보(서버 IP 주소, 포트 번호 등)
    - 각종 모드(운영, 개발, 시뮬레이션 등)
    - 언어(영어, 한국어, 독일어 등)
    - 제어값(업로드 용량 상한, 동시 처리량 등)
    - 경로(아이콘 이미지, 데이터 파일 등)
    - 기타

설정을 저장하는 데 어떤 포맷을 쓰는가에 따라 설정 파일 확장자만 바뀌는 게 아니라, 설정 항목들이 표현되는 형식이 달라지고 나아가 이 항목들이 파일을 이루는 형태도 달라집니다.

대표적인 설정 파일 포맷은 다음과 같습니다.

* 대표적인 설정 파일 포맷
    1. ini
    2. json
    3. yaml
    4. py

위의 대표 설정 파일 포맷 네 가지에 대한 간단한 설명과 특징, 그리고 Python으로 읽는 방법을 소개합니다.

---
## 1. ini - 오랜 전통의 강자

ini는 MS-DOS에서 초기 설정값을 저장해 두는 확장자였습니다. 초기화를 의미하는 initialization의 앞 세 자를 딴 것이죠. 오랜 전통을 자랑하며 많은 사람들이 '아, 저 파일은 설정 파일이구나!'라고 쉽게 인식하지요.

경우에 따라 확장자를 ini 대신 conf, cfg로 쓰기도 하지만 내용적인 형식은 ini, conf, cfg가 모두 같습(비슷합)니다. ini는 독특하게 섹션(section)의 개념이 있어 설정을 그룹화할 수 있습니다.

#### ini 파일 형식/형태 - conf.ini

```ini
[1st_section]
1st_key=value_1
2nd_key=value_2

[2nd_section]
last_key=value_last
```

#### Python으로 ini 파일 읽기 - conf.ini

```python
from configparser import ConfigParser

config = ConfigParser()
config.load('conf.ini')
print(config['1st_section']['1st_key']) # value_1
```

---
## 2. json - 최근 사실상 업계 표준

json은 xml를 대체하는 표준으로 급부상했는데 형태를 보면 Python의 딕셔너리(dict)와 닮아 있습니다. 구조적인 측면에서 사실상 동일하지요.

데이터 형(type)도 인식하기 때문에 수(number), 문자열(string), 부울(boolean), 배열(array), 객체(object), 널(null)로 세분화해 값을 표현할 수 있습니다.

우리나라 오픈 데이터 포맷으로도 json을 사실상 표준으로 인정하고 있고 업계에서도 키-값 구조의 데이터를 json이라 통칭하는 관례도 있습니다.

#### json 파일 형식/형태 - conf.json

```json
{
    "1st_key": ["value_1", "value_2"],
    "2nd_key": "value_last"
}
```

#### Python으로 json 파일 읽기 - conf.json

```python
import json

with open('conf.json') as f:
    config = json.load(f)
print(config['2nd_key']) # value_last
```

---
## 3. yaml - 인간이 읽기 가장 편안한 형식

yaml은 구조화된 데이터를 직렬화(serialization)하는 데 초점을 둡니다. 얼핏 보면 마크다운 문서와 비슷한 느낌을 줄 정도로, 사람이 읽기에도 가장 편안합니다.

yaml은 하이픈 불릿으로 열거된 값들이 주어지면 이를  리스트(또는 배열)로 인식한다는 특징이 있습니다.

#### yaml 파일 형식/형태 - conf.yaml

```yaml
1st_key: value_1
2nd_key: value_2
last_key:
  - list_value_1
  - list_value_2
```

#### Python으로 yaml 파일 읽기 - conf.yaml
```python
import yaml

with open('conf.yaml', 'r') as f:
    config = yaml.load(f)
print(config['1st_key']) # value_1
```

---
## 4. py - Python 파일을 그냥 그대로?

굳이 ini, json, yaml을 사용할 것 없이, Python으로 개발중이라면 py 파일을 설정 파일로 써도 됩니다. Python의 딕셔너리 데이터 그 자체가 사실상 json과 마찬가지라는 것은 따로 설명드릴 필요 없겠지요?

py를 설정파일로 쓰면 ini의 configparser 같은 라이브러리를 별도로 쓸 필요가 없습니다. 그냥 설정 py 파일을 import해 주는 것으로 충분합니다.

#### py 설정 파일 형식/형태 - conf.py

```python
config =\
    {
        '1st_key': ['value_1', 'value_2'],
        '2nd_key': 'value_last',
    }
```

#### Python으로 py 설정 파일(conf.py) 읽기 - conf.py

```python
from conf import config

print(config['2nd_key']) # value_last
```

이외에도, 또 다른 전통인 xml을 설정 파일로 쓰기도 하고, SQLite 파일을 쓰기도 합니다. toml과 같은 포맷도 쓰이기도 하나 자주 보지는 못한 것 같네요.

어떤 포맷을 쓸지는 구현할 서비스나 협업 그룹, 환경에 따라 달라질 수도 있습니다. 소개드린 네 종류 포맷 중 하나를 쓴다 생각하시면 충분하지 않을까 합니다.

---
* 참고자료
    - [(stackoverflow) Python: How would you save a simple settings/config file?](https://stackoverflow.com/questions/19078170/python-how-would-you-save-a-simple-settings-config-file)
    - [(github) Configuration files: ini, json, yaml or python](https://github.com/aio-libs/aiohttp-devtools/issues/2)
    - [(wikipedia) INI file](https://en.wikipedia.org/wiki/INI_file)
    - [(wikipedia) JSON](https://en.wikipedia.org/wiki/JSON)
    - [(wikipedia) YAML](https://en.wikipedia.org/wiki/YAML)