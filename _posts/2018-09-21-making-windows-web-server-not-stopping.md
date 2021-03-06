---
layout: post
cover: 'assets/images/office01.jpg'
navigation: True
title: Windows CLI로 구동한 웹 서버 멈춤 현상 해결 - 빠른 편집 모드 해제
date: 2018-09-21 00:00:00
tags: tech python windows
subclass: 'post tag-speeches'
logo: 'assets/images/pooh03.png'
author: emil
categories: tech
---

Windows OS에서 웹 서버를 구동해야 할 일이 종종 있습니다. Microsoft IIS가 Windows의 대표적인 웹 서버 소프트웨어인데 Windows Server OS를 꼭 써야 할 상황이 아니고선 흔히 쓰이지 않지요. Apache가 가장 보편적으로 널리 쓰이는 웹 서버 소프트웨어가 아닐까 합니다. Windows에서도 WAMP를 통해 MySQL(MariaDB), PHP까지 통합하여 간편하게 설치해 사용하라 수 있습니다.

Apache는 아무래도 무겁습니다. 요즘 핫한 Python과도 궁합이 아주 맞지는 않는 편이지요. Apache의 아성은 여전히 건재하지만 '그렇게나 제대로된 웹 서버가 아니더라도 성능은 적당하고 간편한 게 좋다'면 Apache 외에도 다른 선택지들이 많습니다.

Python 사용자들에게 가장 친숙한 웹 프레임워크는 Django와 Flask입니다. 아직 Python 기반의 웹 서버를 이야기하고 있지 않습니다. Python 기반 웹 프레임워크에 관해 이야기하고 있습니다. Maximal한 기능을 제공하는 Django와 Minimalism을 추구하는 Flask, 둘 다 매력이 있는 웹 프레임워크이지요.

Django와 Flask는 모두 웹 서버는 아니지만 개발용 간이 서버 기능을 포함하고 있기는 합니다. 프로덕션 차원에서는 쓰지 않기를 권장하고 있지만 서비스 트래픽이 과대하지 않다면 또는 접속량이 많지 않다면 임시적으로 프로덕션에 활용할 수도 있습니다. 좀 더 안정적인 웹 서비스를 위해서는 제대로 된 웹 서버 소프트웨어를 써야 하겠지요. Django, Flask와 궁합이 잘 맞는 웹 서버가 물론 있습니다.

대표적인 Python 기반의 웹 서버로 Gunicorn, Waitress 등이 있습니다. Django, Flask와 같이 WSGI 사양을 준수하는 웹 프레임워크는 WSGI 사양을 준수하는 웹 서버와 최적의 호환성을 자랑합니다. Gunicorn과 Waitress는 WSGI 웹 서버이고 Django, Flask는 WSGI 웹 프레임워크이지요. 이들 간에 최고의 궁합을 볼 수 있습니다.

저는 Waitress를 좋아하는데 Linux뿐 아니라 Windows에서도 잘 구동되기 때문입니다. Gunicorn이 사실 더 유명하긴 하지만 UNIX/Linux 전용이라 Windows에서는 사용하기 힘들죠. 이런 차원에서 Waitress는 Python의 가장 큰 장점인 크로스 플랫폼 지원이 더 빛을 발하게 해 줍니다.

서론이 좀 길었습니다만, Django 또는 Flask의 개발용 간이 서버를 이용하든, Gunicorn이나 Waitress 등의 웹 서버를 이용하든, Windows에서 서버 구동을 하기 위해서는 종종 CLI(DOS 창 또는 cmd라고 부르는)를 이용해야 할 상황이 많습니다. 다음과 같은 Python 파일을 CLI에서 실행하게 되겠죠.

```
# 파일명: server.py
from waitress import serve
serve(wsgiapp, host='0.0.0.0', port=8080) # wsgiapp: WSGI 사양을 준수한 웹 어플리케이션이라 가정
```

Windows CLI에서 `python server.py`와 같이 위 Python 파일을 구동합니다. 웹 서버 프로세스가 생성되어 실행되고 Busy Running하게 되겠죠. 명령이 종료되지 않고 Listening 모드로 유지된 채 커서만 깜빡거리고 있겠지요. 당연한 상황입니다. 쉬운 말로 설명하려는 시도가 오히려 어색할 따름입니다.

그런데 그런 경험 없나요? CLI 창(DOS 창)은 Listening 모드로 열러 있고 컴퓨터도 정상적으로 켜져 있으며 웹 서버 구동 직후 접속이 잘 됐었는데, 두어 시간 후 다시 접속하려면 접속되지 않는 현상을 겪은 적이 있나요? 원인을 찾을 수 없어 리부팅까지 해 봤는데도 여전히 반복되지요.

혹시나 해서 CLI 창(Busy Running 중인)에 엔터 키를 입력해 보니, 접속 안될 때 서버로 들어 왔던 트래픽들이 버퍼링되어 있다가 한꺼번에 밀려 들어와 당황했던 적이 있나요? 몇 시간에 한 번씩 CLI 창에 엔터 키를 반복해서 눌러 줘야 하는 건가, 정녕 다른 방법은 없는 것인가 고민도 많이 했습니다.

Linux Shell에 똑같이 하면 이런 문제가 없는데 왜 Windows에서만 문제일까 궁금합니다. 사실 명확한 원인은 모르겠습니다. 추측건대 CLI가 사용자 입력을 받기 위해 대기하는 가운데 키보드 입출력을 버퍼링하는 기능이 있어서 그것이 웹 서버의 출력까지 버퍼링 처리하고, 그 과정에서 무언가 꼬여서 먹통이 되는 게 아닐까 합니다. 이때 사용자로부터 엔터 키 입력이 들어오자 버퍼가 비워지고 먿었던 서버가 작동하게 되는 것이고요.

원인은 잘 모르지만 어쨌든 해결책이 있어 소개드리겠습니다. Windows CLI 창이 열려 있다는 가정 하에(`Win+R` 후 `cmd` 실행) 다음과 같이 해 보시지요.

* Windows CLI 창의 타이틀 바 위에서 우클릭
* 컨텍스트 메뉴에서 '속성(P)' 선택
* '편집 옵션'의 '빠른 편집 모드(Q)' 체크박스 해제

이와 같이 처리한 후 CLI로 웹 서버를 구동하면 입출력 버퍼링이 원인으로 추측되는 웹 서버 멈춤 현상이 발생하지 않게 됩니다. 사실 이 글의 목적은 이 방법을 알려 드리기 위함입니다. 결론에 앞서 서론이 너무 길었는데요, 서론 없이 바로 방법을 설명했다면 어떤 상황에 대한 공감이 잘 되지 않았을 것 같습니다.
