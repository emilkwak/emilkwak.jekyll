---
layout: post
cover: 'assets/images/office01.jpg'
navigation: True
title: Python 토이 데이터셋 3대장 - Scikit-Learn, Statsmodels, Vega Datasets
date: 2020-08-10 00:30:00
tags: tech python toy data datasets scikit sklearn statsmodels vega rdatasets
subclass: 'post tag-speeches'
logo: 'assets/images/pooh03.png'
author: emil
categories: tech
---

Pandas, NumPy, SciPy 등 Python 패키지 또는 Python 자체를 사용해서 데이터 처리를 하다 보면 연습용 데이터가 필요할 때가 있습니다. 데이터 처리는 여러 데이터에 걸쳐 두루 쓸 수 있는 공통의 기술이기 때문에 연습용 데이터를 충분히 확보하고 두루 다뤄 보는 것이 중요합니다. 연습용 데이터를 토이(toy) 데이터라고도 하는데 이와 같은 토이 데이터셋을 제공해 주는 패키지가 있어 있어 소개합니다. Scikit-Learn 패키지, Statsmodels 패키지, Vega Datasets 패키지에서 제공하는 토이 데이터셋을 차례로 소개할 예정입니다.

* 참고: [Data Science in Python - In-Built Datasets](https://python-data-science.readthedocs.io/en/latest/datasets.html)

---

## 1. Scikit-Learn의 datasets 패키지

머신러닝 교육을 위해 만들어진 Scikit-Learn은 원래 목적을 넘어서 개발 실무에도 널리 쓰이고 있죠? Scikit-Learn은 datasets 패키지를 통해 토이 데이터셋을 제공합니다. 단, 그 개수는 7개로 많지 않습니다만 머신러닝 작업을 Scikit-Learn으로 할 때 부담없이 간단히 사용할 수 있어 편리합니다.

* 참고: [(공식) Scikit-Learn - Toy datasets](https://scikit-learn.org/stable/datasets/index.html#toy-datasets)

데이터를 읽어들여 Pandas의 DataFrame에 담아 편의성 있게 다룰 수 있게 해 보겠습니다.

```python
import pandas as pd
from sklearn.datasets import load_iris
from tabulate import tabulate

iris = load_iris()

df_iris = pd.DataFrame(iris.data, columns=iris.feature_names)
df_iris['target'] = iris.target
print(tabulate(df_iris.sample(10), headers='keys'))

'''
       sepal length (cm)    sepal width (cm)    petal length (cm)    petal width (cm)    target
---  -------------------  ------------------  -------------------  ------------------  --------
 85                  6                   3.4                  4.5                 1.6         1
 52                  6.9                 3.1                  4.9                 1.5         1
  8                  4.4                 2.9                  1.4                 0.2         0
 68                  6.2                 2.2                  4.5                 1.5         1
 67                  5.8                 2.7                  4.1                 1           1
129                  7.2                 3                    5.8                 1.6         2
 76                  6.8                 2.8                  4.8                 1.4         1
145                  6.7                 3                    5.2                 2.3         2
105                  7.6                 3                    6.6                 2.1         2
  7                  5                   3.4                  1.5                 0.2         0
'''
```

Scikit-Learn의 datasets 패키지로부터 iris 데이터셋을 불러 왔습니다. Scikit-Learn 토이 데이터셋은 DataFrame 형태로 제공되지 않기 때문에 얻어진 데이터를 DataFrame으로 전환하는 작업을 따로 해 줘야 합니다. `pd.DataFrame()`으로 `iris.data`를 DataFrame으로 전환했습니다. 이때 `columns`로 `iris.feature_names`를 설정해 DataFrame의 헤더명을 부여합니다. `iris.target`은 머신러닝 지도학습(supervised learning) 시 필요한 타겟(target) 또는 레이블(label)인데 이를 위한 컬럼을 별도로 생성해 줍니다.(`df_iris['target']`)

아울러 DataFrame의 내용을 보기 좋게 표시하기 위해 tabulate 패키지를 사용했습니다.

이 iris 데이터셋이 어떤 내용이고 각 컬럼이 무엇이며 타겟이 무엇을 나타내는지 등, 데이터셋에 대한 설명은 `iris.DESCR`에 기술되어 있습니다.

```python
print(iris.DESCR)

'''
.. _iris_dataset:

Iris plants dataset
--------------------

**Data Set Characteristics:**

    :Number of Instances: 150 (50 in each of three classes)
    :Number of Attributes: 4 numeric, predictive attributes and the class

...
...
...

The famous Iris database, first used by Sir R.A. Fisher. The dataset is taken
from Fisher's paper. Note that it's the same as in R, but not as in the UCI
Machine Learning Repository, which has two wrong data points.

...
...
...
'''
```

Scikit-Learn 토이 데이터셋은 iris 데이터셋을 포함해 총 7개인데 `import load_iris` 부분을 바꿔 가면서 다른 데이터셋을 불러 올 수 있습니다. 예를 들어 boston 데이터셋을 불러 오기 위해서는 `import load_boston`을 실행한 뒤 함수 호출을 `load_boston()`와 같이 하면 됩니다.

* 참고: 사용 가능한 토이 데이터셋 7개는 `load_boston`, `load_iris`, `load_diabetes`, `load_digits`, `load_linnerud`, `load_wine`, `load_breast_cancer`입니다.

Scikit-Learn은 토이 데이터셋 외에도 실제(real word) 데이터셋 또한 제공합니다. 실제 데이터셋 또한 연습용으로 쓸 수 있지만 데이터의 용량이 크기 때문에(수십 MB 또는 그 이상) 실제적이고 양이 많은 데이터가 필요한 경우에 한해 사용하는 게 좋습니다.

* 참고: [참고: (공식) Scikit-Learn - Real word datasets](https://scikit-learn.org/stable/datasets/index.html#real-world-datasets)

---

## 2. Statsmodels의 datasets 패키지

데이터 과학 분야의 주요 도구로서 Python과 쌍벽을 이루던 R이 있습니다. R은 Python보다 역사가 길어서 데이터셋 또한 매우 풍부합니다. Statsmodels는 datasets 패키지의 `get_rdataset()` 함수를 통해 R이 제공하는 데이터를 Python에서도 이용할 수 있게 해 줍니다.

* 참고: [(공식) Statsmodels - The Datasets Package](https://www.statsmodels.org/devel/datasets/index.html)

데이터를 불러 와 DataFrame에 담아 줘야 했던 Scikit-Learn과 달리, Statsmodels는 DataFrame 상태로 데이터를 제공합니다.

```python
import statsmodels.api as sm
from tabulate import tabulate

ces11 = sm.datasets.get_rdataset('CES11', 'carData')

print(tabulate(ces11.data.sample(10), headers='keys'))

'''
        id  province      population    weight  gender    abortion    importance    education    urban
----  ----  ----------  ------------  --------  --------  ----------  ------------  -----------  -------
1761  2050  QC               5996930   6153.85  Female    No          not           higher       rural
 535  2297  QC               5996930   6153.85  Female    Yes         notvery       lessHS       urban
 778  2164  QC               5996930   6153.85  Male      No          notvery       college      rural
 149  1830  NS                729545   6399.52  Male      Yes         very          HS           rural
1459   248  SK                734250   7082.8   Male      No          very          HS           rural
1205  3189  ON               9439960   4488.81  Female    No          somewhat      lessHS       urban
 902   527  ON               9439960  13466.4   Male      No          somewhat      bachelors    urban
2009  1948  AB               2515180  17406.1   Female    No          somewhat      college      urban
1198  1186  ON               9439960   8977.61  Male      No          somewhat      college      urban
1821  1488  ON               9439960   8977.61  Male      No          notvery       bachelors    urban
'''
```

`get_rdataset()`에서 얻어진 데이터를 `ces11` 변수에 담고 `ces11.data`를 사용하면 바로 DataFrame에 담겨진 데이터를 쓸 수 있게 됩니다. 그리고 타겟(레이블) 또한 이미 이 DataFrame에 포함되어 있는 상태이지요.(urban 컬럼) 데이터셋의 내용, 컬럼별 정의, 타겟의 의미 등은 `ces11.__doc__`으로 확인할 수 있습니다.

```python
print(ces11.__doc__)

'''
===== ===============
CES11 R Documentation
===== ===============

2011 Canadian National Election Study, With Attitude Toward Abortion
--------------------------------------------------------------------

Description
~~~~~~~~~~~

Data are drawn from the 2011 Canadian National Election Study, including
a question on banning abortion and variables related to the sampling
design.

...
...
...

Details
~~~~~~~

This is an extract from the data set for the 2011 Canadian National
Election Study distributed by the Institute for Social Research, York
University.

...
...
...
'''
```

R의 데이터셋은 종류가 매우 다양하고 양이 무척 많습니다. 당연히 모든 종류를 기억할 수 없습니다. 다음 목록을 보고 필요에 따라 찾아 사용해야 합니다.

* 참고: [Rdatasets - List of Datasets](https://vincentarelbundock.github.io/Rdatasets/articles/data.html)

위 목록을 보고 `get_rdataset()`에 해당하는 인자를 잘 설정해 호출해 주면 데이터를 얻을 수 있습니다. 인자를 설정하는 법은 간단합니다. 목록의 패키지(package)와 아이템(item)을 찾아서 아래처럼 함수를 호출해 보시죠.

* `get_rdataset()` 호출 방법: `get_rdataset(item, package)`

예를 들어 목록 페이지 가장 위에 존재하는 Monthly Excess Return 자료는 패키지가 boot, 아이템이 acme이니 `sm.datasets.get_rdataset('acme', 'boot')`와 같이 호출할 수 있겠죠?

---

## 3. Vega Datasets의 data 패키지

마지막으로 Vega 데이터셋의 data 패키지를 이용해 보겠습니다.

* 참고: [(공식 - 데이터셋) Vega Datasets](https://github.com/vega/vega-datasets/blob/master/SOURCES.md)
* 참고: [(공식 - 패키지) vega_datasets 패키지](https://github.com/jakevdp/vega_datasets)

Vega 데이터셋도 데이터를 DataFrame 형태로 바로 제공하고 있습니다.

```python
from vega_datasets import data
from tabulate import tabulate

airports = data.airports()

print(tabulate(airports.sample(10), headers='keys'))

'''
      iata    name                        city          state    country      latitude    longitude
----  ------  --------------------------  ------------  -------  ---------  ----------  -----------
 301  35A     Union County, Troy Shelton  Union         SC       USA           34.6868     -81.6412
 936  BDX     Broadus                     Broadus       MT       USA           45.4333    -105.417
1739  HNR     Harlan Municipal            Harlan        IA       USA           41.5844     -95.3396
1672  H05     Wilburton Municipal         Wilburton     OK       USA           34.9195     -95.3947
 270  2O3     Angwin-Parrett              Angwin        CA       USA           38.5785    -122.435
1295  DRI     Beauregard Parish           De Ridder     LA       USA           30.8315     -93.3396
 895  AVO     Avon Park Municipal         Avon Park     FL       USA           27.5911     -81.5279
 846  ANY     Anthony Municipal           Anthony       KS       USA           37.1585     -98.0796
2696  PVC     Provincetown Municipal      Provincetown  MA       USA           42.072      -70.2214
2514  ONM     Socorro Municipal           Socorro       NM       USA           34.0225    -106.903
'''
```

Vega 데이터셋은 데이터 불러오는 방법이 조금 특이하지요? 데이터셋 제목(여기서는 airports)을 함수처럼 호출하는 방식입니다. `data.airports()` 호출에 의해 airports 데이터셋이 불려 온 것이지요. 마찬가지로 만약 budgets 데이터셋을 불러오려 한다면 `data.budgets()`와 같이 호출하면 되겠습니다.

데이터셋에 대한 설명은 `data.airports.description`과 같은 구문으로 확인 가능합니다. 데이터셋 불러올 때는 함수 호출(`airports()`) 형태를 썼지만, 설명을 불러 올 때는 호출자에 쓰이는 괄호가 없다는 점에 유의해 주십시오. Vega 데이터셋의 데이터 설명은 앞의 두 패키지에 비해 조금 간략한 면이 있네요.

```python
print(data.airports.description)

'''
This dataset lists US airports, including airport code, city, state, latitude, and longitude. This dataset is a subset of the data compiled and published at http://ourairports.com/data/, and is in the public domain.
'''
```

Vega 데이터셋의 데이터 종류는 다음 링크를 통해서 확인할 수 있고, 패키지 자체적으로 목록을 확인할 수 있는 함수도 제공합니다.(`data.list_datasets()`)

* 참고: [(공식) Vega Datasets - Sources](https://github.com/vega/vega-datasets/blob/master/SOURCES.md)

---

이상으로 쉽고 간편하게 쓸 수 있는 토이 데이터셋 세 가지, Scikit-Learn 데이터셋, Statsmodels R 데이터셋, Vega 데이터셋에 대해 알아 보았습니다. 토이 데이터셋을 통한 연습으로 쌓은 분석 및 처리 기술이 보다 방대한 실제 데이터를 자유자재로 다루는 데 도움이 되기를 기대합니다.
