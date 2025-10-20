# Chapter04 임베딩
## 4.2 단어 임베딩
### 4.2.1 원-핫 인코딩
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

### 4.2.2 희소표현과 분산표현
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
### 4.2.3 Word2Vec
- 원-핫 인코딩의 단점을 해결하기위해 나온 구글의 임베딩 모델
- Gensim 라이브러리를 통해 사용함.
#### 종류
- CBOW 모델 : 주변 단어들을 통해 타킷 단어을 예측하는것
- skip-gram 모델: 타깃 단어를 통해 주변단어를 예측하는것
+) window : 주변단어를 앞뒤로 얼마나 참고할지를 정하는 변수

#### 코드 예제
```python
from gensim.models import Word2Vec
from konlpy.tag import Komoran
import time
import os  #경로설정 문제

this_program_dir=os.path.dirname(os.path.abspath(__file__))
os.chdir(this_program_dir)

# 네이버 영화 리뷰 데이터 읽어옴
#보통 함수형태로 구현함.
def read_review_data(filename):
    with open(filename,'r',encoding='utf-8') as f:
        data=[line.split('\t') for line in f.read().splitlines()]
        data=data[1:]#헤더제거
        #print(data)
    return data

# 학습시간 측정 시작
start=time.time()

#리뷰파일 읽어보기
print('1) 말뭉치 데이터 읽기시작')
review_data=read_review_data('./ratings.txt')
print(len(review_data)) #리뷰데이터 전체개수
print('1)말뭉치데이터 읽기 완료:{0}'.format(time.time()-start))

# 문장단위로 명사만 추출해 학습 입력데이터로 만듬
print("2) 형태소에서 명사만 추출시작")
komoran=Komoran()
docs=[komoran.nouns(sentence[1]) for sentence in review_data]
print('2)형태소에서 명사만 추출 완료{0}'.format(time.time()-start))

print('3) word2Vec 모델 학습시작')
model=Word2Vec(sentences=docs,vector_size=200,window=4,min_count=2,sg=1)
print('3) Word2Vec 모델 학습 완료 {0}'.format(time.time()-start))

#모델 저장
print('4) 학습된 모델 저장')
model.save('nvmc.model')
print('4) 학습된 말뭉치 저장완료:{0}'.format(time.time()-start))

#학습된 말뭉치수, 코퍼스내 전체 단어수
print('5) corpus_count:{0}'.format(model.corpus_count))
print('5) corpus_toatl_words:{0}'.format(model.corpus_total_words))
```
- `model=Word2Vec(sentences=docs,vector_size=200,window=4,min_count=2,sg=1)`
    - vector_size : 단어 임베팅 벡터의 차원(크기)
    - window: 주변 단어 윈도우의 크기
    - hs: 0(0이 아닌 음수 샘플링 사용) 1(모델 학습에 softmax사용)
    - min_count: 단어 최소 빈도수 제한 설정(설정된 빈도수 이하의 단어들은 학습하지 않음)
    - sg : 0(CBOW모델) ,1 (skip-gram 모델)

 ``` python
  from gensim.models import Word2Vec
import os
this_program_dir=os.path.dirname(os.path.abspath(__file__))
os.chdir(this_program_dir)

# 모델 로딩
model=Word2Vec.load('./nvmc.model')
print("corpus_total_words:{0}".format(model.corpus_total_words))

# '사랑' 이라는 단어로 생성한 단어 임베딩 벡터
print('사랑',model.wv['사랑'])
print('결혼',model.wv['결혼'])
#단어 유사도 계산
print('사랑=결혼\t',model.wv.similarity(w1='사랑',w2='결혼'))
print('사랑=여자친구\t',model.wv.similarity(w1='사랑',w2='여자친구'))
print('사랑=엄마\t',model.wv.similarity(w1='사랑',w2='엄마'))
print('일요일=월요일\t',model.wv.similarity(w1='일요일',w2='월요일'))
print('안성기=배우\t',model.wv.similarity(w1='안성기',w2='배우'))
print('대기업=삼성\t',model.wv.similarity(w1='대기업',w2='삼성'))
print('일요일!=삼성\t',model.wv.similarity(w1='일요일',w2='삼성'))
print('히어로!=삼성\t',model.wv.similarity(w1='히어로',w2='삼성'))

# 가장 유사한 단어추출
print(model.wv.most_similar('안성기',topn=5))
print(model.wv.most_similar('시리즈',topn=5))

```


  




