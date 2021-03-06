---
layout: post
cover: 'assets/images/office01.jpg'
navigation: True
title: Anaconda3(conda) 환경 Jupyter Notebook 오류(ImportError) 해결
date: 2019-04-17 22:30:00
tags: tech python jupyter anaconda conda spyder
subclass: 'post tag-speeches'
logo: 'assets/images/pooh03.png'
author: emil
categories: tech
---

Anaconda3로 Python 환경을 설치하고 나서 Jupyter Notebook을 사용하려 할 때, 다음과 같은 오류가 발생할 때가 있습니다. Windows 운영체제라고 가정할 때(다른 OS도 크게 다를 것은 없습니다만), 명령 프롬프트(cmd)를 실행해 `C:\Users\ABC\Anaconda3\Scripts` 디렉토리로 이동한 후 `jupyter.exe notebook`을 실행하게 되지요.

> ImportError: DLL load failed: 지정된 모듈을 찾을 수 없습니다.

conda 환경이 아닌 맥락(context)으로 Anaconda3로 설치된 도구를 실행했기 때문으로 추정됩니다. 명확한 원인을 공식적으로 확인하지는 못했습니다. Scripts 디렉토리 내에 있는 다른 도구를 실행해도 유사한 오류가 발생하는 것을 보아 그렇게 추정이 됩니다.

Jupyter Notebook뿐 아니라, Python IDE인 Spyder 또한 실행되지 않습니다. Jupyter Notebook과는 다른 오류가 뜨지만 상황은 비슷해 보입니다. conda 환경에 맞춰 구성된 requirements와 dependencies가 충분히 해소되지 않기 때문이지요. 같은 `Scripts` 디렉토리에서 `spyder.exe`를 실행해 보시지요.

> qtpy.PythonQtError: No Qt bindings could be found

이를 해결하기 위한 여러가지 방법이 있겠지만, 가장 손쉬운 방법은 'conda 환경 갖추기'가 되겠습니다. 방법은 간단합니다. `Scripts` 디렉토리에서 `activate.bat`를 먼저 실행하면 됩니다. 아래처럼 하면 Jupyter Notebook이 위에서 살펴 본 오류 없이 잘 실행됩니다.

```
cd C:\Users\ABC\Anaconda3\Scripts
activate.bat
jupyter.exe notebook
```

`activate.bat`을 하고 나면 명령 프롬프트 커서 행의 좌측에 '(base)'라는 표시가 생깁니다. conda 환경, 정확히는 conda의 base 환경이 갖춰진 상태임을 뜻합니다. 이 상태에서는 Jupyter Notebook뿐 아니라 Spyder도 오류 없이 정상 구동됩니다.