#Chapter2. 웹 애플리케이션 개발하기

## 2.1 정보 보여주기

예제로 다음을 만든다.
- 도메인 : 타코 식자재 속성을 정의
- 컨트롤러 : 식자재 정보를 뷰에 전달
- 뷰템플릿 : 식자재의 내역을 사용자의 브라우저에 보여줌


###2.1.1 도메인 설정하기
도메인은 이해에 필요한 개념을 다루는 영역이다.


```java
타코 식자재 정의하기

@Data
@RequiredArgsConstructor
public class Ingredient {
    private final String id;
    private final String name;
    private final Type type;
    
    public static enum Type {
        WRAP, PROTEIN, VEGGIES, CHEESE, SAUCE
    }
}

@Data
public class Taco {
    private String name;
    private List<STring> ingredients;
}
```

###2.1.2 컨트롤러 클래스 생성하기

```java
@Slf4j
@Controller
@RequestMapping("/design")
public class DesignTacoController {
   @GetMapping
   public String showDesignForm(Model model) {
       model.addAttribute("taco", new Taco());
       model.addAttribute("ingredients", ingredientsList());
       return "design";
   }
}
```


```java
스프링 MVC 요청 - 대응 어노테이션
@RequestMapping
@GetMapping
@PostMapping
@PutMapping
@DeleteMapping
@PatchMapping
```


###2.1.3 뷰 디자인하기
```xml
https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-thymeleaf
타임리프 디펜던시 추가
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>

```


```html
모델 값 사용하기
<p th:text="${message}">placeholder message</p>

모델의 리스트 값 사용하기
<div th:each="ingredient : ${wrap}">
    <input name="ingredients" type="checkbox" th:value="${ingredient.id}" />
    <span th:text="${ingredient.name}">INGREDIENT</span><br/>
</div>


```

## 2.2 폼 제출 처리하기

```java
@Data
public class Order {
    private String deliveryName;
    private String deliveryStreet;
    private String ccNumber;
    private String ccExpiration;
    private String cccVV;
}
```

```java
@PostMapping을 사용해서 POST 요청 처리하기

@PostMapping
public String processDesign(Taco design){
    // 3장에서 추가) 타코 디자인을 저장한다.
    log.info("Processing design: " = design);
    return "redirect:/orders/current";
   
}
```
```
타코 주문 제출 처리하기
@PostMapping
public String processOrder(Order order){
  return "redirect:/";
}
```

```java
@GetMapping을 이용하여 타코 주문 폼을 나타내는 컨트롤러

@GetMapping("/current")
publici String orderForm(Model model) {
    model.addAttribute("order", new Order());
    return  "orderForm";
}
```

## 2.3 폼 입력 유효성 검사하기

### 2.3.1 유효성 검사 규칙 선언하기
```java

@NotNull
@Size(min=5, message="n=Name must be at least 5 characters long")
@NotBlank(message="Name is required")
@CreditCardNumber(message"Not a valid credit card number")
@Pattern(regexp="^(0[1-9]|1[0-2])(\\/])([1-9][0-9])$", messgage = "Must be formatted MM/YY")
@Digits(integer=3, fraction=0, message = "Invalid CVV")

```

### 2.3.2 폼과 바인딩될때 유효성 


```java
@PostMapping
public String processDesign(@Valid Taco design, Errors errors) {
   if(errors.hasErrors()){
       return "design";
   }
}
```

@Valid 어노테이션을 이용하여 processDesign() 메서드의 코드가 실행되기 전에 유효성 검사를 하라고 스프링 MVC에게 알려준다.
만약 에러가 발생하면 Errors객체의 hasError() 메서드를 이용하여 검사 에러가 있는지 확인한다.


### 2.3.3 유효성 검사 에러 보여주기
Thymeleaf는 fields와 th:errors 속성을 통해서 Errors객체를 사용한다.

```html
<label for="ccNumber"> Credit Card #: </label>
<input type="text" th:field="*{ccNumber}"/>
<span class="validationError"
      th=if="${#fields.hasErrors('ccNumber')}"
      th:errors="*{ccNumber}">CC Num Error</span>
     
```


## 2.4 뷰 컨트롤러로 작업하기
간단한 맵핑은 컨트롤러를 생성하지 않고, WebMvcConfigurer를 구현하여 똑같은 역할을 제공할 수 있다.
WebMvcConfigurer는 부트스트랩 클래스인 TacoCloudApplication에 추가할 수 있다.
```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
   @OVerride
   public void addViewControllers(ViewControllerRegistry registry) {
      registry.addViewController("/").setViewName("home");
   }
}
```

## 2.5 뷰 템플릿 라이브러리 선택하기
스프링에서 
## 2.5 뷰 템플릿 라이브러리 선택하기지원되는 
## 2.5 뷰 템플릿 라이브러리 선택하기템플릿
## 2.5 뷰 템플릿 라이브러리 선택하기
- FreeMarker
- Grooby 템플릿
## 2.5 뷰 템플릿 라이브러리 선택하기
- JSP
- Mustache
- Thymeleaf


### 2.5.1 템플릿 캐싱
개발을 할때 페이지를 수정해도 수정 전의 페이지를 보게 될 수도 있다.
이를 방지하기 위해 템플릿 캐싱을 비활성화 할 수 있다.

아래의 기능은 DevTools를 이용하면 설정하지 않아도 된다.
```
타임리프 기준
spring.thymeleaf.cache=false
```
