# 4. 클래스. 객체, 인터페이스
- 자바와 달리 코틀린 선언은 기본적으로 `final` 이며 `public` 이다.
- 코틀린의 중첩 클래스는 기본적으로 내부 클래스가 아니다.

## 4.1 클래스 계층 정의
- 코틀린 가시성/접근 제한자는 자바와 비슷하지만 아무것도 지정하지 않은 경우 기본 가시성이 다르다.

### 4.1.1 코틀린 인터페이스
- 코틀린 인터페이스는 자바8 인터페이스와 비슷 (추상 메서드 뿐만 아니라 구현 메소드 정의 가능)
- 다만 인터페이스에는 아무런 상태(필드) 도 들어갈 수 없음

```kotlin
interface Clickable {
    fun click()   // 일반 메소드 선언 (추상 메소드)
    fun showOff() = println("I'm clickable!") // 디폴트 구현 메소드
}

class Button : Clickable {
    override fun click() = println("I was clicked")
}

Button().click() // I was clicked
```

- 코틀린에서는 클래스 이름 뒤에 콜론(:) 을 붙이는 걸로 클래스 확장과 인터페이스 구현을 모두 처리
- 자바와 마찬가지로 클래스는 인터페이스 확장 갯수 제한 없지만 클래스는 오직 하나만 확장 가능
- 자바와 달리 코틀린은 `override` 변경자를 꼭 사용해야 함
- 인터페이스 메소드도 디폴트 구현을 제공. 그냥 메소드 본문을 메소드 시그니처 뒤에 추가하면 됨

```kotlin
interface Clickable {
    fun click()
    fun showOff() = println("I'm clickable!")
}

interface Focusable {
    fun setFocus(b: Boolean) =
        println("I ${if (b) "got" else "lost"} focus.")

    fun showOff() = println("I'm focusable!")
}
```
- 한 클래스에서 이 두 인터페이스를 함께 구현하면 어떻게 될까?!
  - 어느쪽도 선택되지 않는다. (컴파일 오류 발생)


```kotlin
class Button : Clickable, Focusable {
    override fun click() = println("I was clicked")
    
    // 여러개 호출
    override fun showOff() {  // 이름과 시그니처가 같은 멤버 메소드에 대해 둘 이상의 디폴트가 있는 경우 하위 클래스에 명시적으로 새로운 구현을 제공
        super<Clickable>.showOff()
        super<Focusable>.showOff()
    }
    
    // 하나 호출
    override fun showOff() = super<Clickable>.showOff()
}
```
- 자바에서는 `Clickable.super.showOff()`
- 코틀린에서는 `super<Clickable>.showOff()`

### 4.1.2 open, final, abstract 변경자: 기본적으로 final
- 취약한 기반 클래스 라는 문제는 하위 클래스가 기반 클래스에 대해 가졌던 가정이 기반 클래스를 변경함으로써 깨져버린 경우 생김 (오버라이드)
- 코틀린의 클래스와 메소드는 기본적으로 `final` 이다.
- 상속을 허용하려면 클래스 앞에 `open` 변경자를 붙여야 한다.
- 오버라이드를 허용하고 싶은 메소드나 프로퍼티의 앞에도 `open` 변경자를 붙여야 한다.

```kotlin
class RichButton : Clickable {
    fun disable() {}     // 파이널 함수. 하위에서 오버라이드 못함

    open fun animate() {} // 이 함수는 열려 있다. 하위에서 오버라이드 가능

    override fun click() {} // 상위 클래스에 선언된 열려있는 메소드를 오버라이드. 오버라이드한 메소드는 기본적으로 열려 있다.
}
```
- 기반 클래스나 인터페이스의 멤버를 오버라이드하는 경우, 그 메소드는 기본적으로 열려 있다.

```kotlin
class RichButton : Clickable {
    final override fun click() {} // final이 없는 override 메소드나 프로퍼티는 기본적으로 열려 있다.
}
```

- 자바처럼 코틀린에서도 클래스를 `abstract`로 선언할 수 있다.
    - 추상 클래스는 인스턴스화할 수 없음
    - 추상 멤버는 항상 열려 있음 (`open` 변경자 명시할 필요 없음)

