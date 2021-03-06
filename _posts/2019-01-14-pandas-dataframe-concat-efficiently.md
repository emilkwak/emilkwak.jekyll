---
layout: post
cover: 'assets/images/office01.jpg'
navigation: True
title: 메모리 덜 쓰고 Pandas DataFrame 병합을 빠르게 하는 방법(여러 개의 용량 큰 파일)
date: 2019-01-14 22:00:00
tags: tech python pandas dataframe concat
subclass: 'post tag-speeches'
logo: 'assets/images/pooh03.png'
author: emil
categories: tech
---

여러 개의 Excel 파일(또는 csv, pickle 등)로부터 같은 형태의 DataFrame을 차례로 읽어 들여 하나의 큰 DataFrame으로 합치려 한다고 합시다. 각 Excel 파일의 DataFrame의 컬럼 목록은 모두 같다고 하죠. 실제 사례를 상상해 보면, 날짜별, 시간별 로그 데이터가 파일 형태로 저장되어 있는 경우를 생각해 볼 수 있겠네요.

가장 먼저 떠오르는 방법은 아래처럼 빈 DataFrame을 선언한 후 For Loop으로 각 파일을 읽어 붙이는 형태가 되겠습니다.

```
# 권장하지 않는 사례
import pandas as pd, os

base_dir = 'some/dir/path'

df_accum = pd.DataFrame() # 빈 DataFrame을 만듦.
for fname in os.listdir(base_dir):
    df_temp = pd.read_excel(os.path.join(base_dir, fname))
    df_accum = df_accum.append(df_temp) # 매 Loop에서 취해진 DataFrame을 앞서 정의한 빈 DataFrame에 붙임.
```

그러나 이 방법은 다음의 두 가지 이유에서 좋지 않습니다.

1. 메모리를 많이 씁니다. `df_accum`은 점점 몸집이 커져 갑니다. Loop마다 커지는 몸집에 `df_temp`를 붙이는 작업은 메모리 사용 차원에서 비효율적입니다.
2. 속도가 느립니다. 크기가 큰 DataFrame을 `append()`하는 것보다 작은 것을 하는 게 더 빠를 것입니다. 그런 차원에서 몸집이 커져 가는 `df_accum`은 느린 속도의 주범입니다.

실제로 pickle 기준으로 총 크기가 4.5GB인 데이터를 위와 같이 처리해 보면 속도가 매우 느린데다가 자칫하면 메모리 부족 오류(MemoryError)를 보게 되기도 합니다.(Windows 64-bit에 8GB RAM을 사용한다 하더라도 말이죠.)

메모리 효율성 차원에서는 사실 뾰족한 답이 없습니다. 다만, '조금이라도 메모리를 덜 낭비'하면서 속도는 훨씬 빨리 할 수 있는 방법이 있습니다. 아래처럼 For Loop에서 등장하는 DataFrame을 list에 담아 두고 마지막에 list 통째로 `pd.concat()`에 넣어 병합하는 방법입니다.

```
# 권장하는 사례
import pandas as pd, os

base_dir = 'some/dir/path'

list_of_df = [] # 빈 list를 만듦.
for fname in os.listdir(base_dir):
    df_temp = pd.read_excel(os.path.join(base_dir, fname))
    list_of_df.append(df_temp) # 매 Loop에서 취해진 DataFrame을 위의 list에 적재함.
df_accum = pd.concat(list_of_df) # list 속 DataFrame들을 일괄 병합함.
```

말씀드린 것처럼 메모리 효율성을 급격하게 높일 수는 없겠지만, 속도 향상은 확실히 느낄 수 있을 것입니다.
