---
layout: post
cover: 'assets/images/office01.jpg'
navigation: True
title: NumPy(넘파이) PyInstaller 패키징 시 실행파일(exe) 용량이 너무 큰 경우 해결책
date: 2020-04-12 15:00:00
tags: tech python pandas dataframe indexing selecting mask loc
subclass: 'post tag-speeches'
logo: 'assets/images/pooh03.png'
author: emil
categories: tech
---

`PyInstaller`는 Python 프로그램 배포를 목적으로 많은 분들이 사용하는 패키지입니다. `cx_Freeze`나 `py2exe` 등 같은 용도의 패키지가 대체재로 존재하지만 사용법이 간단해 더 인기가 있습니다.

`NumPy`는 Python 사용자라면 한번쯤 사용해 본 패키지일 것입니다. Python이 대중적인 데이터 분석용 언어로 각광받게 만든 패키지 중 하나가 `NumPy`라 보아도 무방하지요.(다른 하나는 `pandas`라 할 수 있지요.)

Windows 10 환경에서 `NumPy`를 사용하는 Python 프로그램을 작성한 후 `PyInstaller`로 패키징하면 패키징된 용량이 지나치게 큰 경향이 있어 의아한 분들이 많을 것입니다. `NumPy`를 포함해 두세 개 패키지만 `import`했는데 용량이 200 ~ 300MB라니, 당혹스럽지 않을 수 없습니다.

혹시, Windows 10에 Ananconda나 Miniconda를 설치해 쓰고 계신 가운데, `NumPy` 패키지를 `conda`로 설치하지는 않았나요? Anaconda 또는 Miniconda를 쓰는 것은 상관 없지만, `NumPy`를 `conda install numpy`로 설치한 경우 `PyInstaller` 패키징 용량이 매우 커진다는 사실을 발견했습니다. 반면, `pip`로 설치하면 적정한 수준의 용량으로 패키징이 가능합니다.

이는 `conda install numpy` 시 mkl 계열의 dll 파일이 대거 패키징되기 때문입니다. 한편, 똑같은 `NumPy`를 `pip`로 설치하면 mkl 계열 dll이 제외되는데 덕분에 용량을 덜 차지하지요. 실제 용량 차이가 어느정도까지 나는지 실험을 통해 알아봤습니다.

실험의 공통적인 시스템 환경은 다음과 같습니다.

* Windows 10, 64비트, Anaconda 3 설치
* Python 3.7
* Python 가상환경 `conda create`로 생성
* `PyInstaller`: 3.6
* `NumPy`
  - `conda install`: 1.18.1
  - `pip install`: 1.18.2

## 실험 1: `conda install numpy` 후 `PyInstaller` 패키징

우선 Python 3.7 기준의 Python 가상환경을 만들고, `NumPy`를 `conda install`로 설치합니다. `PyInstaller`까지 설치(`pip install`로)합니다.

```
conda create -n numpy_from_conda python=3.7
conda activate numpy_from_conda
conda install numpy
pip install pyinstaller
```

이제 `NumPy`를 쓰는 간단한 Python 프로그램을 작성합니다. 임의의 행렬 두 개를 행렬곱하는 예제입니다. 오로지 `NumPy`만 `import`해 사용한 간단한 소스코드라고 할 수 있지요. 똑같은 예제를 두 번째 `pip install numpy` 실험에도 사용할 예정입니다.

```python
# matmul_example.py
import numpy as np

if __name__ == '__main__':
    mat_a = np.random.randint(100, size=15).reshape(5, 3)
    mat_b = np.random.randint(100, size=12).reshape(3, 4)
    np.matmul(mat_a, mat_b)
```

이제 `PyInstaller`로 패키징을 해 보겠습니다. `--onefile` 옵션을 적용한 경우와 그렇지 않은 경우, 두 경우를 모두 보겠습니다. 참고로, `numpy_from_conda`로 `conda activate`된 상태를 유지해야 합니다.

```
pyinstaller --onefile matmul_example.py
dir dist\matmul_example.exe

2020-04-11  오후 ..:..       206,681,582 matmul_example.exe
               1개 파일         206,681,582 바이트
```

`--onefile` 옵션 없이 패키징하면 어떻게 될까요?

