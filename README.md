# 2. 코틀린 기초

## 2.1 함수와 변수

### 간단한 예제
```kotlin
fun main(argss: Array<String>) {
    println("Hello, world!")
}
```

- 함수 선언 시, fun 키워드 사용
- 파라미터 이름 뒤에 파라미터의 타입을 사용 (자바랑 반대)
- 함수를 최상위 수준으로 정의 (클래스 안에 선언할 필요 없음)
- 배열도 일반적인 클래스와 동일 (배열을 위한 문법이 존재하지 않음)
- 줄 끝에 세미콜론(;) 생략 가능


```kotlin
@kotlin.internal.InlineOnly
public actual inline fun println(message: Any?) {
    System.out.println(message)
}
```
- 코틀린 표준 라이브러리는 자바 라이브러리를 감싼 래퍼 클래스 제공

### 2.1.2 함수
- 결과값을 반환하는 함수

```kotlin
// 블록이 본문인 함수
fun max(a: Int, b: Int): Int {
    return if (a > b) a else b
}

// 식이 본문인 함수 (자주 사용)
fun max(a: Int, b: Int): Int = if (a > b) a else b

// 반환 타입 생략 (식이 본문인 함수의 경우(중요) 반환타입이 없어도 컴파일러가 타입 추론)
fun max(a: Int, b: Int) = if (a > b) a else b
```
- 함수 선언은 fun 키워드로 시작
- fun 다음에 함수 이름
- 함수 이름 뒤에는 괄호 안에 파라미터 목록
- 함수 반환 타입은 파라미터 다음에 오는데, 콜론(:) 으로 구분

#### 문(statement)과 식(expression)의 구분
- 식은 값을 만들어 내며 다른 식의 하위 요소로 계산에 참여할 수 있다.
- 문은 자신이 둘러싸고 있는 가장 안쪽 블록의 최상위 요소로 존재하며 아무런 값을 만들어내지 않는다.
  - 식은 값을 반환하는 하나의 코드, 문은 여러 개의 코드를 포함하여 작업을 수행하는 코드 (행위)

- 자바에서는 모든 제어 구조가 문인 반면 코틀린에서는 루프를 제외한 대부분의 제어 구조가 식이다.


### 2.1.3 변수
```kotlin
// 불가
String question = "나이"
Int answer = 20

// 명시적 타입
val question: String = "나이"
val answer: Int = 20

// 타입 생략
val question = "나이"
val answer = 20

// 타입 추론 불가
val question
val answer
```

- 식과 변수 선언을 구별하기 위해, 변수 이름 뒤에 타입을 명시하거나 생략을 허용
- 식이 본문인 함수와 동일하게 타입이 생략되면 컴파일러가 초기 식을 분석해서 타입 추론 해줌 
- 초기화 식 사용 안한다면 타입을 반드시 명시 (타입 추론 불가)

#### 변경 가능한 변수, 변경 불가능한 변수
- val(value) - 변경 불가능한 참조를 저장하는 변수 (immutable)
- var(variable) - 변경 가능한 참조 (mutable)
  - 권장은 val을 기본으로 사용하고 필요할 때만 var로 변경


```kotlin
val message: String

if(condition()) {
    message = "Success"
} else {
    message = "Failed"
}
```
- val 변수는 블록을 실행할 때 한번만 초기화 되야 하지만, 한 초기화 문장만 실행됨을 컴파일러가 확인할 수 있다면 조건에 따른 여러 값으로 초기화 가능
- val 참조는 불변이라고 그 참조가 가르키는 내부 값은 변경될 수 있음 (자바랑 동일)


```kotlin
var question = "나이"
question = 20 // 컴파일 오류
```
- var는 변수의 값을 변경할 수 있지만 타입은 변경 못함 (초기화식으로 타입을 추론하며, 재대입 시 추론한 타입으로 검사)

### 2.1.4 문자열 템플릿

- java
```java
String name = "aiden";
System.out.println(String.format("Hello, %s!", name));
System.out.println("Hello, " + name + "!");

result : Hello, aiden!
```

- kotlin
```kotlin
val name = "aiden"
println("Hello, $name!") // 변수가 존재 하지 않으면 컴파일 오류

result : Hello, aiden!
```
- 문자열 템플릿 기능 사용 (변수 앞에 $ 추가)
- 복잡한 식도 중괄호({})로 둘러싸서 문자열 템플릿 안에 넣을 수 있음
  - 가독성도 더 좋고, 컴파일러가 한글을 식별자로 인식할 수 있으므로 이 방법 권장

## 클래스와 프로퍼티

## enum과 when (선택 표현과 처리)

## while과 for 루프 (대상을 이터레이션)

## 예외 처리
