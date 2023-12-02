---

layout: single
title: "[Springboot] Validation"
toc: true
toc_sticky: true
toc_label: " "
categories: Springboot

---

## 유효성 검사

Spring MVC에서는 Java Bean 유효성 검사 옵션을 포함하여 메서드에 대한 유효성 검사를 지원함

유효성 검사 지원은 두 가지 방법이 있음

1. `@ModelAttribute`, `@RequestBody`, `@RequestPart`와 같은 메서드 매개 변수가 수행
2. Java Bean 유효성 검사가 있는 경우와 메서드 매개 변수에 주석이 있는 경우 모든 메서드 인수에 적용되어 유효성 검사 오류 발생

<aside>
💡 - 컨트롤러에 클래스 수준이 있는 경우 메서드 유효성 검사는 AOP 프록시를 통해 적용
<br>- Spring MVC 내장 지원을 활용하려면 Spring Framework 6.1부터 가능

</aside>

메서드 유효성 검사 오류에 대한 추가 사용자 지정 처리를 위해 컨트롤러에서 메서드를 확장하거나 핸들러에서 직접 처리 

예외에는 유효성 검사 오류를 그룹화하는 목록이 포함

```java
HandlerMethodValidationException ex = ... ;

ex.visitResults(new HandlerMethodValidationException.Visitor() {

	@Override
	public void requestHeader(RequestHeader requestHeader, ParameterValidationResult result) {
			// ...
	}

	@Override
	public void requestParam(@Nullable RequestParam requestParam, ParameterValidationResult result) {
			// ...
	}

	@Override
	public void modelAttribute(@Nullable ModelAttribute modelAttribute, ParameterErrors errors) {

	// ...

	@Override
	public void other(ParameterValidationResult result) {
			// ...
	}
});
```

### @Valid는 어떻게 사용할까

Spring boot에서 `@Valid` 를 이용하여 validation을 할 수 있음

RestController를 이용하여 `@RequestBody` 객체를 사용자로부터 가져올 때, 들어오는 값들을 검증하는 방법

`@Valid` 를 이용하면, service 단이 아닌 객체 안에서 들어오는 값에 대해 검증할 수 있음

```java
@PostMapping("/accounts")
public void handle(@RequestBody Account account) {
	// ...
}
```

```java
@PostMapping("/accounts")
public void handle(@Valid @RequestBody Account account, Errors errors) {
	// ...
}
```

파라미터로 `@RequestBody` 옆에 `@Valid` 를 작성하면 RequestBody로 들어오는 객체에 대한 검증을 수행

이 검증에 세부적인 사항은 객체 안에 정의해두어야 함

Dto 객체를 정의한 후 각 필드에 맞는 어노테이션 사용

ex) 

`@NotNull` : 인자로 들어온 필드 값에 null 허용하지 않음

`@Email` : 인자로 들어온 값은 Email 형식을 갖추어야 함

> 자동으로 일정 규격에 맞춰서 Springboot가 생성한 error 형식에 따라 response가 나감<br>
즉, `@Valid`와 검증 어노테이션만 같이 잘 작성해도 객체 단에서 에러를 잡을 수 있음 👍🏻👍🏻👍🏻
>

### @Valid시 발생하는 Exception Handling

`@Valid` 사용시 발생하는 Exception 처리를 할 때 `@ControllerAdvice` 를 이용한다고 함 

두 어노테이션은 같이 쓰는 조합은 실무에서도 많이 사용 된다고 함

<aside>
💡   @ControllerAdvice란?
모든 @Controller 즉, 전역에서 발생할 수 있는 예외를 잡아 처리해주는 어노테이

</aside>

```java
@RestControllerAdvice
public class ApiControllerAdvice {

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Map<String, String>> handleValidationExceptions(MethodArgumentNotValidException ex){
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getAllErrors()
                .forEach(c -> errors.put(((FieldError) c).getField(), c.getDefaultMessage()));
        return ResponseEntity.badRequest().body(errors);
    }

}
```

ResponseEntity 값으로 

- error가 난 field 값과
- 에러 메시지를 Map 형태로 만들어서, Response로 넣어주었음

이때 Map으로 선언하여 forEach를 한 이유는

- @Valid를 사용할 때 해당 객체에서
- **valid에 실패한 내용을 모두 리턴해주기** 때문에
- 모든 error 값을 수용하기 위해서임

이렇게 하면 @Valid를 통과하지 못한 모든 필드 값에 대한 에러와 에러 내용을 커스텀 해준 것이 모두 반영됨

---

### 참고자료

[@Valid 를 이용해 @RequestBody 객체 검증하기](https://jyami.tistory.com/55)

[@RequestBody :: Spring Framework](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/requestbody.html#page-title)

[Validation :: Spring Framework](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-validation.html#page-title)