# 토크나이징
- NLP, 즉 자연어 처리를 할때 거치는 과정중 하나로 문장은 의미를 가진 가장 최소의 단위 , 즉 토큰으로 나누는 과정
- 문장형태 데이터를 처리하는데 가장 기본적인 과정이다.
  ## 토크나이징 파이썬 라이브러리, KonLpy
  - 한국어 자연어 처리를 위한 오픈소스 소프트웨어
  - 문장을 분석하기에 앞서, 문장을 형태소 단위로 나누고 태깅하는 형태소 분석기가 필요한데, KonlPy는 내부에서 3가지 형태소 분석기를 제공한다.
  ### 형태소 분석기 종류
  - KKma : 서울대학교에서 개발, 분석 품질이 높지만 속도가 느리다.
  - Komoran : 이 책에서 주로 사용할 분석기로 적당한 속도와 정확도를 가짐
  - Okt: 트위터에서 만든 한국어 형태소 분석기로 사용자 사전 관리가 어렵고 용언분석의 일관성이 부족함.
 
  ## Komoran 모듈 함수
  - morphs : 인자로 입력한 문장을 형태소 단위로 토그나이징함.
  - nouns: 인자로 입력한 문장에서 명사인 토큰만 추출
  - pos: 인자로 입력한 문장에서 토큰을 추출한뒤, 토큰과 토큰에맞는 품사태그를 튜플형태로 묶어서 리스트 단위로 반환.

    ### 예제 3-2 Komoran 형태소 분석기 사용
    ```python
    from konlpy.tag import Komoran
    # 코모란 분석기 객체 생성
    komoran=Komoran()
    
    text="아버지가 방에 들어갑니다."
    #형태소 추출
    morphs=komoran.morphs(text)
    print(morphs)
    
    #형태소와 품사태그 추출
    pos=komoran.pos(text)
    print(pos)
    ```
    ## 사용자 사전 구축
    - 단어나 문장이 인식이 안되는 경우를 대비하여 대부분의 형태소 분석기는 사용자 사전을 구축할수 있도록 함.

    ### 사용자 사전 없을때 예시
    ```python
    from konlpy.tag import Komoran
    import os
    
    komoran=Komoran()
    text="우리 챗봇은 엔엘피를 좋아해."
    text = text.replace('\t','').replace('\r','')
    
    pos=komoran.pos(text)
    print(pos)
    ```
    - 결과적으로 엔엘피라는 단어를 인지하지 못해 엔,엘,피 각각 태깅을 진행한다.

    ### 사용자 사전을 추가시
    - user_dic.tsv
    ```text
    #단어	품사
    엔엘피  	NNG
    챗봇		NNG
    ```

    - 사용자 사전을 지정한 코드
      ```python
      from konlpy.tag import Komoran
      import os
      
      this_program_dir=os.path.dirname(os.path.abspath(__file__))
      os.chdir(this_program_dir)
      print(this_program_dir)
      
      
      komoran=Komoran(userdic="./user_dic.tsv")
      text="우리 챗봇은 엔엘피를 좋아해."
      text = text.replace('\t','').replace('\r','')
      
      pos=komoran.pos(text)
      print(pos)
      ```
  - 결과: 엔엘피를 단어로써 인지함.
    <img width="2560" height="1600" alt="Screenshot (1)" src="https://github.com/user-attachments/assets/d5ea6108-3030-4162-a2f3-12188223a1dd" />


  - ⚠️ 에러 해결법
    - 파일 경로도 정상적으로 잡히는데도 엔엘피를 단어로 인지하지 못할때 살펴봐야할것.
    - user_dic.tsv 의 인코딩 살펴보기 `UTF-8` 로 되어있지 않는경우 인지하지 못해서 단어분석이 제대로 되지 않을 가능성이 있으니 파일탐색기에서 아래와 같이 변경해주기
      <img width="1705" height="1178" alt="Screenshot (3)" src="https://github.com/user-attachments/assets/c97376b9-6e7b-410d-95ab-6c2c25a05202" />

      


