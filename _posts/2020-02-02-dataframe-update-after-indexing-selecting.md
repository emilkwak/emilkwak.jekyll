---
layout: post
cover: 'assets/images/office01.jpg'
navigation: True
title: 데이터프레임(DataFrame) 인덱싱(indexing), 셀렉팅(selecting)한 상태에서 값 수정하기(loc[] 사용)
date: 2020-02-02 23:00:00
tags: tech python pandas dataframe indexing selecting mask loc
subclass: 'post tag-speeches'
logo: 'assets/images/pooh03.png'
author: emil
categories: tech
---

`pandas`를 사용하다 보면 `DataFrame`의 일부 행과 일부 열에 대해서만 부분적으로 수정을 가해야 할 때가 생깁니다. 인덱싱(indexing)과 셀렉팅(selecting)을 통해 수정을 가할 부분을 어떻게든 찾은 다음에, 어떤 방법을 써야 실제적인 수정이 이뤄지는지 알아 보겠습니다.

선 다음과 같은 샘플 데이터셋을 통해 `DataFrame`을 만들어 보겠습니다. Forbes가 발표한 2019년 미국 최우수 대학교 순위 자료입니다. 1위부터 10위까지만 추렸고 4개의 헤더(`RANK`, `NAME`, `STATE`, `SALARY`)는 각각 순위, 대학교명, 소재주, 평균초봉에 해당합니다.
(참고: [[Forbes] America’s Top Colleges 2019](https://www.forbes.com/top-colleges/#53e27aed1987))

```python
import pandas as pd

header = ['RANK', 'NAME', 'STATE', 'SALARY']
data =\
[['1', 'Harvard University', 'Massachusetts', '74800'],
 ['2', 'Stanford University', 'California', '79000'],
 ['3', 'Yale University', 'Connecticut', '70300'],
 ['4', 'Massachusetts Institute of Technology', 'Massachusetts', '86300'],
 ['5', 'Princeton University', 'New Jersey', '75200'],
 ['6', 'University of Pennsylvania', 'Pennsylvania', '72800'],
 ['7', 'Brown University', 'Rhode Island', '68200'],
 ['8', 'California Institute of Technology', 'California', '84100'],
 ['9', 'Duke University', 'North Carolina', '71100'],
 ['10', 'Dartmouth College', 'New Hampshire', '71500']]

df = pd.DataFrame(data, columns=header)
df.SALARY = df.SALARY.astype(int)
```
위 `df` 변수에 완성된 데이터가 들어가 있습니다. 어떻게 생긴 데이터인지 한번 볼까요?
```python
display(df)
'''
	RANK	NAME	STATE	SALARY
0	1	Harvard University	Massachusetts	74800
1	2	Stanford University	California	79000
2	3	Yale University	Connecticut	70300
3	4	Massachusetts Institute of Technology	Massachusetts	86300
4	5	Princeton University	New Jersey	75200
5	6	University of Pennsylvania	Pennsylvania	72800
6	7	Brown University	Rhode Island	68200
7	8	California Institute of Technology	California	84100
8	9	Duke University	North Carolina	71100
9	10	Dartmouth College	New Hampshire	71500
'''
```
`STATE`가 캘리포니아(California)인 것만 찾아 그 `STATE` CALIFORNIA로 변경해 보겠습니다.
```python
df[df.STATE == 'California'].STATE = 'CALIFORNIA'
display(df)
'''
/usr/anaconda3/lib/python3.7/site-packages/pandas/core/generic.py:5208: SettingWithCopyWarning: 
A value is trying to be set on a copy of a slice from a DataFrame.
Try using .loc[row_indexer,col_indexer] = value instead

See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
  self[name] = value
  
	RANK	NAME	STATE	SALARY
0	1	Harvard University	Massachusetts	74800
1	2	Stanford University	California	79000
2	3	Yale University	Connecticut	70300
3	4	Massachusetts Institute of Technology	Massachusetts	86300
4	5	Princeton University	New Jersey	75200
5	6	University of Pennsylvania	Pennsylvania	72800
6	7	Brown University	Rhode Island	68200
7	8	California Institute of Technology	California	84100
8	9	Duke University	North Carolina	71100
9	10	Dartmouth College	New Hampshire	71500
'''
```
`SettingWithCopyWarning`이 발생했고 변경하려 했던 `df`의 부분도 바뀌지 않고 그대로 있습니다. 우리가 했던 변경 작업이 `df` 원본이 아니라 그것을 복사(copy)한 후 변경 처리한 결과라는 것입니다. 당연히 원본인 `df`는 아무런 영향을 받지 않은 것이고요.

그러면 어떻게 해야 할까요? 다음과 같이 `loc[]`와 `mask`를 이용한 방법이 가장 깔끔합니다.

```
mask = df.STATE == 'California'
df.loc[mask, 'STATE'] = 'CALIFORNIA'
display(df)
'''
	RANK	NAME	STATE	SALARY
0	1	Harvard University	Massachusetts	74800
1	2	Stanford University	CALIFORNIA	79000
2	3	Yale University	Connecticut	70300
3	4	Massachusetts Institute of Technology	Massachusetts	86300
4	5	Princeton University	New Jersey	75200
5	6	University of Pennsylvania	Pennsylvania	72800
6	7	Brown University	Rhode Island	68200
7	8	California Institute of Technology	CALIFORNIA	84100
8	9	Duke University	North Carolina	71100
9	10	Dartmouth College	New Hampshire	71500
'''
```
`STATE` 열을 보면 california였던 것이 모두 CALIFORNIA로 바뀌었습니다. 마찬가지 방법으로, 조금 더 복잡한 작업을 해 보시죠. 평균초봉이 80000 이상이면 대학교명을 모두 대문자 처리하는 코드를 작성해 보겠습니다.
```
mask = df.SALARY > 80000
df.loc[mask, 'NAME'] = df[mask].NAME.str.upper()
display(df)
'''
	RANK	NAME	STATE	SALARY
0	1	Harvard University	Massachusetts	74800
1	2	Stanford University	CALIFORNIA	79000
2	3	Yale University	Connecticut	70300
3	4	MASSACHUSETTS INSTITUTE OF TECHNOLOGY	Massachusetts	86300
4	5	Princeton University	New Jersey	75200
5	6	University of Pennsylvania	Pennsylvania	72800
6	7	Brown University	Rhode Island	68200
7	8	CALIFORNIA INSTITUTE OF TECHNOLOGY	CALIFORNIA	84100
8	9	Duke University	North Carolina	71100
9	10	Dartmouth College	New Hampshire	71500
'''
```
평균초봉이 86300인 MIT와 84100인 CALTECH, 두 군데의 학교명이 대문자 처리되었습니다. 세계적인 공과대학 두 곳인데, 역시 대단하군요.

`DataFrame`을 인덱싱, 셀렉팅할 경우 원래의 `DataFrame`이 아닌 그것의 복사본을 생성해 반환하게 됩니다. 따라서 그것에 수정을 가해도 원본이 바뀌지는 않는 것입니다. 한편, `mask`와 `loc[]`을 이용하면 복사본을 생성하지 않고 원본을 유지하고 수정도 원본에 직접 가할 수 있습니다.