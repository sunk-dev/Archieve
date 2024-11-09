# Chapter 02 JPA 시작
## 2.1 이클립스 설치와 프로젝트 불러오기
- Eclipse IDE for JAVA EE Developers 패키지로 내려받는 걸  권장함.
- 이 패키지로 사용하면 JPA로 개발할 때 편리한 도구들이 지원됨.
- [이클립스 다운 경로](https://www.eclipse.org/downloads/packages/)
![img_3.png](img_3.png)

- 예제 프로젝트 불러오기
- [예제다운사이트](https://github.com/holyeye/jpabook)

**JRE 버전 문제 해결**
1. JRE가 너무 에전 버전이여서 1.8 이상으로 교체하라는 에러가 뜸
   - 우클릭 -> Properties -> JavaBuildPath-> Libraries 에서 Moudluepath 더블 클릭 -> Alternative JRE에서 버전을 올려줌
![img_4.png](img_4.png)
   - JavaComplie 에서 Compiler compliance level을 Buildpath에서 변경한 버전과 동일한 버전으로 설정해줌.
![img_5.png](img_5.png)
## 2.3 H2데이터 베이스 설치
- [설치 경로](https://www.h2database.com/html/download-archive.html)
- 책에 있는 버전은 너무 오래 되서 다운 받을 수 없었고 새로운 버전을 다운 받아야 했는데, 이것도 자바 버전에 영향을 받는다.
- Java 17은 1.4.200과 호환되어 작동 했다.
![img_6.png](img_6.png)


- 버전이 맞지 않는 H2 서버 작동시, 터미널에 아래와 같은 문구가 보인다.
![img_7.png](img_7.png)
- 각자 자바의 컴파일 버전 참고
  - [자바 컴파일 버전 참고 블로그](https://ksabs.tistory.com/185)
- H2 로그인 화면
![img_8.png](img_8.png)

- 예제 테이블 생성하기
```sql
create table MEMBER(
    id varchar(255) not null , -- 아이디(기본키)
    name varchar(255),         -- 이름
    age integer not null ,     -- 나이
    primary key (id)
)
```

