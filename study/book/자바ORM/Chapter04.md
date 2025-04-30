# 04 엔티티 매핑

## 4.1 @Entity
- 테이블과 매핑할 **클래스**에 필수로 붙여야하는 어노테이션
- 속성
  - name: 
    - JPA에서 사용할 엔티티 이름을 지정
    - 기본값: 클래스 이름 
    -  다른 패키지에 같은이름이 있다면 이름을 지정해서 충돌을 막아야함.
- 주의 사항
  - 기본생성자는 필수(파라미터가 없는 public, protected)
  - final 클래스, enum,interface,inner 클래스에 사용 불가
  - 저장할 필드에 final 사용 불가
  - 생성사가 하나 이상일시 기본 생성자를 직접 만들어야함.
## 4.2 @Table
- 엔티티와 매핑할 **테이블 지정**
- 생략시 매핑한 엔티티 이름을 테이블 이름으로 사용
- 속성
  - name
    - 매핑할 테이블 이름
  - catalog 
    - catalog 기능이 있는 데이터 베이스에서 catalog 매핑
  - schema
    - schema 기느이 있는 데이터 베이스에서 매핑
  - uniqueConstraints(DDL)
    - DDL 생성시 유니크 제약조건 만듬
    - 2개이상의 복합 제약 조건도 가능함
    - 스키마 자동 생성 기능을 시용해 DDL 만들때만 사용
## 4.3 다양한 매핑 사용
- 회원관리 프로그램 요구사항 추가
  1. 회원을 일반과 관리자로 구붕
  2. 회원가입일과 수정일 추가
  3. 회원을 설명할 수 있는 필드가 있어야하며, 이 필드의 길이 제한이 없음.
- 회원 엔티티에 기능 추가
```java
package jpabook.start;

import javax.persistence.*;  //**
import java.util.Date;

/**
 * User: HolyEyE
 * Date: 13. 5. 24. Time: 오후 7:43
 */
@Entity
@Table(name="MEMBER", uniqueConstraints = {@UniqueConstraint( //추가 //**
        name = "NAME_AGE_UNIQUE",
        columnNames = {"NAME", "AGE"} )})
public class Member {

    @Id
    @Column(name = "ID")
    private String id;

    @Column(name = "NAME", nullable = false, length = 10) //추가 //**
//    @Column(name = "NAME") //추가 //**
    private String username;

    private Integer age;

    //=== 추가
    @Enumerated(EnumType.STRING)
    private RoleType roleType;

    @Temporal(TemporalType.TIMESTAMP)
    private Date createdDate;

    @Temporal(TemporalType.TIMESTAMP)
    private Date lastModifiedDate;

    @Lob
    private String description;

    @Transient
    private String temp;


    //Getter, Setter

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public RoleType getRoleType() {
        return roleType;
    }

    public void setRoleType(RoleType roleType) {
        this.roleType = roleType;
    }

    public Date getCreatedDate() {
        return createdDate;
    }

    public void setCreatedDate(Date createdDate) {
        this.createdDate = createdDate;
    }

    public Date getLastModifiedDate() {
        return lastModifiedDate;
    }

    public void setLastModifiedDate(Date lastModifiedDate) {
        this.lastModifiedDate = lastModifiedDate;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public String getTemp() {
        return temp;
    }

    public void setTemp(String temp) {
        this.temp = temp;
    }
}

```
- 코드 분석
1. roleType: 자바의 enum을 필드로 사용하려면 `@Enumerated` 어노테이션으로 매핑하여 사용
2. createdDate,lastModifiedDate: 자바의 날짜 타입은 `@Temporal` 을 사용해서 매핑
3. description: 회원을 설명하는 필드에는 제한이 없어야하므로 varchar 타입 대신 `@Lob`을 사용해 CLOB,BLOB 타입을 매핑함.

## 4.4 데이터 스키마 자동 생성
`<property name="hibernate.hbm2ddl.auto" value="create" />`
- persisitence.xml 에 이 속성 추가시 **애플리케이션 실행 시점에 데이터베이스 테이블을 자동으로 생성**
- `hibernate.hbm2ddl.auto` 속성
  - create: 기존테이블 삭제후 새로 생성
  - create-drop: create 속성에 추가로 애플리케이션을 종료할때 생성한 DDL 제거
  - update: 데이터 베이스 테이블과 엔티티 매핑정보를 비교해 변경사항만 수정
  - validate: 데이터베이스 테이블과 엔티티 매핑정보가 차이가 있으면 경고하고 애플리케이션 작동안함. 이 설정을 DDL을 수정하지 않음
  - none: 자동 생성기능 사용 안함.
- ⚠️ 주의사항
  - crate, create-drop, update 와 같이 DDL을 수정하는 옵션 **운영서버에서 사용하면 안됨!!**
  - 개발 환경에 따른 추천 전략
    - 개발 초기 :create, update
    - 초기화된 상태로 자동화된 테스트 진행하는 개발자와 CI 서버: create,create-drop
    - 테스트 서버: update ,validate
    - 스테이징 운영 서버 validate,none
  - 이름 매핑 전략 변경하기
    - <property name="hibernate.ejb.naming_strategy" value="org.hibernate.cfg.ImprovedNamingStrategy" />
    위 속성을 이요하면 테이블 명이나 컬럼명이 생략되도 자바의 카멜표기법을 테이블의 언더스코어 표기법으로 매핑함.
## 4.5 DDL 생성 기능
- 회원 이름은 필수이며 10자를 초과하면 안될때
```java
@Entity
@Table(name="MEMBER", uniqueConstraints = {@UniqueConstraint( //추가 //**
        name = "NAME_AGE_UNIQUE",
        columnNames = {"NAME", "AGE"} )})
public class Member {

    @Id
    @Column(name = "ID")
    private String id;

    @Column(name = "NAME", nullable = false, length = 10) //추가 //**
//    @Column(name = "NAME") //추가 //**
    private String username;
}
```
- @Colum 매핑 정보의 nullable 속성을 false로 지정하면 자동 생성하는  DDL에 not null 생성가능
- length 속성을 사용하면 DDL에 문자 크기 지정가능
- 생성된 DDL
```sql
create table Member(
    ID varchar(255) not null ,
    name varchar(10) not null ,
    primary key (ID)
)
```

- @Table의 uniqueConstraints 을 지정하면 생성하는 DDL에 유니크 제약조건이 추가됨
- ※ 기능들은 DDL 자동 생성시에만 사용되고 JPA로직에는 영향이 없음.(자동 생성을 사용하지 않는 다면 사용할 이유가 없음)

## 4.6 기본 키 매핑
JPA가 제공하는 데이터베이스 기본키 생성 전략
1. 직접 할당
2. 자동생성: 대리키 사용 방식
   - IDENTITY: 기본키 생성을 데이터베이스에 위임
   - SEQUENCE :  데이터 베이스 시퀀스를 사용해 기본키 할당
   - TABLE: 키 생성 테이블 사용하기