- 인터페이스 멤버는 항상 열려 있으며 `final`로 변경할 수 없다.
- 인터페이스 멤버에게 본문이 없으면 자동으로 추상 멤버가 되지만, 따로 `abstract` 키워드를 붙일 필요 없음


### 4.1.3 가시성 변경자: 기본적으로 공개
- 기본적으로 코틀린 가시성 변경자는 자바와 비슷
    - 하지만 코틀린의 기본 가시성은 자바와 다름. 아무 변경자도 없는 경우 `public` 하게 처리됨
    - 코틀린은 패키지 가시성 사용 안함

- 패키지 전용 가시성 대안은 코틀린에서는 `internal` 이라는 새로운 가시성 변경자 도입 (모듈 내부에서만 가능)
    - 모듈은 한번에 한꺼번에 컴파일되는 코틀린 파일들을 의미

- 코틀린에서는 최상위 선언에 대해 `private` 을 허용 (클래스, 함수, 프로퍼티 등) 

- 코틀린은 `public` 함수 내에서 가시성이 더 낮은 메소드를 참조하지 못하게 한다.

- 클래스를 확장한 함수는 그 클래스의 `private`나 `protected` 멤버에 접근할 수 없다.


### 4.1.4 내부 클래스와 중첩된 클래스: 기본적으로 중첩 클래스
- 자바와의 차이는 코틀린의 중첩 클래스는 명시적으로 요청하지 않는 한 바깥쪽 클래스 인스턴스에 대한 접근 권한이 없음
- 자바에서는 다른 클래스 안에 정의한 클래슨느 자동으로 내부 클래스가 됨(직렬화 방해)
    - 이 문제 해결하려면 내부 클래스를 static 으로 선언해야 됨

```kotlin
class Button : View {
    override fun getCurrentState(): State = ButtonState()

    override fun restoreState(state: State) {
        TODO("Not yet implemented")
    }

    class ButtonState : State {}    // 자바의 정적 중첩 클래스와 대응
}
```
- 내부 클래스로 변경해서 바깥쪽 클래스에 대한 참조를 포함하게 만들고 싶다면 `inner` 변경자를 붙여야 함
- 내부 클래스 `inner` 안에서 바깥쪽 클래스 `outer` 의 참조에 접근하려면 `this@outer` 라고 써야됨

```kotlin
class Outer {
    inner class Inner {
        fun getOuterReference(): Outer = this@Outer
    }
}
```

### 4.1.5 봉인된 클래스: 클래스 계층 정의 시 계층 확장 제한
- 상위 클래스에 `sealed` 변경자를 붙이면 그 상위 클래스를 상속한 하위 클래스 정의를 제한할 수 있음
    - 반드시 상의 클래스 안에 중첩시켜야 함

```kotlin
sealed class Expr { // 기반 클래스를 sealed로 봉인
    class Num(val value: Int) : Expr()  // 기반 클래스의 모든 하위 클래스를 중첩 클래스로 나열
    class Sum(val left: Expr, val right: Expr) : Expr()
}

fun eval(e: Expr): Int =
    when (e) {  // when 식이 모든 하위 클래스를 검사하므로 별도의 else 분기 필요 없음
        is Expr.Num -> e.value
        is Expr.Sum -> eval(e.right) + eval(e.left)
    }
```
- `when` 식에서 `sealed` 클래스의 모든 하위 클래스를 처리한다면 디폴트 분기 필요 없음
- 나중에 `Expr` 하위 클래스가 추가되도 컴파일 에러가 발생해서 금방 알아챌 수 있음

## 4.2 뻔하지 않은 생성자와 프로퍼티를 갖는 클래스 선언
- 코틀린은 주 생성자와 부 생성자를 구분
- 코틀린은 초기화 블록을 통해 초기화 로직을 추가할 수 있음

### 4.2.1 클래스 초기화: 주 생성자와 초기화 블록

```kotlin
class User(val nickname: String)
```
- 클래스 이름 뒤에 오는 괄호로 둘러싸인 코드를 주 생성자라고 부름
- 주 생성자는 파라미터를 지정하고 그 생성자 파라미터에 의해 초기화되는 프로퍼티를 정의하는 두 가지 목적

