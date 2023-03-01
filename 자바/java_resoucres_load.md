# 자바 프로젝트내에서 파일 찾기

## 일반적인 클래스에서 파일찾기
```java
public class publicClassTest {
    public void load_file(String fileName) throws URISyntaxException {
        URL url = this.getClass().getResource(fileName);
        Path path = Paths.get(url.toURL());
        byte[] fileContents = Files.readAllBytes(path);

        String stringContents = new String(fileContents);
    }
}
```

## Singleton 패턴의 객체에서 파일 찾기
```java
public class SingleTon {
    private static final SingleTon singleton = new SingleTon();
    
    private SingleTon() {
        // 객체가 생성될때 파일을 로드한다고 가정.
    };
    
    public static SingleTon getSingleton() {
        return singleton;
    }
    private void init() {
        String fileName = "test.txt";
        ClassLoader classLoader = ClassLoader.getSystemClassLoader();
        StringBuffer sb = new StringBuffer();
        try(BufferedReader br = new BufferedReader(new InputStreamReader(classLoader.getResourceAsStream(fileName)))) {
            String s;
            while (true) {
                s = br.readLine();
                if(s != null) sb.append(s);
                else break;
            }
        }
        // sb에 파일 내용을 읽어서 append
    }
}
```