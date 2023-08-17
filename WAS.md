# WAS(Web Application Server)
동적인 처리를 위한 미들웨어로, 클라이언트의 Request를 해석하여 요청에 맞는 서블릿을 탐색하고, 요청에 맞는 데이터를 클라이언트에게 response로 전달한다
WAS는 일반적으로 Servlet Container + Servlet 구조를 얘기하지만, Java에서 주로 사용하는 최신 버전 톰캣의 경우 Apache Tomcat으로, WebServer인 Apache까지 포함한 개념이라고 볼수있다.


# WebServer 

 정적 페이지(자원) 처리를 담당
 > 정적 페이지란 ? <br>
 > : 단순 HTML, 이미지 파일, js파일 등을 정적 페이지 또는 정적자원이라고 함

# Web Container(= Servlet Container)
동적인 처리를 담당
> 동적 페이지란 ?
> : jsp, API 
# Servlet
 >클라이언트의 요청에 따라 결과를 동적으로 생성해주기 위한 역할.<br>
  자바에서 Servlet 인터페이스를 제공하기는 하나, 스프링에서 DispatcherServlet을 기본적으로 지원해주기 때문에
  직접 구현하는 경우는 거의 없다.