```kotlin
class User constructor(_nickname: String) { // 파라미터가 하나만 있는 주 생성자
    val nickname: String

    init {                                  // 초기화 블록
        nickname = _nickname
    }
}
```
- `constructor` 키워드는 주 생성자나 부 생성자 정의를 시작할 때 사용
- `init` 키워드는 초기화 블록을 시작 (클래스의 객체가 만들어질 때 실행될 초기화 코드)

- 기반 클래스를 초기화하려면 기반 클래스 이름 뒤에 괄호를 치고 생성자 인자를 넘김
```kotlin
open class User2(val nickname: String) {
}

class TwitterUser(nickname: String) : User2(nickname) {
}
```

- 기반 클래스의 생성자는 아무 인자도 받지 않지만, 상속한 하위 클래스는 반드시 기반 클래스의 생성자를 호출해야 함
```kotlin
open class Button

class RedioButton : Button()
```

- 인터페이스는 생성자가 없기 때문에 자식 클래스가 인터페이스를 구현하는 경우, 인터페이스 이름 뒤에는 아무 괄호도 없음


### 4.2.2 부 생성자: 상위 클래스를 다른 방식으로 초기화
- 코틀린에서 생성자를 여러개 지원하는 경우는 프레임워크 클래스 확장 시, 여러 가지 방법으로 인스턴스 초기화할 수 있게 다양한 생성자를 지원
- 클래스를 확장하면서 똑같이 부 생성자를 정의할 수 있음
- 클래스에 주 생성자가 없다면 모든 부 생성자는 반드시 상위 클래스를 초기화하거나 다른 생성자에게 생성을 위임

### 4.2.3 인터페이스에 선언된 프로퍼티 구현
- 인터페이스에 추상 프로퍼티 선언을 넣을 수 있음

```kotlin
interface User {
    val nickname: String
}

class PrivateUser(override val nickname: String) : User3    // 주 생성자에 있는 프로퍼티

class SubscribingUser(val email: String) : User3 {
    override val nickname: String
        get() = email.substringBefore('@')           // 커스텀 게터
}

class FacebookUser(val accountId: Int) : User3 {
    override val nickname = getFacebookName(accountId)

    private fun getFacebookName(accountId: Int): String {
        TODO("Not yet implemented")
    }
}
```
- `SubscribingUser` 의 `nickname` 은 매번 호출될 때마다 `substringBefore` 를 호출해 계산
- `FacebookUser` 의 `nickname` 은 객체 초기화 시, 필드에 저장했다가 불러오는 방식

### 4.2.4 게터와 세터에서 뒷받침하는 필드에 접근
```kotlin
class User4(val name: String) {
    var address: String = "unspecified"
        set(value: String) {
            field = value
        }
}
```
- 코틀린에서 값을 바꿀때는 `user.address = "new value"` 처럼 필드 설정 구문을 사용
    - 내부적으로 `address` 의 세터 호출
- 접근자의 본문에서는 `field` 라는 특별한 식별자를 통해 뒷받침하는 필드에 접근 가능
- 게터에서는 `field` 값을 읽을 수만 있고, 세터에서는 `field` 값을 읽거나 쓸 수 있음

### 4.2.5 접근자의 가시성 변경
```kotlin
class LenghtCounter {
    var counter: Int = 0
        private set         // 이 클래스 밖에서 이 프로퍼티의 값을 바꿀 수 없음

    fun addWord(word: String) {
        counter += word.length
    }
}
```



## 4.3 컴파일러가 생성한 메소드: 데이터 클래스와 클래스 위임

### 4.3.1 모든 클래스가 정의해야 하는 메소드

### 4.3.2 데이터 클래스: 모든 클래스가 정의해야 하는 메소드 자동 생성

### 4.3.3 클래스 위임: by 키워드 사용


## 4.4 object 키워드: 클래스 선언과 인스턴스 생성

### 4.4.1 객체 선언: 싱글턴을 쉽게 만들기

### 4.4.2 동반 객체: 팩토리 메소드와 정적 멤버가 들어갈 장소

### 4.4.3 동반 객체를 일반 객체처럼 사용

### 4.4.4 객체 시: 무명 내부 클래스를 다른 방식으로 작성


## 4.5 요약



