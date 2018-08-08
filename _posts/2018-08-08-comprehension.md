
--- 
category : Data Analysis  
title : Python Comprehension  
tags : Data Analysis, Python, Comprehension  
---


## Python Comprehension

R에서 Python으로 넘어온 후 가장 혼동되었던 것들이 fancy indexing, comprehension 등이다.  
특히 comprehension 같은 경우 text 전처리에서 많이 사용되므로 확실하게 알고 갈 필요가 있다.

### Comprehension 이란?  
간단히 말해 한 줄 짜리 반복문이다.  
아래와 같은 중첩 for문을 한줄로 표현할 수 있다.


```python
s1 = ['a', 'b', 'c']
s2 = [1, 2, 3]

for j in s1:
    for k in s2:
        print(j, k, end= ' ')
print('\n')
lst = [(j, k) for j in s1 for k in s2]
print(lst)
```

    a 1 a 2 a 3 b 1 b 2 b 3 c 1 c 2 c 3 
    
    [('a', 1), ('a', 2), ('a', 3), ('b', 1), ('b', 2), ('b', 3), ('c', 1), ('c', 2), ('c', 3)]


한 Sequence가 다른 Sequence (Iterable Object)로부터 (변형되어) 구축될 수 있게하는 기능으로,    
리스트 셋은 집계함수에 전달되는 입력 값으로도 사용될 수 있다.  

Python 2 에서는 List Comprehension (리스트 내포)만을 지원하며,  
Python 3 에서는 Set Comprehension과 Dictionary Comprehension을 추가로 지원하고 있다. 
<br><br>


```python
import random 
random.seed(1)
a1 = [random.randrange(100) for _ in range(10)]
a2 = [random.randrange(100) for _ in range(10)]
a3 = [random.randrange(100) for _ in range(10)]

a = [a1, a2, a3] # 3*10 matrix
print(a) 

print([sum(i) for i in a]) # 2차원 배열의 행 별 합계
print(sum([sum(i) for i in a])) # 2차원 배열의 행/열 합계
print(sum([j for i in a for j in i ])) # 2차원 배열의 행/열 합계 (i : 행, j : 열)
```

    [[17, 72, 97, 8, 32, 15, 63, 97, 57, 60], [83, 48, 26, 12, 62, 3, 49, 55, 77, 97], [98, 0, 89, 57, 34, 92, 29, 75, 13, 40]]
    [518, 512, 527]
    1557
    1557


### List / Set / Dictionary Comprehension  

#### List Comprehension  
입력 Sequence로부터 지정된 표현식에 따라 새로운 리스트 컬렉션을 생성  
** 문법 : [출력표현식 for 요소 in 입력Sequence [if 조건식]] **  


```python
oldlist = [1, 2, 'A', False, 3] 
newlist = [i*i for i in oldlist if type(i)==int]
print(newlist)
```

    [1, 4, 9]


#### Set Comprehension  
Set Comprehension은 입력 Sequence로부터 지정된 표현식에 따라 새로운 Set 컬렉션을 생성  
** 문법 : {출력표현식 for 요소 in 입력Sequence [if 조건식]} **


```python
oldlist = [1, 1, 2, 3, 3, 4]
newlist = {i*i for i in oldlist}
print(newlist)
```

    {16, 1, 4, 9}


#### Dictionary Comprehension  
Dictionary Comprehension은 입력 Sequence로부터 지정된 표현식에 따라 새로운 Dictionary 컬렉션을 생성  
** 문법 : {Key:Value for 요소 in 입력Sequence [if 조건식]} **


```python
id_name = {1: '박진수', 2: '강만진', 3: '홍수정'}
name_id = {val:key for key,val in id_name.items()}
print(name_id)
```

    {'박진수': 1, '강만진': 2, '홍수정': 3}


#### Nested Comprehension Examples   
- 단어에서 모음을 제거하는 List Comprehension  
- 2차원 행렬을 일차원화 시키는 List Comprehension