```
pyinstaller --onefile matmul_example.py
dir dist\matmul_example

2020-04-11  오후 ..:..    <DIR>          .
2020-04-11  오후 ..:..    <DIR>          ..
2020-04-11  오후 ..:..         3,554,703 matmul_example.exe
2020-04-11  오후 ..:..             1,037 matmul_example.exe.manifest
2020-04-03  오후 ..:..            19,208 api-ms-win-core-console-l1-1-0.dll
2020-04-03  오후 ..:..            18,696 api-ms-win-core-datetime-l1-1-0.dll
2020-04-03  오후 ..:..            18,696 api-ms-win-core-debug-l1-1-0.dll
.......
2020-04-11  오후 ..:..    <DIR>          mkl
2020-04-06  오전 ..:..        43,504,000 mkl_avx.dll
2020-04-06  오전 ..:..        40,924,032 mkl_avx2.dll
2020-04-06  오전 ..:..        51,629,952 mkl_avx512.dll
.......
2020-04-06  오전 ..:..        71,339,904 mkl_core.dll
2020-04-06  오전 ..:..        35,575,168 mkl_def.dll
2020-04-06  오전 ..:..        32,865,664 mkl_intel_thread.dll
2020-04-06  오전 ..:..        41,196,416 mkl_mc.dll
2020-04-06  오전 ..:..        42,454,400 mkl_mc3.dll
.......
2020-04-06  오전 ..:..        28,328,832 mkl_pgi_thread.dll
2020-04-06  오전 ..:..        15,146,880 mkl_rt.dll
2020-04-06  오전 ..:..         7,586,176 mkl_scalapack_ilp64.dll
2020-04-06  오전 ..:..         7,527,296 mkl_scalapack_lp64.dll
2020-04-06  오전 ..:..        18,359,168 mkl_sequential.dll
2020-04-06  오전 ..:..        20,887,936 mkl_tbb_thread.dll
2020-04-06  오전 ..:..        13,028,224 mkl_vml_avx.dll
2020-04-06  오전 ..:..        12,949,376 mkl_vml_avx2.dll
2020-04-06  오전 ..:..        12,604,800 mkl_vml_avx512.dll
2020-04-06  오전 ..:..         6,645,632 mkl_vml_cmpt.dll
2020-04-06  오전 ..:..         6,877,056 mkl_vml_def.dll
2020-04-06  오전 ..:..        12,032,896 mkl_vml_mc.dll
2020-04-06  오전 ..:..        11,888,512 mkl_vml_mc2.dll
2020-04-06  오전 ..:..        11,977,088 mkl_vml_mc3.dll
...
              91개 파일         565,684,976 바이트
```

요컨대 `--onefile` 때는 206MB, `--onefile` 아닐 때는 무려 565MB로 패키징됩니다. 행렬곱 하나 하는 장난감 프로그램의 크기가 이정도라는 게 말이 안되지요? mkl 계열 dll들이 큰 용량의 주범인데 이것들을 어떻게 제외시킬 수 있을까요?

## 실험 2: `pip install numpy` 후 `PyInstaller` 패키징

이번에는 Python 3.7 기준 가상환경을 만들고, `NumPy`를 `pip install`로 설치합니다. `PyInstaller`까지 설치합니다.

```
conda create -n numpy_from_pip python=3.7
conda activate numpy_from_pip
pip install numpy pyinstaller
```

첫 번째 실험(`conda install numpy`)때의 예제 프로그램을 `PyInstaller`로 패키징해 보겠습니다. `numpy_from_pip`로 `conda activate`된 상태여야 합니다.

```
pyinstaller --onefile matmul_example.py
dir dist\matmul_example.exe

2020-04-11  오후 ..:..        19,334,062 matmul_example.exe
               1개 파일          19,334,062 바이트
```

`--onefile` 옵션을 적용해 보겠습니다.

```
pyinstaller --onefile matmul_example.py
dir dist\matmul_example

2020-04-11  오후 ..:..    <DIR>          .
2020-04-11  오후 ..:..    <DIR>          ..
2020-04-11  오후 ..:..         3,554,246 matmul_example.exe
2020-04-11  오후 ..:..             1,037 matmul_example.exe.manifest
2020-04-03  오후 ..:..            19,208 api-ms-win-core-console-l1-1-0.dll
2020-04-03  오후 ..:..            18,696 api-ms-win-core-datetime-l1-1-0.dll
2020-04-03  오후 ..:..            18,696 api-ms-win-core-debug-l1-1-0.dll
...
              59개 파일          49,431,319 바이트
```

`--onefile` 때는 19MB, `--onefile` 아닐 때는 49MB로 패키징됩니다. 뭔가 딱 예상했던, 적정해 보이는 크기이죠? mkl 계열 dll이 하나도 생성되지 않았음을 볼 수 있습니다.

```
dir dist\matmul_example

파일을 찾을 수 없습니다.
```

`PyInstaller`를 사용한 Python 프로그램 패키징 배포 시 용량이 과도하게 크다 싶으면 `NumPy`가 `conda`로 설치되었는지 확인해 보시고 그렇다면 `pip`로 재설치해 보시기 바랍니다. 매우 간단한 행렬곱 프로그램을 패키징하는데, `--onefile` 기준으로 19MB면 되었을 것이 206MB가 될 뻔 했으니까요.(`--onefile` 아닌 경우에는 더 심각합니다. 49MB vs. 565MB이니까요.)

## 참고자료
* [MKL DLLs cannot be excluded using --exclude-module](https://github.com/pyinstaller/pyinstaller/issues/2270)
