# JPA (Java Persistence API)
JPA란 Java의 ORM 기술 표준을 뜻한다. JPA는 인터페이스의 모음이라고 보면되는데, 이를 구현한 하이버네이트, EclipseLink, DataNucleus라는 3가지의 구현체가 있다. 주로 하이버네이트를 가장 많이 사용한다.


## ORM (Object Relational Mapping)
객체 관계 매핑. 객체는 객체대로 설계하고, 데이터베이스는 데이터베이스대로 설계하여 ORM 프레임워크가 중간에서 둘을 매핑해주는 기술을 말한다.

## ORM 기술의 필요성
ORM 기술의 중요성이 대두되기전에는 주로 SQL 중심적인 개발이 이루어졌다. 하지만 근래의 애플리케이션은 대다수 객체지향 언어로 개발하여 관계형 DataBase에 저장하는 방식의 구조를 띄고있다.<br>
개발자는 객체를 데이터베이스에 저장하기위해 SQL을 생성하고, 실행하는 일련의 과정을 반복해왔다. 하지만 객체와 관계형 데이터베이스는 서로 다른 구조를 띄고있기 때문에 패러다임의 불일치가 발생하고 이를 해결하기 위해 객체에 변경이 있을때마다 개발자는 관계형 데이터베이스에 저장하는 코드를 수정하는 과정을 반복하게 된다. <br>이러한 문제점을 해결하기 위해 객체는 객체대로, 관계형 데이터 베이스는 데이터베이스 대로 설계하고 그 둘 사이의 관계를 매핑해주는 ORM 기술의 중요성이 높아진 것이다. ORM의 객체 관계 매핑 덕분에 개발자는 객체에 변경이 생겨도 객체에 대한 코드만 수정하면 되므로 개발 생산성은 높아지고, 설계에 더욱 집중할 수 있게 된다.

## JPA를 사용해야하는 이유
- SQL 중심적인 개발에서 객체 중심 개발이 가능함
- 생산성의 증가
- 유지보수 용이
- 패러다임의 불일치 해결
- 성능 최적화
  - 1차 캐시, 동일성(identity) 보장, 트랜잭션을 지원하는 쓰기 지연(Transactional write-behind), 지연 로딩(Lazy Loading)
- 표준 기술
- 데이터 접근 추상화, 벤더 독립성

## JPA에서 가장 중요한 2가지
1. ORM
2. 영속성 컨텍스트 (Persistence Context)

## 영속성 컨텍스트
영속성 컨텍스트란 논리적인 개념으로, 엔티티를 영구 저장하는 환경이라고 보면 된다. EntityManager를 통해 영속성 컨텍스트에 접근할 수 있다.
- `영속성 컨텍스트의 이점`
  - 1차 캐시 : 객체를 persist()하거나, find면 영속성 컨텍스트의 1차 캐시에 저장된다. 이후 동일한 트랜잭션 내에서 1차 캐시에 있는 객체를 조회하는 경우 DB에 쿼리를 날리지 않고 1차 캐시에 있는 정보를 가져온다.  <br>![jpa_first_cache.png](/img/jpa_first_cache.png)
  - 동일성(identity) 보장 : 동일한 트랜잭션 내에서 같은 객체를 조회해오는 경우, 동일한 객체임을 보장한다.<br>![jpa_identity.png](/img/jpa_identity.png)
  - 트랜잭션을 지원하는 쓰기 지연(Transactional write-behind) : 객체를 여러개 persist() 하는 경우 트랜잭션이 커밋되기전까진 데이터베이스에 SQL을 날리지 않는다. <br> ![jpa_transactional_write_behind.png](/img/jpa_transactional_write_behind.png)
  - 지연 로딩(Lazy Loading)
  - 변경 감지(Dirty Checking) : 객체를 조회하여 수정하는 경우, 따로 update를 하지 않아도 트랜잭션이 커밋되는 순간 객체의 변경된 부분을 감지하여 데이터베이스에 UPDATE 한다. <br> ![jpa_dirty_checking.png](/img/jpa_dirty_checking.png)

