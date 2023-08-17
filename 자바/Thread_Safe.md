# Thread Safe
자바는 멀티 스레드 환경을 지원하는 특징을 가지고 있음. 멀티 스레드 환경에서도 기능이 정확하게 동작하는 것을 Thread-safe 하다고 얘기한다.

## Thread Safe를 위한 방법
1. synchronized 키워드 사용
   - synchronized 메소드를 이용하는 방법, synchronized 블럭을 사용하는 방법 2가지가 있음.
```java
class Test {
    public synchronized void synchronized_method() {
        
    }
    
    public void part_synchronized_method() {
        int x = 1;
        synchronized (x) {
            
        }
    }
}
```

2. Immutable(불변)한 클래스 사용
   - 불변이란 말 그대로 변경이 불가한 클래스라는 의미로 Immutable class는 객체 생성시 메모리의 Heap 영역에 생성된다.<br>
     동일한 프로세스내에서는 메모리의 Code, Data, Heap 영역을 공유하게 된다. 즉 스레드끼리는 Immutable class가 생성된 Heap 영역을 공유하게 되므로<br>
     멀티 스레드 환경에서도 동일한 객체를 참조하게 되는 것이므로 thread-safe 하게 동작하는 것이다. <br>
     (ex. String, Integer, Boolean, Float, Long 등)

3. 동시성을 보장하는 클래스 사용
   - ex. java.util.concurrent 패키지 (ConcurrentHashMap, AtomicInteger 등등)