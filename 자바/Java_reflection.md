2023/02/18 과제 구현 관계로 TIL로 대체 

# Java Reflection
객체를 통해 클래스를 찾아내는 방식.

### Reflection 간단한 예제
```java
import java.lang.reflect.*;

    // ref. https://www.oracle.com/technical-resources/articles/java/javareflection.html
   public class DumpMethods {
      public static void main(String args[])
      {
         try {
            Class c = Class.forName(args[0]); // @param 클래스명
            Method m[] = c.getDeclaredMethods();
            for (int i = 0; i < m.length; i++)
            System.out.println(m[i].toString());
         }
         catch (Throwable e) {
            System.err.println(e);
         }
      }
   }
```

### Reflection 새 개체 생성
```java
import java.lang.reflect.*;
        
   public class constructor2 {
      public constructor2()
      {
      }
        
      public constructor2(int a, int b)
      {
         System.out.println(
           "a = " + a + " b = " + b);
      }
        
      public static void main(String args[])
      {
         try {
           Class cls = Class.forName("constructor2");
           Class partypes[] = new Class[2];
            partypes[0] = Integer.TYPE;
            partypes[1] = Integer.TYPE;
            Constructor ct 
              = cls.getConstructor(partypes);
            Object arglist[] = new Object[2];
            arglist[0] = new Integer(37);
            arglist[1] = new Integer(47);
            Object retobj = ct.newInstance(arglist);
         }
         catch (Throwable e) {
            System.err.println(e);
         }
      }
   }
```



### 실제 테스트 
```java
package com.test.reflect;

public class DynamicTest {
    private int a;
    private int b;

    public DynamicTest() {
        System.out.println("empty constructor");
    }

    public DynamicTest(int a, int b) {
        this.a = a;
        this.b = b;
        System.out.println("a = " + a + " , b = " + b );
    }
}
```
```java
package com.test.alg;

public class Main {
    public static void main(String[] args) throws Exception {
        
        // 파라미터가 있는 생성자를 이용할 경우
        Class test = Class.forName("com.test.reflect.DynamicTest");
        Class[] partypes = new Class[2];
        partypes[0] = Integer.TYPE;
        partypes[1] = Integer.TYPE;
        Constructor ct = test.getConstructor(partypes);
        Object[] arglist = new Object[2];
        arglist[0] = 37;
        arglist[1] = 57;
        Object obj = ct.newInstance(arglist);
        
        // 파라미터가 없는 생성자를 이용할 경우
        Class test2 = Class.forName("com.test.reflect.DynamicTest");
        Constructor ct2= test2.getConstructor(); // 클래스의 생성자를 얻어온다.
        Object obj2 = ct2.newInstance(); //해당 클래스의 생성자로 인스턴스를 생성. 생성자의 출력문이 출력됨을 확인할 수 있음.
    }
}
```


### References
[Oracle-Java-Reflection](https://www.oracle.com/technical-resources/articles/java/javareflection.html)
