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
