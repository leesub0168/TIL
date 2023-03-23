# 추상클래스
자바의 클래스는 일반 클래스와 추상 클래스로 나뉘는데, 추상클래스는 `클래스내에 추상 메소드가 1개 이상 존재`하거나 `클래스명에 abstract 키워드`가 붙은 경우를 의미한다. <br>
추상 클래스의 경우 일반 클래스와 달리 인스턴스화가 불가능하며, 이는 추상 클래스의 목적이 객체의 생성이 아니라`추상 클래스를 상속받아 기능을 재사용하고, 확장`시키는데 있기 때문이다.

```java
abstract class Test {
   abstract void abstract_method();

   public void public_method() {

   }

   private void private_method() {

   }

   void default_method() {

   }

   protected void protected_method() {

   }
}


```

# 인터페이스
구조는 추상 클래스와 유사하며 `추상 메소드를 가질수 있고, 상수(final)인 변수만 가질 수 있으며`, `public static final`과 `public abstract`는 생략가능하다. <br> 
`Java8부터는 인터페이스에서도 구현부가 있는 메소드를 가질 수 있으며` private, default 메소드, static 메소드를 정의하여 사용 할 수 있다. <br>
인터페이스는 클래스와 달리 `다중 구현이 가능`하고, 자식 클래스는 인터페이스의 구현과 클래스의 상속을 동시에 할 수 있다.



```java

public interface inter {
    void test(); 
    
    private void private_method() {
        
    }

   static void static_method() {

   }
   /**
    * 일반적인 클래스에서는 접근제어자를 생략하면 default로 간주하지만, 
    * 인터페이스에서는 추상 메소드를 기본으로 하기 때문에 접근제어자를 생략하면 추상메소드로 간주한다.
      인터페이스에서 default의 구현부가 있는 메소드를 정의하고 싶다면 default 키워드를 명시적으로 붙여주어야한다.
    * */
    default void default_method() {
    }
}

```

# 추상 클래스 vs 인터페이스
 ## 공통점
 1. 추상 메소드를 1개 이상 가지고 있어야한다.
 2. 인스턴스화 할 수 없다. (= new 로 생성할 수 없다.)
 3. 인터페이스 혹은 추상클래스를 상속받아 구현한 클래스의 구현체를 이용해야 한다.
 4. 인터페이스와 추상클래스를 상속받은 클래스는 추상 메소드를 구현해야하는 의무를 가진다.
 ## 차이점
 인터페이스에 메소드 구현이 지원되면서 둘의 경계가 모호해진 느낌이 있긴 하지만 클래스의 상속은 부모 클래스에 종속된다는 느낌인 반면, <br>
 인터페이스의 구현은 종속이 아닌 인터페이스에 속하지만 각자의 기능을 구현한다는 점에서 차이가 존재한다고 생각한다. 
1. 키워드
   1. 추상 클래스 : abstract
   2. 인터페이스  : interface
2. 사용 가능 변수
   1. 추상클래스 : 제한없음
   2. 인터페이스 : static final만 사용 가능
3. 사용 가능 메소드
   1. 추상 클래스 : 제한 없음
   2. 인터페이스  : 추상 메소드 , default 메소드, static 메소드, private method
4. 상속 키워드
   1. 추상 클래스 : extends
   2. 인터페이스  : implements
5. 다중 상속 가능 여부
   1. 추상 클래스 : 불가능
   2. 인터페이스  : 가능 (인터페이스 끼리는 다중 상속 가능, 클래스에 다중 구현 가능)




### References
[인터페이스(Interface) 문법 & 활용 - 완벽 가이드](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4Interface%EC%9D%98-%EC%A0%95%EC%84%9D-%ED%83%84%ED%83%84%ED%95%98%EA%B2%8C-%EA%B0%9C%EB%85%90-%EC%A0%95%EB%A6%AC) <br>
[추상 클래스(Abstract) 용도 완벽 이해하기](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%B6%94%EC%83%81-%ED%81%B4%EB%9E%98%EC%8A%A4Abstract-%EC%9A%A9%EB%8F%84-%EC%99%84%EB%B2%BD-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0) <br>
[인터페이스 vs 추상클래스 차이점 - 완벽 이해하기](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4-vs-%EC%B6%94%EC%83%81%ED%81%B4%EB%9E%98%EC%8A%A4-%EC%B0%A8%EC%9D%B4%EC%A0%90-%EC%99%84%EB%B2%BD-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0) <br>
[인터페이스 vs 추상클래스 차이점 - 완벽 이해하기](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4-vs-%EC%B6%94%EC%83%81%ED%81%B4%EB%9E%98%EC%8A%A4-%EC%B0%A8%EC%9D%B4%EC%A0%90-%EC%99%84%EB%B2%BD-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0) <br>
[https://wildeveloperetrain.tistory.com/112](https://wildeveloperetrain.tistory.com/112) <br>
