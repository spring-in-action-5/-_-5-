#1. 스프링 시작하기

가장 많이 개발된 애플리케이션 유형은 RDMS를 사용하는 웹브라우저 기반의 웹 애플리케이션.
요즘의 추세는 다양한 DB에 데이터를 저장하는 클라우드에 맞춰진 MSA에 관심이 높다. 더불어 확장성과 향상된 성능을 제공하는 Reactive Programming도 새로운 관심사이다.


## 1.1 스프링이란?

**스프링 애플리케이션 컨텍스트** 라는 **컨테이너**를 제공하는데, 애플리케이션 컴포넌트들을 생성하고 관리한다.
빈의 상호 연결은 **의존성 주입** 기반으로 수행된다.

핵심 컨테이너 외에도 관련 라이브러리에서는 웹프레임워크, 다양한 데이터 저장 옵션, 보안 프레임워크, 타 시스템과의 통합, 런타임 모니터링, 마이크로 서비스 지원, 리액티브 프로그래밍 등 많은 다른 기능을 제공한다.

```java
컴포넌트 및 다른 컴포넌트와의 관계를 나타내는 설정방법

@Configuration
public class ServiceConfiguration {
    @Bean
    public InventoryService inventoryService() {
        return new InventoryService();
    }
}

```

XML 기반 구성에 비해 자바 기반 구성은 더 강화된 타입 안전과 향상된 리팩토링 기능을 포함하여 많은 이점이 있다.
그리너 자동-구성 기능이 있어서 자동-구성을 할 수 없을 경우에만 사용한다.
자동-구성은 **자동 연결(Autowiring)**과 **컴포넌트 검색(Component Scanning)** 이라는 스프링 기법을 이용한다.

## 1.2 스프링 애플리케이션 초기 설정하기
이 책의 진도를 나가면서 타코를 주문하는 타코 클라우드 온라인 애플리케이션을 만들 것이다.
스프링을 초기 설정하는 대표적인 방법인 [스프링 Initializr](https://start.spring.io/)를 사용한다.

### 1.2.2 스프링 프로젝트 구조 살펴보기
- mvnw, mvnw.cmd : 메이븐 래퍼 스크립트. 메이븐이 각자 컴퓨터에 설치 되어 있지 않더라도 이 스크립트를 사용하여 빌드 가능. 비슷하게 gradle에서는 gradlew가 존재한다.
- pom.xml : 메이븐 빌드 명세(필요한 정보)
- TacoCloudApplication.java : 스프링 부트 메인 클래스
- application.properties : 스프링 구성 속성(5장과 관련)
- static : 정적 콘텐츠 폴더
- templates : 브라우저에 콘텐츠를 보여주는 템플릿 폴더
- TacoCloudApplicationTests.java : 스프링 부트 메인 클래스 테스트 파일


#### 애플리케이션의 부트스트랩(구동)
[@SpringBootApplication](https://docs.spring.io/spring-boot/docs/2.1.13.RELEASE/reference/html/using-boot-using-springbootapplication-annotation.html#using-boot-using-springbootapplication-annotation)은 3개의 어노테이션이 결합한 것이다.
```java
@SpringBootConfiguration // 현재 클래스를 구성 클래스로 지정
@EnableAutoConfiguration // 스프링 부타 자동-구성 활성화
@ComponentScan(excludeFilters = { // 컴포넌트 검색 활성화, @Component, @Controller, @Service
		@Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
} 
```

## 1.3 스프링 애플리케이션 작성하기

#### 컨트롤러 생성
```java
@Controller <- 컨트롤러 지정
public class HomeController {
    @GetMapping("/") <- 경로 이름
    public String home() {
        return "home"; <- 뷰이름
    }
}
```

#### 뷰 정의하기 
```html
/src/main/resources/templates/home.html

<body>
  <img th:src="@{/images/TacoCloud.png}"/>  // /src/main/resources/imges/TacoCloud.png를 참조할 수 있다.
</body>
```

#### 컨트롤러 테스트하기
참조 :[Server-side-tests](https://docs.spring.io/spring/docs/current/spring-framework-reference/testing.html#spring-mvc-test-server)
아래 코드는 다음을 테스트한다.
- 응답코드가 200 OK인가?
- 뷰의 이름이 home인가?
- 브라우저에 "Welcome to"가 포함되어 보이는가?
```java
@WebMvcTest(HomeController.class)
public class HomeControllerTest{
    @Autowired
    private MockMvc mockMvc; < MockMvc 주입
    
    @Test
   public void testHomePage() throws Exception {
   mockMvc.perform(get("/")
     .andExpect(status().isOk())
     .andExpect(view().name("home"))
     .andExpect(content().string(containsString("Welcome to...")));
   }

}
```


### 1.3.5 스프링부트 DevTools 알아보기
- 코드가 변경될때 자동으로 애플리케이션을 다시 시작시킨다.
- 리소스가 변경될때 자동으로 새로고침한다.
- 템프릿 캐시를 자동으로 비활성화한다.
- 만일 H2 DB가 사용중이라면 자동으로 H2 콘솔을 활성화한다ㅏ.


##1.4 스프링 살펴보기

###1.4.1 핵심 스프링 프레임워크
- 스프링 MVC
- JPA
- JdbcTemplate
- WebFlux
- 스프링 부트
- 스프링 시큐리티
- 스프링 통합과 배치
- 스프링 클라우드
