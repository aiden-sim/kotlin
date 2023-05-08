# 코틀린 타입 시스템

## 6.1 널 가능성
- 코틀린을 비롯한 최신 언어에서는 null에 대한 접근 방법은 컴파일 시점으로 옮기는 것

### 6.1.1 널이 될 수 있는 타입
```kotlin
fun main(args: Array<String>) {
    strLen(null) // Null can not be a value of a non-null type String
}

fun strLen(s:String) = s.length
```
- 이 함수가 널과 문자열을 인자로 받게 하려면 이름 뒤에 물음표(?) 를 명시한다.
- 어떤 타입이든 타입 이름 뒤에 물음표를 붙이면 그 타입의 변수나 프로퍼티에 null 참조를 저장할 수 있다는 뜻

- 이렇게 제약이 많다면 널이 될 수 있는 타입의 값으로 대체 무엇을 할 수 있을까?
  - 가장 중요한 일은 null과 비교하는 것 

```kotlin
fun strLenSafe(s: String?) : Int =
    if(s != null) s.length else 0
```

### 6.1.2 타입의 의미
- 타입은 분류로 타입은 어떤 값들이 가능한지와 그 타입에 대해 수행할 수 있는 연산의 종류를 결정
- java8 부터는 Optional 도입
  - 코드가 지저분해지고, 성능이 저하되는 단점이 있다.

### 6.1.3 안전한 호출 연산자 ?.
- 코틀린이 제공하는 가장 유용한 도구 중 하나가 안전한 호출 연산자인 `?.` 이다.
  - `?.` 은 null 검사와 메소드 호출을 한 번의 연산으로 수행

- 메소드 호출뿐 아니라 프로퍼티를 읽거나 쓸 때도 안전한 호출을 사용할 수 있다.

- 객체 그래프에서 널이 될 수 있는 중간 객체가 여럿 있다면 한 식 안에서 안전한 호출을 연쇄해서 사용 가능
```kotlin
class Address(
    val streetAddress: String, val zipCode: Int,
    val city: String, val country: String
)

class Company(val name: String, val address: Address?)

class Person(val name: String, val company: Company?)

fun Person.countryName(): String {
    val country = this.company?.address?.country
    return if (country != null) country else "Unknown"
}
```

### 6.1.4 엘비스 연산자: ?:
- 코틀린은 null 대신 사용할 디폴트 값을 지정할 때 편리한 연산자를 제공하는데 엘비스 연산자라고 한다.

```kotlin
fun Person.countryName() = company?.address?.country ?: "Unknown"
```

### 6.1.5 안전한 캐스트: as?
- `as?` 연산자는 어떤 값을 지정한 타입으로 캐스트한다. 타입으로 변환할 수 없으면 null을 반환

- 안전한 호출, 안전한 캐스트, 엘비스 연산자는 유용하기 때문에 코틀린 코드에서 자주 사용 

### 6.1.6 널 아님 단언: !!
- 느낌표를 이중(!!) 으로 사용하면 어떤 값이든 널이 될 수 없는 타입으로 바꿀 수 있다.

```kotlin
fun ignoreNulls(s: String?) {
    val sNotNull: String = s!!
    println(sNotNull.length)
}
```

### 6.1.7 let 함수
- let 함수를 안전한 호출 연산자와 함께 사용하면 원하는 식을 평가해서 결과가 널인지 검사한 다음에 그 결과를 변수에 넣는 작업을 간단한 식을 사용해 한꺼번에 처리 가능

```kotlin
fun main(args: Array<String>) {
    var email: String? = "yole@example.com" // 호출됨
    //email = null // email에 null을 할당하면 호출 안됨
    email?.let { sendEmailTo(it) }
}

fun sendEmailTo(email: String) {
    println("Sending email to $email")
}
```

### 6.1.8 나중에 초기화할 프로퍼티
- 코틀린에서는 클래스 안의 널이 될 수 없는 프로퍼티를 생성자 안에서 초기화하지 않고 특별한 메소드 안에서 초기화할 수 없다.
- `lateinit` 을 붙이면 프로퍼티를 나중에 초기화할 수 있다.
- 나중에 초기화하는 프로퍼티는 항상 var 여야 한다.

### 6.1.9 널이 될 수 있는 타입 확장
- 널이 될 수 있는 타입에 대한 확장 함수를 정의하면 null 값을 다루는 강력한 도구로 활용가능

### 6.1.10 타입 파라미터의 널 가능성
- 타입 파라미터 T를 클래스나 함수 안에서 타입 이름으로 사용하면 이름 끝에 물음표가 없더라고 T가 널이될 수 있는 타입이다.
- 타입 파라미터는 널이 될 수 있는 타입을 표시하려면 반드시 물음표를 이름 뒤에 붙여야 하는 규칙의 유일한 예외

### 6.1.11 널 가능성과 자바

#### 플랫폼 타입
- 플랫폼 타입은 코틀린이 널 관련 정보를 알 수 없는 타입을 말한다.
  - 판단을 사용자가 해서, 널 처리 해야된다. 아니면 자바처럼 NPE 발생한다. 

#### 상속
- 코틀린에서 자바 메소드를 오버라이드할 때 그 메소드의 파라미터와 반환 타입을 널이 될 수 있는 타입으로 선언할지 널이 될 수 없는 타입으로 선언할지 결정해야 함

## 6.2 코틀린의 원시 타입

### 6.2.1 원시 타입: Int, Boolean 등

### 6.2.2 널이 될 수 있는 원시 타입: Int?, Boolean? 등

### 6.2.3 숫자 변환

### 6.2.4 Any, Any?: 최상위 타입

### 6.2.5 Unit 타입: 코틀린의 void

### 6.2.6 Nothing 타입: 이 함수는 결코 정상적으로 끝나지 않는다.


## 6.3 컬렉션과 배열

### 6.3.1 널 가능성과 컬렉션

### 6.3.2 읽기 전용과 변경 가능한 컬렉션

### 6.3.3 코틀린 컬렉션과 자바

### 6.3.4 컬렉션을 플랫폼 타입으로 다루기

### 6.3.5 객체의 배열과 원시 타입의 배열




