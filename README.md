# Spring_MVC

## 6/8 
![image](https://user-images.githubusercontent.com/92084680/172860999-d6d40cc3-61c8-44fa-b431-903571fdd6b8.png)
1. HTTP 요청시 WAS는 Request, Response 객체를 새로 만들어서 서블릿 객체를 호출 한다.
2. 개발자는 Request 객체에서 HTTP 요청정보를 꺼내서 사용하고 Response객체에 HTTP응답정보를 담는다.
3. WAS는 Response객체에 담겨있는 내용으로 HTTP 응답 정보를 생성한다. 
****
## 6/9
#### 클라이언트에서 서버로 메시지를 보내는 방법
![image](https://user-images.githubusercontent.com/92084680/172861283-07252cf3-8d22-46ed-8227-52a08e89bfd0.png)

## 6/10
#### 너무 많은 짐을 짊어진 JSP (유지보수의 지옥)

## 6/11
#### MVC 패턴
![image](https://user-images.githubusercontent.com/92084680/173189182-fe6f29fc-9829-4a17-afc5-f00be941ba3b.png)
1. 클라이언트 요청이오면 컨트롤러에서 파라미터 정보들을 확인 후 오류가 없을 시
2. 비즈니스 로직을(service단) 실행 (회원 조회, 주문,,,등등) 
3. 결과를 모델에 담아서 뷰에 건네준다. 
4. 뷰는 모델에 담긴 정보들을 꺼내서 화면에 그려준다.

> 한계
공통 처리가 어렵다는 문제가 있다.
이 문제를 해결하려면 컨트롤러 호출 전에 먼저 공통 기능을 처리해야 한다. 소위 수문장 역할을 하는 기능이
필요하다. 프론트 컨트롤러(Front Controller) 패턴을 도입하면 이런 문제를 깔끔하게 해결할 수 있다.
(입구를 하나로!)

## 6/12
![image](https://user-images.githubusercontent.com/92084680/173372473-cc42d18e-3021-46cd-b290-2cd90b32fbb2.png)

>v3컨트롤러는 서블릿 기술을 전혀 사용하지 않는다. 따라서 구현이 매우 단순해지고, 테스트 코드 작성시
>테스트 하기 쉽다.  HttpServletRequest가 제공하는 파라미터는 프론트 컨트롤러가 paramMap에 담아서 호출해주면 된다.
>응답 결과로 뷰 이름과 뷰에 전달할 Model 데이터를 포함하는 ModelView 객체를 반환하면 된다.
```
private Map<String, String> createParamMap(HttpServletRequest request) {
 Map<String, String> paramMap = new HashMap<>();
 request.getParameterNames().asIterator()
 .forEachRemaining(paramName -> paramMap.put(paramName,
request.getParameter(paramName)));
 return paramMap;
 }
```
>HttpServletRequest에서 파라미터 정보를 꺼내서 Map으로 변환한다. 그리고 해당 Map( paramMap )을
컨트롤러에 전달하면서 호출한다

## 6/13
![image](https://user-images.githubusercontent.com/92084680/173374195-9d5dadf6-7a8f-48ae-9680-579dc0145a34.png)
> 기본적인 구조는 v3와 동일, 대신에 컨트롤러가 ``` ModelView``` 를 반환하지 않고 ```ViewName```만 반환.
```
public interface ControllerV4 {
 /**
 * @param paramMap
 * @param model
 * @return viewName
 */
 String process(Map<String, String> paramMap, Map<String, Object> model);
}
```
> 인터페이스에 ModelView 가 없음. 리턴값으로 뷰의 이름만 반환해 준다. 

## 6/14
![image](https://user-images.githubusercontent.com/92084680/173533846-9b84878e-ca96-4a6b-8f92-464c3f6090c3.png)

1. 클라이언트 요청이 오면 핸들러 조회 후 맵핑 정보를 살핀 후 핸들러를 처리할 수 있는 핸들로 어댑터를 조회하여 찾아온다.
2. 어댑터는 handler(컨트롤러)를 호출하고 다음 모델 뷰를 반환 후 뷰네임을 갖고와서 뷰리졸브를 호출한 다음 뷰렌더로 모델을 넘겨준다. 



> '어댑터 패턴' 을 이용하여 기존에 한가지 방식으로만 사용되었던 프론트컨트롤러 인터페이스를 다양한 방식으로 사용할 수 있게 하였다. 
>  이전과 다르게 직접 컨트롤러를 맵핑하지 않고 핸들러와 어댑터를 사용하여 URL에 맵핑하여 사용할 수 있게 하였다. 
```
 private Object getHandler(HttpServletRequest request) {
      String requestURI = request.getRequestURI();
      return handlerMappingMap.get(requestURI);
       
 }
 ```
 > 핸들러 매핑 정보인 handlerMappingMap 에서 URL에 매핑된 핸들러(컨트롤러) 객체를 찾아서 반환한다.
 #### 어댑터 호출
``` ModelView mv = adapter.handle(request, response, handler);```
 > 어댑터의 ``` handle(request, response, handler)``` 메서드를 통해 실제 어댑터가 호출된다. 어댑터는 handler(컨트롤러)를 호출하고 그 결과를 어댑터에 맞추어 반환한다.

## 6/15
<img width="719" alt="스크린샷 2022-06-15 오후 7 52 55" src="https://user-images.githubusercontent.com/92084680/173810659-842965b2-9ef9-4bef-8d5c-e7c550aa40bb.png">
> Spring MVC 의 프론트컨트롤러가 DispatcherServlet
> ``` DispatcherServlet ``` 도 부모클래스에서 ```HttpServlet``` 을 상속받아서 사용하고 서블릿으로 동작한다.
> 스프링 부트는 ```DispacherServlet``` 을 서블릿으로 자동으로 등록하면서 모든 경로( urlPatterns="/" )에 대해서 매핑한다.

1. 서블릿이 호출되면 HttpServlet이 제공하는 service()가 호출된다.
2. Spring MVC는 ```DispacherServlet```의 부모인 ```FrameworkServlet``` 에서 ```service()```를 오버라이드 해두었다.
3. ```FrameworkServlet.service()``` 를 시작으로 여러 메서드가 호출되면서 ```DispacherServlet.doDispatch()``` 가 호출된다.
****
>  ```DispacherServlet.doDispatch()``` 가 하는 일
1. 핸들러 조회: 핸들러 매핑을 통해 요청 URL에 맵핑된 핸들러(컨트롤러)를 조회한다.
2. 핸들러를 처리할 수 있는 핸들러 어댑터 조회 
3. 핸들러 어댑터 실행 - > 핸들러 어댑터를 통한 핸들러 실행 
4. ModelAndView 반환 : 핸들러 어댑터는 핸들러가 반환하는 정보를 ModelAndView로 변환해서 반환한다.
5. 뷰 렌더링 호출 
6. 뷰 리졸버를 통해서 뷰 찾기
7. 뷰 반환 : 뷰 리졸버는 뷰의 논리 이름을 물리 이름으로 바꾸고, 렌더링 역할을 담당하는 뷰 객체를 반환한다. 
8. 뷰 렌더링

****
HttpRequestHandler
> 서블릿과 가장 유사한 형태의 핸들러
```
@Component("/springmvc/request-handler")
  public class MyHttpRequestHandler implements HttpRequestHandler {
@Override
      public void handleRequest(HttpServletRequest request, HttpServletResponse
  response) throws ServletException, IOException {
          System.out.println("MyHttpRequestHandler.handleRequest");
      }
}
```
> 핸들러 매핑으로 핸들러 조회
1. HandlerMapping 을 순서대로 실행해서, 핸들러를 찾는다.
2. 이 경우 빈 이름으로 핸들러를 찾아야하기 때문에 이름 그대로 빈 이름으로 핸들러를 찾아주는 BeanNameUrlHandlerMapping 가 실행에 성공하고 핸들러인 MyHttpRequestHandler 를 반환한다.
> 핸들러 어댑터 조회
1. HandlerAdapter 의 supports() 를 순서대로 호출한다.
2. HttpRequestHandlerAdapter 가 HttpRequestHandler 인터페이스를 지원하므로 대상이 된다.
> 핸들러 어댑터 실행
1. 디스패처 서블릿이 조회한 HttpRequestHandlerAdapter 를 실행하면서 핸들러 정보도 함께 넘겨준다.
2. HttpRequestHandlerAdapter 는 핸들러인 MyHttpRequestHandler 를 내부에서 실행하고, 그 결과를 반환한다.
****
 핸들러 어댑터 호출
1. 핸들러 어댑터를 통해 new-form 이라는 논리 뷰 이름을 획득한다.
> ViewResolver 호출
2. new-form 이라는 뷰 이름으로 viewResolver를 순서대로 호출한다.
   BeanNameViewResolver 는 new-form 이라는 이름의 스프링 빈으로 등록된 뷰를 찾아야 하는데 없다. InternalResourceViewResolver 가 호출된다.
> InternalResourceViewResolver
3. 이 뷰 리졸버는 InternalResourceView 를 반환한다.
> 뷰 - InternalResourceView
4. InternalResourceView 는 JSP처럼 포워드 forward() 를 호출해서 처리할 수 있는 경우에 사용한다.
> view.render()
5. view.render() 가 호출되고 InternalResourceView 는 forward() 를 사용해서 JSP를 실행한다.

##### 가장 우선순위가 높은 핸들러 매핑과 핸들러 어댑터는 RequestMappingHandlerMapping , RequestMappingHandlerAdapter 
