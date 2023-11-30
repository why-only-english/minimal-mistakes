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
- Spring MVC 내장 지원을 활용하려면 Spring Framework 6.1부터 가능

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

> 자동으로 일정 규격에 맞춰서 Springboot가 생성한 error 형식에 따라 response가 나감
즉, `@Valid`와 검증 어노테이션만 같이 잘 작성해도 객체 단에서 에러를 잡을 수 있음 👍🏻👍🏻👍🏻
>