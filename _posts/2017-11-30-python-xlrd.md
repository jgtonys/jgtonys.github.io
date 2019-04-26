---
layout: post
title:  "Python Excel 다루기"
category: Python
tags: [xlrd]
---

업무를 하게되면 가장 많이 다루게 되는 Excel.<br>
수 없이 많은 셀작업들을 하다 보면 프로그래밍으로 쉽게 쉽게 정보를 얻고, 수정하고 싶어진다.<br>
Python module xlrd를 사용하여 Excel을 다루는 방법을 소개한다.<br>

<!-- more -->

* * *

**xlrd 설치법**

python 3.5 기준으로
```python
$ pip install xlrd
```
설치가 완료된다.<br>
* * *

**xlrd 사용하기**

- Excel 파일 불러오기

```python
import xlrd

f = xlrd.open_workbook("파일.xlsx",on_demand=False,encoding_override='cp949')
sheet = f.sheet_by_name("시트이름")
```

- 특정 cell 또는 row,col 얻기

```python
# 1,1의 내용을 가져온다.
target = sheet.cell(1,1)

# 전체 col의 개수를 리턴한다.
target = sheet.ncols

# 전체 row의 개수를 리턴한다.
target = sheet.nrows

# 특정 row의 값들을 리턴한다.
target = sheet.row_values(i)

# 특정 col의 값들을 리턴한다.
target = sheet.col_values(i)
```

- 만약 읽어온 Excel 파일 안의 내용이 날짜형식이라면??

엑셀은 자동으로 날짜 형식을 구현해 주기 때문에, 값을 python으로 읽어오게 되면 이상한 숫자가 불려와진다.

```python
f = xlrd.open_workbook("파일.xlsx",on_demand=False,encoding_override='cp949')
sheet = f.sheet_by_name("시트이름")
d = sheet.cell(1,1)
date = xlrd.xldate.xldate_as_datetime(d,f.datemode)
```

이로써 변환이 가능하다.


* * *

이제 Excel 파일을 쉽고 빠르게 읽어오자!

#Excel 쓰기 Python 모듈 포스팅 예정#

