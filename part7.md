# 연산자 오버로딩과 기타 관례
- 자바에서는 기능이 클래스와 연관 (AutoCloseable 등)
- 코틀린은 특정 함수와 연관
  - 어떤 기능과 미리 정해진 이름의 함수를 연결해주는 기법을 관례(convention) 라고 부름 

## 7.1 산술 연산자 오버로딩
- 자바에서는 원시 타입에 대해서만 산술 연산자 사용 가능
- BigInteger 에서 add가 아닌 + 연산을 사용하고 싶다면?! (코틀린은 가능)

### 7.1.1 이항 산술 연산 오버로딩
```kotlin
data class Point(val x: Int, val y: Int) {
    operator fun plus(other: Point): Point {
        return Point(x + other.x, y + other.y)
    }
}

// 확장 함수 형태도 가능 (외부 라이브러리 등)
operator fun Point.plus(other: Point): Point {
    return Point(x + other.x, y + other.y)
}

>>> val p1 = Point(10, 20)
>>> val p2 = Point(30, 40)
>>> println(p1 + p2) // + 계산시 'plus' 함수가 호출됨
Point(x=40, y=60)
```
- 연산자를 오버로딩하는 함수 앞에는 꼭 `operator` 가 있어야 함 (관례를 따르는 함수임을 명확히 알 수 있다.)
  - `operator` 없으면 컴파일 시점에 에러남

- 오버로딩 가능한 이항 산술 연산자
  - ![image](https://github.com/simjunbo/kotlin/assets/7076334/2942c5c2-bde9-461c-a5fb-240fb4e3f8bf)


```kotlin
operator fun Point.times(scale: Double): Point {
    return Point((x * scale).toInt(), (y * scale).toInt())
}

>>> val p = Point(10, 20)
>>> println(p * 1.5)
Point(x=15, y=30)

>>> println(1.5 * p1) // 교환 법칙은 지원하지 않음. 별도 정의해야됨
```
- 두 피연산자의 타입이 다른 연산자 정의
- 연산자 함수의 반환 타입이 꼭 두 피연산자와 일치하지 않아도 됨


### 7.1.2 복합 대입 연산자 오버로딩
- 코틀린은 +=, -= 등과 같은 복합 대입 연산자도 지원

```kotlin
>>> var point = Point(1, 2)
>>> point += Point(3, 4) // point = point + Point(3,4)와 같다
>>> println(point)
Point(x=4, y=6)
```
- 변수가 변경 가능한 경우에만 복합 대입 연산자 사용 가능
- 경우에 따라 객체 대한 참조를 다른 참조로 바꾸기보다 원래 객체의 내부 상태를 변경하고 싶을 때 사용

```kotlin
data class Point(var x: Int, var y: Int) {

}

operator fun Point.plus(other: Point): Point {
    return Point(x + other.x, y + other.y)
}

operator fun Point.plusAssign(other: Point) {
    this.x += other.x
    this.y += other.y
}
```

- 복합 대입 연산자는 `minusAssign`, `timeAssign` 등의 이름 사용
- 이론적으로 +=를 plus와 plusAssign 둘다 컴파일 가능. 둘다 정의되어 있으면 아래와 같은 컴파일 에러 발생 (동시 정의 하지 마라)
  - ![image](https://github.com/simjunbo/kotlin/assets/7076334/4317ea59-3641-448b-bd5b-1f0985d3f9e5)

- 클래스가 불변이면 `plus`와 같이 새로운 값을 반환하는 연산만 사용. 빌더와 같이 변경 가능한 클래스라면 `plusAssign` 연산 제공


### 7.1.3 단항 연산자 오버로딩

```kotlin
operator fun Point.unaryMinus(): Point {
    return Point(-x, -y)
}

>>> val p = Point(10, 20)
>>> println(-p)
Point(x=-10, y=-20)
```
- 이항 연산자와 마찬가지로 미리 정해진 이름의 함수를 선언하면서 operator로 표시하면 됨
- 단항 연산자를 오버로딩하기 위해 사용하는 함수는 인자를 취하지 않는다.

- 오버로딩할 수 있는 단항 산술 연산자
  - ![image](https://github.com/simjunbo/kotlin/assets/7076334/523eb949-1c33-4245-88dc-4b7d64ed3236)


## 7.2 비교 연산자 오버로딩
- equals나 compareTo를 호출해야 하는 자바와 달리 코틀린에서는 == 비교 연산자를 직접 사용할 수 있어 비교 코드가 간결하며 이해하기 쉽다. (코틀린에서는 == 호출을 equals 메서드 호출로 컴파일함)

### 7.2.1 동등성 연산자: equals

```kotlin
// data class 사용 시, equals, hashCode, toString 제공
data class Point(var x: Int, var y: Int) {

}
```

- data class 사용 시 다음과 같이 자동 생성
  - 인스턴스 간 비교를 위한 equals
  - HashMap과 같은 해시 기반 컨테이너에서 키로 사용할 수 있는 hashCode
  - 클래스의 각 필드를 선언 순서대로 표시하는 문자열 표현을 만들어주는 toString

```kotlin
class Point(var x: Int, var y: Int) {
    override fun equals(obj: Any?): Boolean {
        if (obj === this) return true
        if (obj !is Point) return false
        return obj.x == x && obj.y == y
    }
}
```

- 다른 연산자 오버로딩 관례와 달리 equals는 Any에 정의된 메소드 이기 때문에 override 가 필요하다.
- Any의 equals에는 operator가 붙어있지만 그 메소드를 오버라이드하는 메소드 앞에는 operator 변경자를 붙이지 않아도 자동으로 적용됨
- 또한 Any에서 상속받은 equals가 확장 함수보다 우선순위가 높기 때문에 확장함수로 정의 불가

### 7.2.2 순서 연산자: compareTo
- 자바에서는 정렬등을 사용하기 위해 Comparable 인터페이스 구현해야됨
- 코틀린에서도 동일하게 Comparable 인터페이스 지원. 코틀린에서는 비교 연산자(<, >, <=, >=) 는 compareTo 호출로 컴파일됨
  - ex) p1 < p2 는 p1.compareTo(p2) < 0 과 같음

```kotlin
public interface Comparable<in T> {
    public operator fun compareTo(other: T): Int
}
```
- Comparable 의 compareTo도 operator 변경자가 붙어 있으므로 하위 클래스의 오버라이딩 함수에 operator를 붙일 필요 없음


## 7.3 컬렉션과 범위에 대해 쓸 수 있는 관례

### 7.3.1 인덱스로 원소에 접근: get과 set

### 7.3.2 in 관례

### 7.3.3 rangeTo 관례

### 7.3.4 for 루프를 위한 iterator 관례


## 7.4 구조 분해 선언과 component 함수

### 7.4.1 구조 분해 선언과 루프


## 7.5 프로퍼티 접근자 로직 재활용: 위임 프로퍼티

### 7.5.1 위임 프로퍼티 소개

### 7.5.2 위임 프로퍼티 사용: by lazy()를 사용한 프로퍼티 초기화 지연

### 7.5.3 위임 프로퍼티 구현

### 7.5.4 위임 프로퍼티 컴파일 규칙

### 7.5.5 프로퍼티 값을 맵에 저장

### 7.5.6 프레임워크에서 위임 프로퍼티 활용