```python
# 단어에서 모음을 제거하는 LC
word = 'mathematics'
without_vowels = ''.join([c for c in word if c not in ['a', 'e', 'i', 'o', 'u']])
print("without_voewls : ", without_vowels)

# 행렬을 일차원화 시키는 LC
matrix = [
  [1, 2, 3, 4],
  [5, 6, 7, 8],
  [9, 10, 11, 12],
]
flatten = [e for r in matrix for e in r]
print("flatten : ", flatten)
```

    without_voewls :  mthmtcs
    flatten :  [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12]


#### Advanced Examples  

**예 1. 아래 모양의 행렬 만들기**  
[ [ 1, 0, 0 ],  
  [ 0, 1, 0 ],  
  [ 0, 0, 1 ] ]


```python
m = [ [ 1 if item_idx == row_idx else 0 for item_idx in range(0, 3) ] for row_idx in range(0, 3) ]
print(m)
```

    [[1, 0, 0], [0, 1, 0], [0, 0, 1]]


**예 2. 두 개 이상의 원소는 zip으로 접근하기**  
python 3에서 zip, map의 원소를 출력하기 위해서는 list(zip(x)), list(map(x))를 사용한다  


```python
cities = ['Chicago', 'Detroit', 'Atlanta']
airports = ['ORD', 'DTW', 'ATL']

# 결과가 원소가 튜플 시리즈의 리스트
print(list(zip(cities,airports)))

# map을 사용하면 결과가 리스트 시리즈로 반환
print(list(map(list, zip(cities, airports))))

# zip을 이용하여 튜플을 리스트로 변환
print([[c, a] for c, a in zip(cities, airports)])


```

    [('Chicago', 'ORD'), ('Detroit', 'DTW'), ('Atlanta', 'ATL')]
    [['Chicago', 'ORD'], ['Detroit', 'DTW'], ['Atlanta', 'ATL']]
    [['Chicago', 'ORD'], ['Detroit', 'DTW'], ['Atlanta', 'ATL']]


** 예 3. two-level list comprehension 이용 **  
os.woak() 이용(하위 폴더까지 모두 검색)하여 *.ipynb 파일 모두 불러오기


```python
import os
restFiles = [os.path.join(d[0], f) for d in os.walk("./") for f in d[2] if f.endswith(".ipynb")]
for r in restFiles:
    print(r)
```

    ./comprehension.ipynb
    ./.ipynb_checkpoints/comprehension-checkpoint.ipynb


** 예 4. set comprehension 이용 **  
set comprehension으로 중복 이름 제거하고 2글자 이상 이름만 표시 (첫글자 대분자, 나머지 소문자)  


```python
names = [ 'Bob', 'JOHN', 'alice', 'bob', 'ALICE', 'J', 'Bob' ]
name_set = { name[0].upper() + name[1:].lower() for name in names if len(name) > 1 }
print(name_set)
```




    {'Alice', 'Bob', 'John'}



**예 5. dictionary comprehension 이용**  
key에 있는 중복 알파벳은 소문자로 결합하고 value 값 합산하기




```python
mcase = {'a':10, 'b': 34, 'A': 7, 'Z':3}
mcase_frequency = { k.lower() : mcase.get(k.lower(), 0) + mcase.get(k.upper(), 0) for k in mcase.keys() }

print(mcase_frequency)
```

    {'a': 17, 'b': 34, 'z': 3}


#### Etc  
comprehension은 map & filter 함수로 대체될 수 있다.  


```python
a_list = [1, '4' , 9, 'a', 0, 4]

# comprehension
squared_ints = [ e**2 for e in a_list if type(e) == int ]
print(squared_ints)

# map
squared_ints = map(lambda e: e**2, a_list)

# filter
squared_ints = filter(lambda e: type(e) == int, a_list)

squared_ints = map(lambda e: e**2, filter(lambda e: type(e) == int, a_list))
print(list(squared_ints))


```

    [1, 81, 0, 16]
    [1, 81, 0, 16]


### Reference  
- http://pythonstudy.xyz/python/article/22-Python-Comprehension  
- https://mingrammer.com/introduce-comprehension-of-python/  
- http://python-3-patterns-idioms-test.readthedocs.io/en/latest/Comprehensions.html

