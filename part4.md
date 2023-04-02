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

### 4.1.3 가시성 변경자: 기본적으로 공개

### 4.1.4 내부 클래스와 중첩된 클래스: 기본적으로 중첩 클래스

### 4.1.5 봉인된 클래스: 클래스 계층 정의 시 계층 확장 제한



## 4.2 뻔하지 않은 생성자와 프로퍼티를 갖는 클래스 선언

### 4.2.1 클래스 초기화: 주 생성자와 초기화 블록

### 4.2.2 부 생성자: 상위 클래스를 다른 방식으로 초기화

### 4.2.3 인터페이스에 선언된 프로퍼티 구현

### 4.2.4 게터와 세터에서 뒷받침하는 필드에 접근

### 4.2.5 접근자의 가시성 변경

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