### JPA의 엔티티 생명주기
<img src="/img/entity_life_cycle.png">

- New : 비영속. 새로 생성된 상태.
- Managed : 영속. 영속성 컨텍스트에 의해 관리되는 상태
- Detached : 준영속. 영속성 컨텍스트에 관리되다가 분리된 상태
- Removed : 삭제된 상태

### 비영속
객체를 생성한 상태로 영속성 컨텍스트에는 등록되지 않은 상태.
```java
@Entity
public class Member {
  @Id
  private Long id;
  private String name;
  
  public Member() {
  }
  
  public Member(Long id, String name) { // Entity로 등록된 클래스에서 파라미터를 받는 생성자를 만들려면, 기본 생성자도 만들어 줘야함.
    this.id = id;
    this.name = name;
  }
}

public class Main {
  public static void main(String[] args) {
    Member member = new Member(101L, "Member1");
  }
}
```
### 영속
객체를 생성하여, 영속성 컨텍스트에 객체를 저장(등록)한 상태.

```java
@Entity
public class Member {
  @Id
  private Long id;
  private String name;
  
  public Member() {
  }
  
  public Member(Long id, String name) { 
    this.id = id;
    this.name = name;
  }
}

public class Main {
  public static void main(String[] args) {
    EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");
    EntityManager em = emf.createEntityManager();

    EntityTransaction transaction = em.getTransaction();
    transaction.begin();
    
    try {
      Member member = new Member(101L, "Member1");

      em.persist(member);

      transaction.commit();
    }catch (Exception e) {
      transaction.rollback();
    }finally {
      em.close();
    }
  }
}
```
### 준영속
영속성 컨텍스트에 저장했던 객체를 영속성 컨텍스트에서 분리한 상태
```java

@Entity
public class Member {
  @Id
  private Long id;
  private String name;
  
  public Member() {
  }
  
  public Member(Long id, String name) { 
    this.id = id;
    this.name = name;
  }
}

public class Main {
  public static void main(String[] args) {
    EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");
    EntityManager em = emf.createEntityManager();

    EntityTransaction transaction = em.getTransaction();
    transaction.begin();
    
    try {
      Member member = new Member(101L, "Member1");
      em.persist(member);
      
      
      em.detach(member);// detach를 호출하면 영속성 컨텍스트에서 해당 객체를 제거한다. 따라서 객체의 정보가 변경되어도 데이터베이스에 반영되지 않는다.
                        // 영속 컨텍스트에서 제거된 상태를 '준영속 상태' 라고 한다.
      
      em.clear();       // 영속성 컨텍스트를 완전히 초기화 한다.
      
      em.close();       // 영속성 컨텍스트를 종료한다. -> 이때는 영속성 컨텍스트가 아예 종료된 것 이기때문에 jpa에서 객체를 관리하지 않는다. 따라서 객체를 변경하거나 해도 데이터베이스에 반영되지 않는다.
      
      transaction.commit();
    }catch (Exception e) {
      transaction.rollback();
    }finally {
      em.close();
    }
  }
}
```
### 삭제
객체를 삭제한 상태

```java

@Entity
public class Member {
  @Id
  private Long id;
  private String name;
  
  public Member() {
  }
  
  public Member(Long id, String name) { 
    this.id = id;
    this.name = name;
  }
}

public class Main {
  public static void main(String[] args) {
    EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");
    EntityManager em = emf.createEntityManager();

    EntityTransaction transaction = em.getTransaction();
    transaction.begin();
    
    try {
      Member member = em.find(Member.class, 101L); // 객체를 찾는다.
      
      em.remove(member);    // 객체를 삭제.
      
      transaction.commit();
    }catch (Exception e) {
      transaction.rollback();
    }finally {
      em.close();
    }
  }
}
```






### References
[김영한님-자바 ORM 표준 JPA 프로그래밍(기본편)](https://www.inflearn.com/course/ORM-JPA-Basic/dashboard)
