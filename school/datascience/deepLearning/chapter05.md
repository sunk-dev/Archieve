# Chapter05 텍스트 유사도
## 5.1 텍스트 유사도 개요
## 5.2 n-gram 유사도
- 주어진 문장에서 n개의 연속적인 단어 나열을 의미함.
- n=1 : 유니그램
- n=2: 바이그램
- n=3 : 트라이그램
## 5.2 n-gram 유사도
- 주어진 문장에서 n개의 연속적인 단어 나열을 의미함.
- n=1 : 유니그램
- n=2: 바이그램
- n=3 : 트라이그램
### 유사도 공식
![CodeCogsEqn](https://github.com/user-attachments/assets/08ead7ad-b92a-43e2-a75c-f3a7b57f6817)
- tf(term frequency): A,B에 등장한 동일한 토큰의 출현빈도
- tokens: 해당 문장의 전체 토큰 수

###  n-gram 유사도 코드 
```python
from konlpy.tag import Komoran

komoran=Komoran()

def word_ngram(bow,num_gram): #문자단위
    text=tuple(bow)
    ngrams=[text[x:x+num_gram] for x in range(0,len(text))]
    return ngrams
def phoneme_ngram(bow,num_gram): #음절단위
    sentence="".join(bow)
    text=tuple(sentence)
    ngrams=[text[x:x+num_gram] for x in range(0,len(text))]
    return ngrams
test="ABCDefghijklmn"
result=word_ngram(test,2)
print(result)

#test2="오늘은 기분이 우울해서 수업을 자체공강했다."
test2=["ab","bc","cd","df","fg"]
result2=phoneme_ngram(test2,2)
print(result2)

result3=word_ngram(test2,2)
print(result3)

# 간단한,설명을 위한 유사도 계산
def similarity(doc1,doc2):
    cnt=0
    for token in doc1:
        if token in doc2:
            cnt+=1
    return cnt/len(doc1)

sentence1="6월에 뉴턴은 선생님의 제안으로 트리니티에 입학하였다."
sentence2="6월에 뉴턴은 선생님의 제안으로 대학교에 입학하였다."
sentence3="나는 맛있는 밥을 뉴턴 선생님과 먹었습니다."

bow1=komoran.nouns(sentence1)
bow2=komoran.nouns(sentence2)
bow3=komoran.nouns(sentence3)

doc1=word_ngram(bow1,2)
doc2=word_ngram(bow2,2)
doc3=word_ngram(bow3,2)

print(doc1)
print(doc2)
print(doc3)

s1=similarity(doc1,doc2)
s2=similarity(doc1,doc3)
s3=similarity(doc2,doc3)

print(s1,s2,s3)

tri1=word_ngram(bow1,3)
tri2=word_ngram(bow2,3)
tri3=word_ngram(bow3,3)

print(tri1)
print(tri2)
print(tri3)

s4=similarity(tri1,tri2)
s5=similarity(tri1,tri3)
s6=similarity(tri2,tri3)
print(s4,s5,s6)
```

## 5.3 코사인 유사도
- 실무에서 많이 사용한 유사도
- -1~1 사이의 값을 가지면, 두벡터의 방향이 동일하면 1, 반대면 -1 , 서로 직각을 이루면 0의 값을 가짐/
<img src="https://latex.codecogs.com/svg.image?&space;similarity=cos\theta=\frac{AB}{\left\|A\right\|\left\|B\right\|}=\frac{\sum_{i=1}^{n}A_{i}*B_{i}}{\sqrt{\sum_{i=1}^{n}{A_{i}}^{2}}*\sqrt{\sum_{i=1}^{n}{B_{i}}^{2}}}" title=" similarity=cos\theta=\frac{AB}{\left\|A\right\|\left\|B\right\|}=\frac{\sum_{i=1}^{n}A_{i}*B_{i}}{\sqrt{\sum_{i=1}^{n}{A_{i}}^{2}}*\sqrt{\sum_{i=1}^{n}{B_{i}}^{2}}}" />

### 코사인 유사도 계산
```python

from konlpy.tag import Komoran
import numpy as np
from numpy import dot
from numpy.linalg import norm


def cos_sim(vec1,vec2):
    #코사인 유사도 계산
    return dot(vec1,vec2)/(norm(vec1)*norm(vec2))

#TDM
def make_term_doc_mat(sentence_bow,word_dics):
    freq_mat={}
    
    for word in word_dics:
        freq_mat[word]=0
    for word in word_dics:
        if word in sentence_bow:
                freq_mat[word]+=1
    return freq_mat

def make_vector(tdm):
    vec=[]
    for key in tdm:
        vec.append(tdm[key])
    return vec


s1='abcdef'
s2='abc'
dics='abcdef'
f1=make_term_doc_mat(s1,dics)
f2=make_term_doc_mat(s2,dics)
v1=np.array(make_vector(f1))
v2=np.array(make_vector(f2))

print(cos_sim(v1,v2))

sentence1="6월에 뉴턴은 선생님의 제안으로 트리니티에 입학하였다."
sentence2="6월에 뉴턴은 선생님의 제안으로 대학교에 입학했다."
sentence3="나는 맛있는 밥을 뉴턴 선생님과 함께 먹었다."

komoran=Komoran()
bow1=komoran.nouns(sentence1)
bow2=komoran.nouns(sentence2)
bow3=komoran.nouns(sentence3)

bow= bow1+bow2+bow3

word_dics=[]
for token in bow:
    if token not in word_dics:
        word_dics.append(token)

f1=make_term_doc_mat(sentence1,word_dics)

f2=make_term_doc_mat(sentence2,word_dics)

f3=make_term_doc_mat(sentence3,word_dics)
print(f1,f2,f3)
doc1=np.array(make_vector(f1))
doc2=np.array(make_vector(f2))
doc3=np.array(make_vector(f3))

r1=cos_sim(doc1,doc2)
r2=cos_sim(doc3,doc1)
r3=cos_sim(doc2,doc3)
print(r1)
print(r2)
print(r3)
```

## 코드 실행 오류
### 오류 항목
<img width="877" height="467" alt="image" src="https://github.com/user-attachments/assets/de7ffb01-27d7-42e7-ab40-4a1051b2e4d2" />

### 해결
`pip install protobuf==3.20.*` 
- protobuf 버전이 맞지 않아 발생한 문제로 터미널에서 제시한 해결책대로 3.20,* 또는 그보다 낮은 버전으로 pip install을 통해 설정한다.
- 
####  참고 자료
https://woodadada16.tistory.com/79

https://stackoverflow.com/questions/72899948/how-to-downgrade-protobuf

https://protobuf.dev/news/2022-05-06/#python-updates




