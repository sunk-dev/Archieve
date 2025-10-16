# 임베팅
## 단어 임베딩
### 원-핫 인코딩
```python
from konlpy.tag import Komoran
import numpy as np
komoran=Komoran()
text="오늘 날씨는 구름이 많아요."

#명사만 추출
nouns=komoran.nouns(text)
print(nouns)

# 단어 사전 구축 및 단어별 인덱스 부여
# 매번 만들기 귀찮으므로 보통 만들어두고 단어사전을 열어서 사용

dics={}
for word in nouns:
    if word not in dics.keys():
        dics[word]=len(dics) 
#가장 간단한 인덱스 부여 방법
print(dics)
#원-핫 인코딩
nb_classes=len(dics)
targets=list(dics.values())
one_hot_targets=np.eye(nb_classes)[targets]
print(one_hot_targets)

indata=input()
if indata in list(dics.keys()):
    print("{0} word's code:{1}".format(indata,one_hot_targets[dics[indata]]))
```

#### 코드설명
`one_hot_targets=np.eye(nb_classes)[targets]`
- np.eye(value) : value x value 정항행렬을 만듬
- np.eye(nb_classes)**\[targets]** : 만들어진 정방행렬을: targets 안에 인덱스 리스트에 따라 순서를 지정
ex) np.eye(3)
1,0,0
0,1,0
0,0,1
np.eye(3)\[\[2,1,0]] :인덱싱으로 순서지정
0,0,1
0,1,0
1,0,0

### 희소표현과 분산표현
#### 희소표현
- 희소벡터(sparse vector):단어의 인덱스 요소만 1이고 나모지는 모두 0으로 표현되는 벡터 또는 행렬
- 희소표현이란 : 희소 벡터로 표현되는 방식
- 직관적이지만 단어사전이 크기가 커질수록 복작도가 커지며, 서로 단어간의 연관성이 없다.

#### 분산표현
- 단어의미와 주변 단어간의 관계를 표현하기 위해 고안된 방식
- 한 차원에 다양한 정보를 가지고 있음.
- 단어 임베딩 기법에서 가장 많이 사용
- 의미있는 데이터를 고르게 밀집시켜, 밀집벡터, 밀집 표현(dense representation) 이라는 표현으로 사용됨

- 장점
-   데이터 손실 최소화하며 압축가능
-   주변 단어간의 관계 표현 가능




